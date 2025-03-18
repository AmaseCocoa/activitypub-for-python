# `apsig.draft`
[draft-cavage-http-signatures-12](https://datatracker.ietf.org/doc/html/draft-cavage-http-signatures-12)に準拠したHTTP署名の実装。
## `apsig.draft.Verifier`
HTTP署名を検証するためのクラス。

`apsig.draft`から直接インポートできます: 
```python
from apsig.draft import Verifier
```
### 例
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
    `body`は`bytes`形式も許容しますが、検証が失敗する可能性があるため辞書型の場合はそのまま`dict`として渡すべきです。

| parameter    | type              | description                                                                                                                | 
| ------------ | ----------------- | -------------------------------------------------------------------------------------------------------------------------- | 
| `public_pem` | `str`             | Public key in PEM format or `cryptography.hazmat.primitives.asymmetric.rsa.RSAPublicKey` format used to verify signatures. | 
| `method`     | `str`             | The HTTP method (e.g., `GET`, `POST`).                                                                                     | 
| `url`        | `str`             | The URL of the request.                                                                                                    | 
| `headers`    | `dict`            | A dictionary of HTTP headers, including the signature and other relevant information.                                      | 
| `body`       | `bytes` or `dict` | The request body. Defaults to an empty byte string.                                                                        | 

### `apsig.draft.Verifier.verify`
署名を検証します。

| parameter       | type              | description                                                           | 
| --------------- | ----------------- | --------------------------------------------------------------------- | 
| `raise_on_fail` | `bool`            | Whether to return an error on verification failure. Default is False. | 

#### 例
```python
...
verifier = Verifier(public_key, method, url, headers, body)
result = verifier.verify()

print(result) # keyId or None (failed)
```
#### エラー

The following will only occur if the `raise_on_fail` argument is passed as `True` when calling `Verifier.verify`:

- **apsig.exceptions.MissingSignature**: Occurs when the Signature header is not present in the signed text.
- **apsig.exceptions.UnknownSignature**: Occurs when signed using an unsupported algorithm.
- **apsig.exceptions.VerificationFailed**: This occurs when the signature is invalid, the Digest does not match, the Date header value is more than a certain amount of time past the current time, etc.