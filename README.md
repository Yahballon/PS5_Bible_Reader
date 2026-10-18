<div align="center">

![Bible Reader — offline Bible and neural voice on PS5](assets/banner.svg)

# Bible Reader

### A complete offline Bible and audiobook-style reader for PS5 homebrew

**by YAHBALLON · v0.2.1 · PPSA99793**

[![Latest release](https://img.shields.io/github/v/release/Yahballon/PS5_Bible_Reader?style=flat-square&label=Release)](https://github.com/Yahballon/PS5_Bible_Reader/releases/latest)
[![Native PS5 build](https://img.shields.io/github/actions/workflow/status/Yahballon/PS5_Bible_Reader/build.yml?style=flat-square&label=PS5%20build)](https://github.com/Yahballon/PS5_Bible_Reader/actions/workflows/build.yml)
![Languages](https://img.shields.io/badge/languages-5-ccac70?style=flat-square)
![Offline](https://img.shields.io/badge/runtime-100%25%20offline-456c60?style=flat-square)
[![GPL-3.0](https://img.shields.io/badge/license-GPL--3.0-blue?style=flat-square)](LICENSE)

**[Download v0.2.1](https://github.com/Yahballon/PS5_Bible_Reader/releases/tag/bible-reader-v0.2.1)** · **[Source code](https://github.com/Yahballon/PS5_Bible_Reader)** · **[Build workflow](.github/workflows/build.yml)**

</div>

---

## About

Bible Reader is a **native C++ homebrew application** for compatible PS5 jailbreak/homebrew environments. It contains the complete 66-book Bible in **five languages** and supports comfortable reading, search, bookmarks, daily verses, and natural **Piper neural text-to-speech computed locally on the console**. After installation, reading and speech **do not require internet access**.

Unlike a web browser wrapper, the interface, controller handling, native SDL2 audio, offline verse database and ONNX Runtime CPU inference run locally. A classic eSpeak NG engine is retained as a fallback.

## Highlights

- **Full Bible:** 66 books, 1,189 chapters and over 31,000 verses per language, stored on the console.
- **Five full translations:** English, Deutsch, Français, Español and Русский.
- **Native Neural HD speech:** Piper VITS ONNX voices for all five languages. Press **X** on a verse to start reading from that point; playback can continue through subsequent verses, chapters and books.
- **Yellow karaoke-style word highlighting** while a verse is spoken. For Piper, word alignment is an **approximation** based on eSpeak timing rescaled to neural audio; it is not phoneme-forced alignment.
- **Classic and Retro TTS:** Offline eSpeak NG backup plus an optional English computer-voice preset labeled *Stephen Hawking (Retro TTS)*. This is a synthetic homage, **not the original person's voice**.
- **Comfortable reading:** Old/New Testament picker, chapters, adjustable font size, two themes, optional Daily Verse panel and wider reading mode.
- **Search and bookmarks:** Search the selected offline translation, save favorite passages and retain reading position.
- **Multilingual interface:** Menus, book names, on-screen search keyboard and Bible text follow the chosen language, including Cyrillic support.
- **Privacy-first:** Entirely offline at runtime; no login, streaming service or speech API.

## Included languages and voices

| Language | Bible translation | Neural HD voice |
|---|---|---|
| English | World English Bible, British Edition (WEBPB) | Kristin Medium |
| Deutsch | Lutherbibel 1912 | Thorsten Medium |
| Français | Louis Segond 1910 | Siwis Medium |
| Español | Reina-Valera 1909 | DaveFX Medium |
| Русский | Russian Synodal Bible 1876 | Denis Medium |

Public-domain Bible texts are obtained from [eBible.org](https://ebible.org/) **during the build**, verified and packaged inside the installer. Neural voice models are also downloaded and integrity-checked during CI. Each distribution contains their licensing and attribution files.

## Installation

1. Download **`PPSA99793.zip`** from the [latest release](https://github.com/Yahballon/PS5_Bible_Reader/releases/latest).
2. Extract the archive; it contains the **`PPSA99793/`** PS5 homebrew installation directory.
3. Copy this directory to **`/data/homebrew/PPSA99793/`**, or install it using a compatible homebrew manager.
4. Launch **Bible Reader** through your existing compatible PS5 homebrew environment.

> **Important:** This is a native **PS5 homebrew installation ZIP**, not an official Sony PKG. It requires a compatible homebrew-capable console. The ZIP is relatively large because it includes five offline neural models and all translations.

### DualSense controls

| Button | Action |
|---|---|
| **D-pad** | Move focus, browse books, verses and menus |
| **X** | Select a menu entry or start reading aloud from a verse |
| **O** | Go back or stop ongoing narration; exit confirmation from the main menu |
| **□ Square** | Bookmark verse / delete character in search |
| **△ Triangle** | Open search |
| **R2** | Submit entered search |
| **L1 / R1** | Previous / next chapter |
| **L2** | Open Daily Verse |
| **OPTIONS** | Settings |

### Voice options

Open **Settings → Language** to pick the Bible/interface language, then **Settings → Voice style** to choose **Neural HD** or **Classic**. The optional retro preset uses English eSpeak speech. If native Piper cannot initialize or generate audio, the app falls back to its classic engine; an included German Genesis 1 prerecorded preview offers an additional fallback.

## Persistent files and diagnostics

| Path | Purpose |
|---|---|
| `/data/BibleReader/settings.txt` | Font size, colors, daily panel, language, voice style, bookmarks and last reading position |
| `/data/BibleReader/bible-reader.log` | Native speech and application diagnostics |
| `/data/BibleReader/bible-reader.previous.log` | Rotated previous log |
| `/data/homebrew/PPSA99793/assets/bible/` | Bundled translation data (installation directory) |
| `/data/homebrew/PPSA99793/assets/tts/neural/` | Bundled offline Piper models |

The TTS log includes events such as `PIPER_READY`, `PIPER_NATIVE`, `PIPER_RUN_ERR`, and `PIPER_FALLBACK`. Model loading or synchronous verse inference can take time, and interruption during a model run may be delayed.

## Build from source

Everything required to **build the app** is under version control, except third-party source releases, voices, fonts, Bible text and toolchain files downloaded during CI. You do **not** need a Windows-based SDK or private proprietary keys for the documented homebrew build.

The [native PS5 workflow](.github/workflows/build.yml) runs on Ubuntu and:

1. Builds/tests the native Bible reading and TTS logic.
2. Downloads/validates all five public-domain translations and generates the Unicode font atlas.
3. Downloads pinned Piper models and verifies their hashes/licenses.
4. Prepares [PS5-DevBridge](https://github.com/Yahballon/PS5-DevBridge), the PS5 SDK, SDL2 and the offline eSpeak NG runtime.
5. Cross-compiles ONNX Runtime CPU and its dependencies with the PS5-targeted Clang toolchain and portability patches.
6. Links the native executable, assembles `PPSA99793.zip`, and verifies its payload.
7. Publishes release assets **only after a successful build** (or via the explicitly verified v0.2.1 release promotion).

Use **Actions → Build Bible Reader for PS5 → Run workflow** for a reproducible test build. Versioned future tags matching `bible-reader-v*` can trigger a release build.

### Source layout

```text
native/          C++ PS5 Bible reader, UI, localization and TTS worker
native_piper/    Piper ONNX inference, phonemes, PS5 portability and tests
tools/           Bible corpus, font generation and offline TTS build tools
tests/           Corpus, reading, Unicode and TTS regression tests
assets/          Original PS5 app icon generator and banner artwork
.github/workflows/  Native build, language tests and release workflows
app.json         Title ID, languages, feature metadata and version
RELEASE_NOTES.md Latest changelog
LICENSE          GPL-3.0
```

## v0.2.1 release notes

The current release fixes the Russian classic/fallback voice initialization (`zle/ru`) that could previously display **“Voice unavailable”**, adds validation for all five profiles and preserves the tested Neural HD reader. See [RELEASE_NOTES.md](RELEASE_NOTES.md) for details.

## Licenses and credits

- **App source:** GPL-3.0, see [LICENSE](LICENSE).
- **eSpeak NG:** GPL-3.0-or-later; [upstream](https://github.com/espeak-ng/espeak-ng).
- **ONNX Runtime:** MIT; [upstream](https://github.com/microsoft/onnxruntime).
- **Piper models:** See the distributed model cards and voice attribution; different datasets have different license and credit requirements, including **CC BY 4.0 for French SIWIS**.
- **Bible translations:** Public-domain editions distributed by [eBible.org](https://ebible.org/).
- **DejaVu fonts:** License documentation is included in the install package.
- **PS5-DevBridge / PS5 Payload SDK:** Follow their respective upstream licensing and notices.

No Sony or PlayStation logos are included in the original homebrew artwork. “PS5” describes the target platform only; the project is independent and not affiliated with Sony.

---

<div align="center"><strong>Read. Listen. Explore — entirely offline.</strong><br/>Made for the PS5 homebrew community · <strong>by YAHBALLON</strong></div>
