# Compression

Compression codecs for PFound (currently LZMA). Engine-free.

This module is the umbrella for compression codecs; today it provides a single
clean-room `Lzma` codec (`PFound.Compression.Lzma`). Future codecs (e.g. zstd,
gzip) slot in as sibling classes in the same namespace.

Part of the PFound modular Unity foundation.
