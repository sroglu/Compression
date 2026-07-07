# PFound.Compression

Compression codecs for PFound. Engine-free (`noEngineReferences`, no Unity types), pure static
library. Today it ships a single clean-room `Lzma` codec; future codecs (zstd, gzip) slot in as
sibling classes in the same `PFound.Compression` namespace.

## Quick reference

Reference the `PFound.Compression` assembly and call the static methods directly — no scene,
MonoBehaviour, DI registration, or lifecycle setup:

```csharp
byte[] packed   = Lzma.Compress(payload);   // e.g. at build/content-authoring time
byte[] original = Lzma.Decompress(packed);  // e.g. at runtime on a downloaded blob
```

`Decompress` inverts `Compress` from this same codec (the only property PFound needs — the editor
compresses, the runtime decompresses). Public methods throw `ArgumentNullException` on a null
argument — the library boundary, not defensive guarding.

## Public API (`Lzma`, static)

- `byte[] Compress(byte[] data)` — encode to the classic `.lzma`/"alone" stream layout (packed
  props + little-endian dict size + little-endian uncompressed length + range-coded payload).
- `byte[] Decompress(byte[] data)` — decode to a freshly-allocated array the caller owns.
- `void DecompressInto(byte[] data, Stream destination)` — decode in-memory bytes straight to a
  destination stream.
- `void DecompressInto(Stream source, Stream destination)` — stream both ends; the runtime hot
  path, kept low-peak-memory and allocation-light under concurrency.
- `long ReadUncompressedLength(byte[] data)` — the uncompressed length declared in the header.

Decoders are pooled internally (each reuses its model state and a `DictSize` output window), so
concurrent multi-blob decompression allocates neither the model arrays nor a per-blob output
buffer.

## Dependencies

None — `PFound.Compression` has an empty `references` list, `autoReferenced:false`,
`noEngineReferences:true`.

## Limitations

- Clean-room implementation of the LZMA1 algorithm from the published spec — NOT a port of the
  7-Zip SDK, and NOT bit-identical to it. The encoder emits literals and simple matches only (it
  never chooses rep matches), so streams are smaller-than-LZ4 but larger than a full 7-Zip encode.
- Fixed codec parameters: `lc=3, lp=0, pb=2`, 4 MB dictionary window.

## Docs

Single-file module — this README is the full reference. Codec source: `Runtime/Lzma.cs`
(assembly `PFound.Compression`). Part of the PFound modular Unity foundation.
