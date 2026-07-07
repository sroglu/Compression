# PFound.Compression

Compression codecs for PFound (currently LZMA). Engine-free — `noEngineReferences`, no Unity types.

This module is the umbrella for compression codecs; today it provides a single clean-room `Lzma`
codec. Future codecs (e.g. zstd, gzip) slot in as sibling classes in the same `PFound.Compression`
namespace.

## Public API (`Lzma`, static)

- `byte[] Compress(byte[] data)` — encode to the classic `.lzma`/"alone" stream layout (packed
  props + little-endian dict size + little-endian uncompressed length + range-coded payload).
- `byte[] Decompress(byte[] data)` — decode to a freshly-allocated array the caller owns.
- `void DecompressInto(byte[] data, Stream destination)` / `void DecompressInto(Stream source,
  Stream destination)` — stream the decode straight to a destination, low-peak-memory under
  concurrency.
- `long ReadUncompressedLength(byte[] data)` — the uncompressed length declared in the header.

Decoders are pooled internally (each reuses its model state and output window), so concurrent
multi-blob decompression allocates neither the model arrays nor a per-blob output buffer. Public
methods throw `ArgumentNullException` on a null argument — the library boundary, not defensive
guarding.

## Setup / wiring

Pure static library — no scene, MonoBehaviour, DI registration, or lifecycle setup. Reference the
`PFound.Compression` assembly and call the static methods directly:

```csharp
byte[] packed = Lzma.Compress(payload);   // e.g. at build/content-authoring time
byte[] original = Lzma.Decompress(packed); // e.g. at runtime on a downloaded blob
```

`Decompress` inverts `Compress` from this same codec (the only property PFound needs — the editor
compresses, the runtime decompresses); the stream is not bit-identical to the 7-Zip SDK.

## Layout

- `Runtime/Lzma.cs` — the codec. Assembly `PFound.Compression` (`autoReferenced:false`,
  `noEngineReferences:true`).

Part of the PFound modular Unity foundation.
</content>
</invoke>
