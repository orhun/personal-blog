+++
title = "How hard upgrading a Rust JWT library could be?"
date = 2022-04-22

[taxonomies]
categories = ["Activities"]
+++

Recently one of my clients requested me to maintain their Rust project. It is a web server that is built with [Rocket](https://rocket.rs/) + [Diesel](http://diesel.rs/) and running stable for a couple of years now. Like any other Rust developer would do, the first thing that I checked was the outdated dependencies via `cargo-outdated`. The result was close to what I expected: most of the dependencies were out-of-date. However, among all those crates, [rust-jwt](https://github.com/mikkyang/rust-jwt) caught my eye. It was 12 minor versions behind!

<!-- more -->

```sh
Name                        Project  Compat  Latest   Kind    Platform
----                        -------  ------  ------   ----    --------
jwt                         0.4.0    ---     0.16.0   Normal  ---
```

Although this doesn't seem like a big problem, since it is not a major version or anything, I took this lightly and started working on updating the other dependencies. It is the JWT that everyone knows and loves after all, how hard updating this dependency would be?

Well, it turns out, it was pretty hard after in my case.

The version that the project used at the time was `0.4.0`. When I checked the changelog (release notes), there wasn't even an entry about it! Even worse, this version is not tagged on GitHub. The oldest version on GitHub was `0.5.0` and the release notes are the following:

> ### Update (very outdated) dependencies
> The only user facing changes are much needed updates to the crypto dependencies, courtesy of #10.
>
> Types from the previous crate, `rust-crypto` are replaced with types from various crates from [https://github.com/RustCrypto](https://github.com/RustCrypto).

Although this is a good improvement for the library itself, it surely didn't look too good for me. I have spent some time deciphering what these release notes mean and it eventually boils down to [rust-crypto](https://github.com/DaGenix/rust-crypto) crate being unmaintained since 2016.

- [RUSTSEC-2016-0005](https://rustsec.org/advisories/RUSTSEC-2016-0005.html): rust-crypto is unmaintained; switch to a modern alternative

So the maintainer of rust-jwt made the most expected thing by moving away from `rust-crypto` and migrating the cryptography-related operations to be handled by the actively maintained [RustCrypto](https://github.com/RustCrypto) project. I was sure I will benefit from this change in the security and performance aspect. 

One thing to note here, was that really a "minor" change in rust-jwt? Can I just bump the dependency and call it a day?

Well, I don't know. I thought to myself "Hey let me bump it to the latest version, it's all minor changes anyways". That's why I updated it to `0.16.0` and took a look at the previous changelog entries before attempting to compile the project.

```sh
0.6.0: New Token API

- Move all of the previous structs to the legacy module
- Introduce more idiomatic Header, Claims, and Token types
- Support more algorithms with optional OpenSSL support
- Convenience methods to just sign and verify claims
```

So it seemed like I will be dealing with 2 issues:

1. Side effects of "rust-crypto -> RustCrypto"
2. New API

### Use case

To demonstrate my use case of this library, I wrote this oversimplified [Rust script](https://github.com/fornwall/rust-script):

```rs
#!/usr/bin/env rust-script
//! ```cargo
//! [dependencies]
//! jwt = "0.4.0"
//! rust-crypto = "0.2"
//! ```

use crypto::sha2::Sha256;
use jwt::{Header, Registered, Token};

const DATA: &str = "420";
const SECRET_KEY: &str = "verysecretkey";

fn main() {
    let claims = Registered {
        sub: Some(DATA.to_string()),
        ..Default::default()
    };

    let header = Header::default();

    let token = Token::new(header, claims)
        .signed(SECRET_KEY.as_bytes(), Sha256::new())
        .unwrap();

    println!("{token}");

    let token = Token::<Header, Registered>::parse(&token).unwrap();
    if token.verify(SECRET_KEY.as_bytes(), Sha256::new()) {
        assert_eq!(Some(DATA.to_string()), token.claims.sub);
    } else {
        panic!("Token is not valid")
    }
}
```

After upgrading the dependencies, the script looks something like this:

```rs
#!/usr/bin/env rust-script
//! ```cargo
//! [dependencies]
//! hmac = "0.12.1"
//! jwt = "0.16.0"
//! sha2 = "0.10.2"
//! ```

use hmac::{Hmac, Mac};
use jwt::claims::RegisteredClaims;
use jwt::{Header, SignWithKey, Token, VerifyWithKey};
use sha2::Sha256;
use std::collections::BTreeMap;

const DATA: &str = "420";
const SECRET_KEY: &str = "verysecretkey";

fn main() {
    let secret_key = Hmac::<Sha256>::new_from_slice(SECRET_KEY.as_bytes()).unwrap();

    let claims = RegisteredClaims {
        subject: Some(DATA.to_string()),
        ..Default::default()
    };

    let header = Header::default();

    let token = Token::new(header, claims)
        .sign_with_key(&secret_key)
        .unwrap();

    println!("{}", token.as_str());

    match VerifyWithKey::<Token<Header, BTreeMap<String, String>, _>>::verify_with_key(
        token.as_str(),
        &secret_key,
    ) {
        Ok(token) => {
            assert_eq!(DATA.to_string(), token.claims()["sub"]);
        }
        Err(e) => {
            panic!("Token is not valid: {}", e)
        }
    }
}
```

Let's check the output of both scripts:

```sh
$ ./jwt-0.4.0.rs
eyJ0eXAiOiJKV1QiLCJraWQiOm51bGwsImFsZyI6IkhTMjU2In0.eyJpc3MiOm51bGwsInN1YiI6IjQyMCIsImF1ZCI6bnVsbCwiZXhwIjpudWxsLCJuYmYiOm51bGwsImlhdCI6bnVsbCwianRpIjpudWxsfQ./sN8Ur+b+38g4X2yQsIuhs4Z1dWjPW+7SHSFgmYa4xM

$ ./jwt-0.16.0.rs
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI0MjAifQ.wvPvTjXDF9tfOmB8ZjVh9Bosx5zw9M5SCpQ_NMI29OI
```

Hmm, they print different tokens and the second one is visibly shorter for some reason. This is probably due to the API changes in the library. 

> So what? Just upgrade the dependencies, it works.

Well, that's right. However, there is a bigger problem.

### Architecture

In the current architecture, JWT tokens are stored on the user device and used for authentication. On top of that, there is a "use without registration" feature of the app which means users can take a look at the app and use some of the features without logging in but still they will have a JWT token associated in case they want to register later on.

![Architecture](/jwt-server-client-architecture.png)

At the end of the day, changing the JWT decoding logic in an incompatible way would make the tokens of those "anonymous" users invalid thus they will be logged out and lose their data. On the other hand, normal users will also be logged out but a new JWT token will be generated on login. Anonymous users cannot log in because... well, they are anonymous.

At this point, I realized I was dealing with a dependency that will eventually affect **~100k** users. Simply bumping rust-jwt will discard all the existing JWT tokens and make the application unusable for the majority of the users. Some time passed and I put more thought into this topic and we came up with a solution with the help of my colleague:

1. Add an API endpoint for renewing the JWT token of a user.
2. Call this endpoint from the mobile application to renew the user token.
3. Hope that most of the users will update the app and have their tokens renewed.

Although this sounds like a feasible solution, it requires an unnecessary amount of time and work. Plus I had different priorities at the time so I postponed it for later, thinking that I will eventually feel less lazy to implement and coordinate this.

Unsurprisingly, it didn't happen.

### Inspection

I decided to debug this issue further and understand the root cause of this problem. Why would two different versions of the same library yield two completely different and incompatible tokens? There wasn't even a major change in the library!

Let's inspect the previously generated tokens with [jwt-cli](https://github.com/mike-engel/jwt-cli):

```sh
$ ./jwt-0.4.0.rs | jwt decode -

Token header
------------
{
  "typ": "JWT",
  "alg": "HS256"
}

Token claims
------------
{
  "aud": null,
  "exp": null,
  "iat": null,
  "iss": null,
  "jti": null,
  "nbf": null,
  "sub": "420"
}
```

```sh
$ ./jwt-0.16.0.rs | jwt decode -

Token header
------------
{
  "alg": "HS256"
}

Token claims
------------
{
  "sub": "420"
}
```

The newer version of rust-jwt strips the empty (null) fields from claims, that's why the generated token is different (and shorter than I expected). Also, `typ` field is missing from the header.

> You already know where this is going. I'm going to modify the rust-jwt library for my needs!

Let's solve the missing header field problem first by explicitly specifying `typ` while defining the header:

```rs
let header = Header {
    algorithm: jwt::AlgorithmType::Hs256,
    type_: Some(jwt::header::HeaderType::JsonWebToken),
    ..Default::default()
};
```

```sh
$ ./jwt-0.16.0.rs | jwt decode -

Token header
------------
{
  "typ": "JWT",
  "alg": "HS256"
}

Token claims
------------
{
  "sub": "420"
}
```

Nice! We have the correct header fields in the generated token. Also, why not update the [Default](https://doc.rust-lang.org/std/default/trait.Default.html) implementation of [Header](https://github.com/mikkyang/rust-jwt/blob/a09172fcbf667db7a982c787ff709c6d6294ef18/src/header.rs#L32) so that we can use it as it was before?

```diff
-#[derive(Default, Debug, PartialEq, Serialize, Deserialize)]
+#[derive(Debug, PartialEq, Serialize, Deserialize)]
 pub struct Header {
     #[serde(rename = "typ")]
     pub type_: Option<HeaderType>,
@@ -43,6 +43,17 @@ pub struct Header {
     pub content_type: Option<HeaderContentType>,
 }
 
+impl Default for Header {
+    fn default() -> Header {
+        Header {
+            type_: Some(HeaderType::JsonWebToken),
+            key_id: None,
+            algorithm: AlgorithmType::Hs256,
+            content_type: None,
+        }
+    }
+}
+
```

[https://github.com/orhun/rust-jwt/commit/a2433724a4ed4f1e028624968b6f0d4eb67c4734](https://github.com/orhun/rust-jwt/commit/a2433724a4ed4f1e028624968b6f0d4eb67c4734)

Ta-da!

```rs
let header = Header::default();
```

Next step, let's figure out why null fields do not exist in the token.

The answer is shrouded in the struct definition of [`RegisteredClaims`](https://github.com/mikkyang/rust-jwt/blob/a09172fcbf667db7a982c787ff709c6d6294ef18/src/claims.rs#L31):

```rs
#[derive(Clone, Debug, Default, PartialEq, Serialize, Deserialize)]
pub struct RegisteredClaims {
    #[serde(rename = "iss", skip_serializing_if = "Option::is_none")]
    pub issuer: Option<String>,

    #[serde(rename = "sub", skip_serializing_if = "Option::is_none")]
    pub subject: Option<String>,

    #[serde(rename = "aud", skip_serializing_if = "Option::is_none")]
    pub audience: Option<String>,

    #[serde(rename = "exp", skip_serializing_if = "Option::is_none")]
    pub expiration: Option<SecondsSinceEpoch>,

    #[serde(rename = "nbf", skip_serializing_if = "Option::is_none")]
    pub not_before: Option<SecondsSinceEpoch>,

    #[serde(rename = "iat", skip_serializing_if = "Option::is_none")]
    pub issued_at: Option<SecondsSinceEpoch>,

    #[serde(rename = "jti", skip_serializing_if = "Option::is_none")]
    pub json_web_token_id: Option<String>,
}
```

Bingo! [skip_serializing_if](https://serde.rs/field-attrs.html#skip_serializing_if) attribute in combination with "Option::is_none" causes `None` fields to be removed from the generated token. So let's just remove that attribute. 

```diff
 #[derive(Clone, Debug, Default, PartialEq, Serialize, Deserialize)]
 pub struct RegisteredClaims {
-    #[serde(rename = "iss", skip_serializing_if = "Option::is_none")]
+    #[serde(rename = "iss")]
     pub issuer: Option<String>,
 
-    #[serde(rename = "sub", skip_serializing_if = "Option::is_none")]
+    #[serde(rename = "sub")]
     pub subject: Option<String>,
 
-    #[serde(rename = "aud", skip_serializing_if = "Option::is_none")]
+    #[serde(rename = "aud")]
     pub audience: Option<String>,
 
-    #[serde(rename = "exp", skip_serializing_if = "Option::is_none")]
+    #[serde(rename = "exp")]
     pub expiration: Option<SecondsSinceEpoch>,
 
-    #[serde(rename = "nbf", skip_serializing_if = "Option::is_none")]
+    #[serde(rename = "nbf")]
     pub not_before: Option<SecondsSinceEpoch>,
 
-    #[serde(rename = "iat", skip_serializing_if = "Option::is_none")]
+    #[serde(rename = "iat")]
     pub issued_at: Option<SecondsSinceEpoch>,
 
-    #[serde(rename = "jti", skip_serializing_if = "Option::is_none")]
+    #[serde(rename = "jti")]
     pub json_web_token_id: Option<String>,
 }
```

[https://github.com/orhun/rust-jwt/commit/4ef2ff4768a6485281e1bd451dee502bfc185d0d](https://github.com/orhun/rust-jwt/commit/4ef2ff4768a6485281e1bd451dee502bfc185d0d)

That should be enough, right?

```sh
$ ./jwt-0.16.0.rs
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOm51bGwsInN1YiI6IjQyMCIsImF1ZCI6bnVsbCwiZXhwIjpudWxsLCJuYmYiOm51bGwsImlhdCI6bnVsbCwianRpIjpudWxsfQ.mXy5xf4an2Bfv6mmhABh9-Yfmqit2AeXZWahCPgrvr0

thread 'main' panicked at 'Token is not valid: invalid type: null, expected a string at line 1 column 11', jwt-0.16.0.rs:45:13
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Uhm... verification failed. It is because we allowed `Option` types by removing the serde attribute so we should update the generic parameter from `String` to `Option<String>` in our expected claims data in the script:

```diff
-    match VerifyWithKey::<Token<Header, BTreeMap<String, String>, _>>::verify_with_key(
+    match VerifyWithKey::<Token<Header, BTreeMap<String, Option<String>>, _>>::verify_with_key(
         token.as_str(),
         &secret_key,
     ) {
         Ok(token) => {
-            assert_eq!(DATA.to_string(), token.claims()["sub"]);
+            assert_eq!(Some(DATA.to_string()), token.claims()["sub"]);
         }
         Err(e) => {
             panic!("Token is not valid: {}", e)
```

```sh
$ ./jwt-0.16.0.rs | jwt decode -

Token header
------------
{
  "typ": "JWT",
  "alg": "HS256"
}

Token claims
------------
{
  "aud": null,
  "exp": null,
  "iat": null,
  "iss": null,
  "jti": null,
  "nbf": null,
  "sub": "420"
}
```

Good. We now have null fields in the claims. But do we have the same data in tokens generated by both scripts?

```sh
$ diff <(./jwt-0.4.0.rs | jwt decode -) <(./jwt-0.16.0.rs | jwt decode -) && echo "decode result is the same"
decode result is the same
```

Yay! It is the expected output. Let's check the tokens too if they are the same:

```sh
$ ./jwt-0.4.0.rs
eyJ0eXAiOiJKV1QiLCJraWQiOm51bGwsImFsZyI6IkhTMjU2In0.eyJpc3MiOm51bGwsInN1YiI6IjQyMCIsImF1ZCI6bnVsbCwiZXhwIjpudWxsLCJuYmYiOm51bGwsImlhdCI6bnVsbCwianRpIjpudWxsfQ./sN8Ur+b+38g4X2yQsIuhs4Z1dWjPW+7SHSFgmYa4xM

$ ./jwt-0.16.0.rs
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOm51bGwsInN1YiI6IjQyMCIsImF1ZCI6bnVsbCwiZXhwIjpudWxsLCJuYmYiOm51bGwsImlhdCI6bnVsbCwianRpIjpudWxsfQ.mXy5xf4an2Bfv6mmhABh9-Yfmqit2AeXZWahCPgrvr0
```

Uh-oh... Token data is exactly the same according to `jwt-cli` but tokens are different. What is going on?

> WE'VE BEEN BAMBOOZLED.

It turns out `jwt-cli` was wrong /o\\

It uses [jsonwebtoken](https://github.com/Keats/jsonwebtoken) under the hood for decoding the tokens and this library has the same serde attribute:

```rs
#[derive(Debug, Clone, PartialEq, Serialize, Deserialize, Hash)]
pub struct Header {
    /// The type of JWS: it can only be "JWT" here
    ///
    /// Defined in [RFC7515#4.1.9](https://tools.ietf.org/html/rfc7515#section-4.1.9).
    #[serde(skip_serializing_if = "Option::is_none")]
    pub typ: Option<String>,
    /// <strip>
```

See [jsonwebtoken::Header](https://github.com/Keats/jsonwebtoken/blob/e869bff62fddb039ea20c60813549d30aa00e7f2/src/header.rs#L13)

So that's why null fields are not shown in the output. 🤦🏼‍♂️

Later on I used [jwt.io](https://jwt.io/) to compare the data of the both tokens and it revealed the following result:

```diff
 {
-  "alg": "HS256",
-  "typ": "JWT"
+  "typ": "JWT",
+  "kid": null,
+  "alg": "HS256"
 }
```

So only `kid` is missing from the header? Alright, easy:

```diff
 pub struct Header {
     #[serde(rename = "alg")]
     pub algorithm: AlgorithmType,
 
-    #[serde(rename = "kid", skip_serializing_if = "Option::is_none")]
+    #[serde(rename = "kid")]
     pub key_id: Option<String>,
 
     #[serde(rename = "typ", skip_serializing_if = "Option::is_none")]
```

[https://github.com/orhun/rust-jwt/commit/b8c4b78d357fa20f54afc4c1ddcc0cb29479e1a4](https://github.com/orhun/rust-jwt/commit/b8c4b78d357fa20f54afc4c1ddcc0cb29479e1a4)

How about now?

```diff
 {
-  "alg": "HS256",
+  "typ": "JWT",
   "kid": null,
-  "typ": "JWT"
+  "alg": "HS256"
 }
```

Oooh, they need to be in the same order as well. Let's reorder the struct fields:

```diff
 pub struct Header {
-    #[serde(rename = "alg")]
-    pub algorithm: AlgorithmType,
+    #[serde(rename = "typ", skip_serializing_if = "Option::is_none")]
+    pub type_: Option<HeaderType>,
 
     #[serde(rename = "kid")]
     pub key_id: Option<String>,
 
-    #[serde(rename = "typ", skip_serializing_if = "Option::is_none")]
-    pub type_: Option<HeaderType>,
+    #[serde(rename = "alg")]
+    pub algorithm: AlgorithmType,
 
     #[serde(rename = "cty", skip_serializing_if = "Option::is_none")]
     pub content_type: Option<HeaderContentType>,
```

[https://github.com/orhun/rust-jwt/commit/c6d2fb9cc635ee0beafdeaebdac68c5d9e0d7c23](https://github.com/orhun/rust-jwt/commit/c6d2fb9cc635ee0beafdeaebdac68c5d9e0d7c23)

That should be fine now. Please god!

```sh
$ diff <(./jwt-0.4.0.rs | jwt decode -) <(./jwt-0.16.0.rs | jwt decode -)

$ diff <(./jwt-0.4.0.rs) <(./jwt-0.16.0.rs)
1c1
< eyJ0eXAiOiJKV1QiLCJraWQiOm51bGwsImFsZyI6IkhTMjU2In0.eyJpc3MiOm51bGwsInN1YiI6IjQyMCIsImF1ZCI6bnVsbCwiZXhwIjpudWxsLCJuYmYiOm51bGwsImlhdCI6bnVsbCwianRpIjpudWxsfQ./sN8Ur+b+38g4X2yQsIuhs4Z1dWjPW+7SHSFgmYa4xM
---
> eyJ0eXAiOiJKV1QiLCJraWQiOm51bGwsImFsZyI6IkhTMjU2In0.eyJpc3MiOm51bGwsInN1YiI6IjQyMCIsImF1ZCI6bnVsbCwiZXhwIjpudWxsLCJuYmYiOm51bGwsImlhdCI6bnVsbCwianRpIjpudWxsfQ._sN8Ur-b-38g4X2yQsIuhs4Z1dWjPW-7SHSFgmYa4xM
```

WHAT!? OH... WAIT... They look so similar. Only the signature part is slightly different:

```sh
/sN8Ur+b+38g4X2yQsIuhs4Z1dWjPW+7SHSFgmYa4xM
_sN8Ur-b-38g4X2yQsIuhs4Z1dWjPW-7SHSFgmYa4xM
```

(`/` instead of `_` & `+` instead of `-`)

Well, it turns out they are actually the same token. According to the warning on jwt.io, the difference is due to the first token not being encoded correctly:

> Warning: Looks like your JWT signature is not encoded correctly using base64url ([https://tools.ietf.org/html/rfc4648#section-5](https://tools.ietf.org/html/rfc4648#section-5)).
> 
> Note that padding ("=") must be omitted as per [https://tools.ietf.org/html/rfc7515#section-2](https://tools.ietf.org/html/rfc7515#section-2)

Just to be safe, I decided to replace the invalid characters in the signature before verifying:

```diff
-        if key.verify(header_str, claims_str, signature_str)? {
+        let signature_str = signature_str.replace('+', "-").replace('/', "_");
+        if key.verify(header_str, claims_str, &signature_str)? {
             Ok(Token {
                 header: self.header,
                 claims: self.claims,
```

[https://github.com/orhun/rust-jwt/commit/3a47cae2b5d2a12a46548c07646305f1df0e1253](https://github.com/orhun/rust-jwt/commit/3a47cae2b5d2a12a46548c07646305f1df0e1253)

And then I tested it with different tokens and had no issues, meaning that this -_slightly modified_- version of rust-jwt is used in production now! 🎉

<details>
<summary>The final version of the script</summary>

```rs
#!/usr/bin/env rust-script
//! ```cargo
//! [dependencies]
//! hmac = "0.12.1"
//! jwt = { version="0.16.0", git="https://github.com/orhun/rust-jwt"  }
//! sha2 = "0.10.2"
//! ```

use hmac::{Hmac, Mac};
use jwt::claims::RegisteredClaims;
use jwt::{Header, SignWithKey, Token, VerifyWithKey};
use sha2::Sha256;
use std::collections::BTreeMap;

const DATA: &str = "420";
const SECRET_KEY: &str = "verysecretkey";

fn main() {
    let secret_key = Hmac::<Sha256>::new_from_slice(SECRET_KEY.as_bytes()).unwrap();

    let claims = RegisteredClaims {
        subject: Some(DATA.to_string()),
        ..RegisteredClaims::default()
    };

    let header = Header::default();

    let token = Token::new(header, claims)
        .sign_with_key(&secret_key)
        .unwrap();

    println!("{}", token.as_str());

    match VerifyWithKey::<Token<Header, BTreeMap<String, Option<String>>, _>>::verify_with_key(
        token.as_str(),
        &secret_key,
    ) {
        Ok(token) => {
            assert_eq!(Some(DATA.to_string()), token.claims()["sub"]);
        }
        Err(e) => {
            panic!("Token is not valid: {}", e)
        }
    }
}
```

</details>

I pushed the changes in the rust-jwt library to my fork in case anyone hits this incredibly specific issue: [**https://github.com/orhun/rust-jwt**](https://github.com/orhun/rust-jwt) 

Thanks for coming down with me to this rabbit hole! 🐇

(Special thanks to [Tim Heide](https://timhei.de) for bringing this issue to my attention.)

~ cya!
