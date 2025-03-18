# `apsig.draft`
An implementation of HTTP signatures compliant with the [draft-cavage-http-signatures-12](https://datatracker.ietf.org/doc/html/draft-cavage-http-signatures-12)
## `apsig.draft.Signer`
Class for signing HTTP signatures.

You can import directly from `apsig.draft`:
```python
from apsig.draft import Signer
```
### Example
```python
import json

from apsig.draft import Signer
from cryptography.hazmat.primitives.asymmetric import rsa

private_key = rsa.generate_private_key(public_exponent=65537, key_size=2048)

# HTTP Request Details (Here we use dummy, actually copying this code will not work)
method = "POST"
url = "https://example.com/api"
headers = {
    "Content-Type": "application/json",
    "Date": "Wed, 21 Oct 2015 07:28:00 GMT",
}
body = {"key": "value"}
signer = Signer(
    headers=headers,
    private_key=private_key,
    method=method,
    url=url,
    key_id="https://example.com/users/johndoe#main-key",
    body=body,
)
```

| parameter    | type              | description | 
| ------------ | ----------------- | ------------------------- | 
| `private_key` | `cryptography.hazmat.primitives.asymmetric.rsa.RSAPrivateKey` | The RSA private key used to sign the request. | 
| `method`     | `str`             | The HTTP method (e.g., `GET`, `POST`). | 
| `url`        | `str`             | The URL of the request. | 
| `headers`    | `dict`            | A dictionary of HTTP headers that will be signed. | 
| `key_id`       | `str` | The key identifier to include in the signature header. | 
| `body`       | `bytes` or `dict` | The request body. Defaults to an empty byte string. | 

### `apsig.draft.Signer.sign`
Create HTTP Signature. 

#### Example
```python
...
signed_headers = signer.sign()

print(signed_headers) # signed headers
```
## `apsig.draft.Verifier`
Class for verifying HTTP signatures.

You can import directly from `apsig.draft`:
```python
from apsig.draft import Verifier
```
### Example
```python
import json

from apsig.draft import Verifier

public_key = """
-----BEGIN PUBLIC KEY-----
... (RSA PublicKey Here)
-----END PUBLIC KEY-----
""" # or cryptography.hazmat.primitives.asymmetric.rsa.RSAPublicKey

# HTTP Request Details (Here we use dummy, actually copying this code will not work)
method = "POST"
url = "https://example.com/api"
headers = {
    "Content-Type": "application/json",
    "Date": "Wed, 21 Oct 2015 07:28:00 GMT",
    "signature": 'keyId="my-key-id",algorithm="rsa-sha256",headers="(request-target) Content-Type Date",signature="..."',
    "digest": "SHA-256=...",
}
body = {"key": "value"}
verifier = Verifier(public_key, method, url, headers, body)
```
!!! warning
    The `body` is also acceptable in `bytes` format, but should be passed as `dict` for dictionary types since verification may fail.

| parameter    | type              | description | 
| ------------ | ----------------- | -------------------------------------------------------------------------------------------------------------------------- | 
| `public_pem` | `str`             | Public key in PEM format or `cryptography.hazmat.primitives.asymmetric.rsa.RSAPublicKey` format used to verify signatures. | 
| `method`     | `str`             | The HTTP method (e.g., `GET`, `POST`).                                                                                     | 
| `url`        | `str`             | The URL of the request.                                                                                                    | 
| `headers`    | `dict`            | A dictionary of HTTP headers, including the signature and other relevant information.                                      | 
| `body`       | `bytes` or `dict` | The request body. Defaults to an empty byte string.                                                                        | 

### `apsig.draft.Verifier.verify`
Verify signatures. 

| parameter       | type              | description                                                           | 
| --------------- | ----------------- | --------------------------------------------------------------------- | 
| `raise_on_fail` | `bool`            | Whether to return an error on verification failure. Default is False. | 

#### Example
```python
...
result = verifier.verify()

print(result) # keyId or None (failed)
```
#### Errors

The following will only occur if the `raise_on_fail` argument is passed as `True` when calling `Verifier.verify`:

- **apsig.exceptions.MissingSignature**: Occurs when the Signature header is not present in the signed text.
- **apsig.exceptions.UnknownSignature**: Occurs when signed using an unsupported algorithm.
- **apsig.exceptions.VerificationFailed**: This occurs when the signature is invalid, the Digest does not match, the Date header value is more than a certain amount of time past the current time, etc.