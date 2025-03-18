# `apsig.LDSignature`
## Overview
The `LDSignature` class provides methods for signing and verifying Linked Data signatures 1.0 using RSA keys. It utilizes the W3C's security vocabulary and JSON-LD normalization to ensure the integrity and authenticity of the signed documents.

### Methods

#### `__init__(self)`
Initializes an instance of the `LDSignature` class.

#### `sign(self, doc: dict, creator: str, private_key: rsa.RSAPrivateKey, options: dict = None, created: datetime.datetime = None) -> dict`
Signs the provided document using the specified RSA private key.

**Args:**
- `doc` (dict): The document to be signed.
- `creator` (str): The identifier of the creator of the document.
- `private_key` (rsa.RSAPrivateKey): The RSA private key used for signing.
- `options` (dict, optional): Additional signing options. Defaults to None.
- `created` (datetime.datetime, optional): The timestamp when the signature is created. Defaults to the current UTC time if not provided.

**Returns:**
- `dict`: The signed document containing the original data and the signature.

#### `verify(self, doc: dict, public_key: rsa.RSAPublicKey | str) -> bool`
Verifies the signature of the provided document against the given public key.

**Args:**
- `doc` (dict): The signed document to verify.
- `public_key` (rsa.RSAPublicKey | str): The RSA public key in PEM format or as a multibase-encoded string.
- `raise_on_fail` (bool): Whether to return an error on verification failure. Default is False.

**Returns:**
- `string` or `None`: On success, the keyId of the creator is returned; on failure, None is returned.

**Raises:**
The following will only occur if the `raise_on_fail` argument is passed as `True` when calling `LDSignature.verify`:

- `MissingSignature`: If the signature section is missing in the document.
- `UnknownSignature`: If the signature type is not recognized.
- `VerificationFailed`: If the signature verification fails.

## Example Usage

```python
from apsig import LDSignature
from cryptography.hazmat.primitives.asymmetric import rsa

private_key = rsa.generate_private_key(public_exponent=65537, key_size=2048)
public_key = private_key.public_key()


# Example document
document = {
    "actor": "http://example.com/alice",
    "signature": {}
}

# Generate the signature
ld_signature = LDSignature()
signed_document = ld_signature.sign(document, "http://example.com/alice", private_key)
print("Generated Document with Signature:")
print(json.dumps(signed_document, indent=2))

# Verify the signature
result = ld_signature.verify(signed_document, public_key)
print("Is the signature valid?", False if result is None else True)
```

## Notes
- The `LDSignature` class assumes the usage of RSA keys and follows the signature format defined by the W3C.

## Exceptions
The following will only occur if the `raise_on_fail` argument is passed as `True` when calling `LDSignature.verify`:

- **MissingSignature**: Raised when the signature section is not found in the document.
- **UnknownSignature**: Raised when the signature type is not recognized.
- **VerificationFailed**: Raised when the signature verification fails due to an invalid signature.