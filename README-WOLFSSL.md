# libgcrypt wolfCrypt Port

This project implements a bridge between libgcrypt and wolfCrypt, allowing libgcrypt users to leverage wolfCrypt's implementations while maintaining the familiar libgcrypt API.

## Current Status

The port currently implements:
- AES cipher operations in CBC and GCM modes
- Support for 128, 192, and 256-bit keys
- Full AEAD support for GCM mode
- Transparent fallback to native libgcrypt implementation

## Implementation Details

### Usage
Applications can request wolfCrypt implementations by setting the `GCRY_CIPHER_WOLFSSL` flag when opening a cipher handle:
```c
gcry_cipher_open(&handle, GCRY_CIPHER_AES256, GCRY_CIPHER_MODE_GCM, GCRY_CIPHER_WOLFSSL);
```

### Architecture
The integration is implemented as a wrapper layer that:
1. Checks for wolfCrypt compatibility first
2. Falls back to native libgcrypt implementation if:
   - wolfCrypt is not available
   - The requested operation is not supported by wolfCrypt
   - The `GCRY_CIPHER_WOLFSSL` flag is not set

### Known Limitations
- GCM mode only supports 12-byte IVs (wolfCrypt restriction)
- Some cipher modes are not yet implemented
- Some control operations are still pending implementation

## Building

The port adds wolfSSL detection to the configure script. To build with wolfCrypt support:

```bash
./configure --with-wolfssl[=DIR]
make
```

If no directory is specified, the system wolfSSL installation will be used.

## Future Work

Areas that need attention:
1. Implementation of additional cipher modes
2. Support for remaining control operations
3. Additional test coverage
4. Performance benchmarking and optimization
5. Implementation of other algorithms beyond AES

## Testing

The test suite has been modified to accommodate wolfCrypt's limitations while maintaining compatibility with existing test vectors where possible. Run the test suite with:

```bash
make check
```

## Contributing

When extending this port:
1. Maintain the layered architecture approach
2. Preserve FIPS compliance checks
3. Add appropriate debug logging for new features
4. Update test vectors and skip incompatible ones
5. Ensure proper cleanup and memory management
6. Document any wolfCrypt-specific limitations

## Implementation Notes

The core integration is primarily in these files:
- `src/visibility.c`: Main API entry points
- `cipher/cipher.c`: Core cipher operations
- `src/gcrypt-int.h`: Internal API definitions
- `configure.ac`: Build system integration

Debug logging can be enabled for development by defining `DEBUG_WOLFSSL`.