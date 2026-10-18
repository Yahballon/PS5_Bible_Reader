# Native Piper engineering notes

The v0.2.1 voice backend runs a **Piper VITS model inside PS5 native C++**
using a PS5-cross-compiled ONNX Runtime 1.18.2 CPU backend. It does **not**
require network access during playback.

## Pipeline

1. Resolve the chosen offline Bible translation and selected verse.
2. Convert verse text into eSpeak phonemes for the active locale.
3. Map IPA phonemes using the selected model's generated phoneme IDs.
4. Run VITS inference through the ONNX Runtime C API.
5. Convert the model's float waveform into 16-bit PCM and queue audio via SDL2.
6. Update the karaoke mark using word-event timing rescaled to Piper duration.
7. Advance to the next verse until interrupted.

**Caveat:** native word timing is approximate; Piper does not output exact per-word
timestamps. Speech generation is synchronous per verse. If the native model
fails to initialize or synthesize, eSpeak remains available, and German
Genesis 1 has an additional prerecorded neural backup.

## Models and data

Model downloads/hashes and upstream voice credits are maintained in
[native_piper/fetch_multilingual_voices.sh](../native_piper/fetch_multilingual_voices.sh),
[tools/generate_piper_preview.py](../tools/generate_piper_preview.py) and
[build.yml](../.github/workflows/build.yml).

Audio and translation data are generated or fetched **at build time** and
bundled into the installer; the repository contains the source and generation
scripts rather than redistributing large ONNX files in Git.

## Build reproducibility

`native_piper/probe_native_onnx.sh` configures PS5 FreeBSD-compatible
Clang cross-compilation. `native_piper/patch_ps5_mlas.py` ensures that
AVX-512 / VNNI helpers are built only for appropriate kernel objects.
The runtime still must dispatch instructions according to actual CPU support.

The release package includes its own model cards and relevant dependency
licences. Inspect the resulting binary on actual PS5 hardware; cross-compile
success by itself is not a hardware runtime guarantee.
