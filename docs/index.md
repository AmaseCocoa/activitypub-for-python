# Welcome

ActivityPub for Python is a set of libraries for implementing the ActivityPub protocol in Python. This facilitates the development of distributed social network applications.

## Libraries
### apkit
`apkit` is a toolkit that makes it easier to create implementations using apsig, and apmodel.
#### features
- Integration with popular web frameworks (e.g. Starlette/FastAPI)
### apsig
The `apsig` is a set of signatures commonly used among ActivityPub implementations.

RsaSignature2017 was ported from Takahē.
#### features
- [FEP-8b32](https://codeberg.org/fediverse/fep/src/branch/main/fep/8b32/fep-8b32.md) Support
- [draft-cavage-http-signatures-12](https://datatracker.ietf.org/doc/html/draft-cavage-http-signatures-12) Support
- [RsaSignature2017](https://docs.joinmastodon.org/spec/security/#ld) Support
- [RFC9421](https://datatracker.ietf.org/doc/html/rfc9421) (in the future)
### apmodel
apmodel is a set of model implementations often used in nodeinfo 2.0/2.1 and ActivityStreams 2.0 and ActivityPub.
#### features
- nodeinfo support
- Automatically convert json to a suitable model