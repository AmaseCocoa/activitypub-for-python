# `apsig.proof`
## Overview
The `ProofSigner` and `ProofVerifier` classes implement the functionality for creating and verifying Object Integrity Proofs as described in [FEP-8b32](https://codeberg.org/fediverse/fep/src/branch/main/fep/8b32/fep-8b32.md).

## Class: `ProofSigner`
You can import directly from `apsig`:
```python
from apsig import ProofSigner
```
### Initialization

#### `__init__(self, private_key: ed25519.Ed25519PrivateKey | str)`
Initializes the `ProofSigner` with a private key, which can be provided either as an Ed25519 private key object or as a multibase-encoded string.

**Args:**
- `private_key` (ed25519.Ed25519PrivateKey | str): The Ed25519 private key as an object or a multibase-encoded string.

**Raises:**
- `TypeError`: If the provided private key is not of type Ed25519.

### Methods

#### `sign_data(self, hash_data: bytes) -> bytes`
Signs the provided hash data using the private key.

**Args:**
- `hash_data` (bytes): The data to be signed.

**Returns:**
- `bytes`: The generated signature.

#### `canonicalize(self, document: dict) -> str`
Canonicalizes the provided document using JSON Canonicalization Scheme (JCS).

**Args:**
- `document` (dict): The document to be canonicalized.

**Returns:**
- `str`: The canonicalized representation of the document.

#### `transform(self, unsecured_document: dict, options: dict) -> str`
Transforms the unsecured document based on the provided options.

**Args:**
- `unsecured_document` (dict): The document that needs to be transformed.
- `options` (dict): Options that dictate the transformation process.

**Returns:**
- `str`: The transformed document.

**Raises:**
- `ValueError`: If the options do not specify the correct type or cryptosuite.

#### `hashing(self, transformed_document: str, canonical_proof_config: str) -> bytes`
Generates a hash for the transformed document and the canonical proof configuration.

**Args:**
- `transformed_document` (str): The transformed document.
- `canonical_proof_config` (str): The canonical proof configuration.

**Returns:**
- `bytes`: The concatenated hash of both the transformed document and the proof configuration.

#### `create_proof(self, unsecured_document: dict, options: dict) -> dict`
Creates a proof for the unsecured document using the specified options.

**Args:**
- `unsecured_document` (dict): The document for which the proof is created.
- `options` (dict): Options that define how the proof is structured.

**Returns:**
- `dict`: The proof object containing the proof value and other relevant information.

#### `sign(self, unsecured_document: dict, options: dict) -> dict`
Signs the unsecured document by creating a proof and returning the signed document.

**Args:**
- `unsecured_document` (dict): The document to be signed.
- `options` (dict): Options that define the signing process.

**Returns:**
- `dict`: The signed document, including the proof.

## Example Usage

```python
from apsig import ProofSigner
from cryptography.hazmat.primitives.asymmetric import ed25519

private_key = ed25519.Ed25519PrivateKey.generate()
public_key = private_key.public_key()

# Create a ProofSigner instance with a private key
proof_signer = ProofSigner(private_key)

# Example unsecured document
unsecured_document = {
    "data": "This is a sample document."
}

# Options for the proof
options = {
    "type": "DataIntegrityProof",
    "cryptosuite": "eddsa-jcs-2022",
}

# Sign the unsecured document
signed_document = proof_signer.sign(unsecured_document, options)
print("Signed Document with Proof:")
print(signed_document)
```

## Notes
- apsig only supports the use of ed25519 private keys for signing.

## Exceptions
- **TypeError**: Raised when the provided private key is not of type Ed25519.
- **ValueError**: Raised when the provided options for transformation are invalid.

## Class: `ProofVerifier`
`apsig`から直接インポートできます: 
```python
from apsig import ProofVerifier
```

### Initialization

#### `__init__(self, public_key: ed25519.Ed25519PublicKey | str)`
Initializes the `ProofVerifier` with a public key, which can be provided either as an Ed25519 public key object or as a multibase-encoded string.

**Args:**
- `public_key` (ed25519.Ed25519PublicKey | str): The Ed25519 public key as an object or a multibase-encoded string.

**Raises:**
- `TypeError`: If the provided public key is not of type Ed25519.

### Methods

#### `verify_signature(self, signature: bytes, hash_data: bytes) -> None`
Verifies the provided signature against the given hash data using the public key.

**Args:**
- `signature` (bytes): The signature to be verified.
- `hash_data` (bytes): The hashed data to verify against.

#### `canonicalize(self, document: dict) -> str`
Canonicalizes the provided document using JSON Canonicalization Scheme (JCS).

**Args:**
- `document` (dict): The document to be canonicalized.

**Returns:**
- `str`: The canonicalized representation of the document.

#### `transform(self, unsecured_document: dict, options: dict) -> str`
Transforms the unsecured document based on the provided options.

**Args:**
- `unsecured_document` (dict): The document that needs to be transformed.
- `options` (dict): Options that dictate the transformation process.

**Returns:**
- `str`: The transformed document.

**Raises:**
- `ValueError`: If the options do not specify the correct type or cryptosuite.

#### `hashing(self, transformed_document: str, canonical_proof_config: str) -> bytes`
Generates a hash for the transformed document and the canonical proof configuration.

**Args:**
- `transformed_document` (str): The transformed document.
- `canonical_proof_config` (str): The canonical proof configuration.

**Returns:**
- `bytes`: The concatenated hash of both the transformed document and the proof configuration.

#### `verify_proof(self, secured_document: dict) -> dict`
Verifies the proof contained in the secured document.

**Args:**
- `secured_document` (dict): The document containing the proof to be verified.

**Returns:**
- `dict`: A dictionary containing:
  - `bool`: `verified`: Indicates whether the proof verification was successful.
  - `dict`: `verifiedDocument`: The unsecured document if verification was successful, otherwise `None`.

**Raises:**
- `ValueError`: If the proof is not found in the document.

#### `verify(self, secured_document: dict) -> dict`
An alias for the `verify_proof` method.

**Args:**
- `secured_document` (dict): The document containing the proof to be verified.
- `raise_on_fail` (bool): Whether to return an error on verification failure. Default is False.

**Returns:**
- `dict`: The result of the proof verification.

## Example Usage

```python
from apsig import ProofSigner
from cryptography.hazmat.primitives.asymmetric import ed25519

proof_signer = ProofVerifier(public_key)

secured_document = {
    "data": "This is a sample document.",
    "proof": {
        "proofValue": "base58btc_encoded_signature",
        "type": "DataIntegrityProof",
        "cryptosuite": "eddsa-jcs-2022",
    }
} # not working this (invalid proofValue)

# Verify the proof
verification_result = proof_verifier.verify(secured_document)
print("Verification Result:", verification_result) # If this code is executed as is, it is expected to return None (failure).
```

## Exceptions
- **TypeError**: Raised when the provided public key is not of type Ed25519.

The following will only occur if the `raise_on_fail` argument is passed as `True` when calling `ProofVerifier.verify`:

- **ValueError**: Raised when the proof is not found or when the options for transformation are invalid.
- **apsig.exceptions.UnknownSignature**: Occurs when an invalid signature is passed.
- **apsig.exceptions.VerificationFailed**: Returned when verification fails due to internal errors, etc.