# Quran Reader

> A focused Arabic Quran reader for iOS and Android. Tajweed rules rendered inline. Three bundled typefaces. Everything works offline, from day one, with no account and no network.

---

## Try it

<table>
<tr>
<td align="center" width="50%">

**iOS — Expo Go**

Scan with the [Expo Go](https://expo.dev/go) app

<img src="assets/qr_code.png" width="160" alt="Expo Go QR code" />

*Opens directly in Expo Go on your device*

</td>
<td align="center" width="50%">

**Android — Preview build**

No Expo Go required

[![Download APK](https://img.shields.io/badge/Download%20Android%20Preview-3DDC84?style=for-the-badge&logo=android&logoColor=white)](https://expo.dev/accounts/robert_19/projects/quran-reader/builds/9dd4b04a-1374-40d4-bd67-6f4d66f10bf2)

---

## Screenshots

<table>
<tr>
  <td align="center"><img src="screenshots/homepage_1.png" width="160"/><br/><sub>Home · Light</sub></td>
  <td align="center"><img src="screenshots/homepage_dark_mode.png" width="160"/><br/><sub>Home · Dark</sub></td>
  <td align="center"><img src="screenshots/surah_details_2.png" width="160"/><br/><sub>Surah View</sub></td>
</tr>
<tr>
  <td align="center"><img src="screenshots/surah_details_1.png" width="160"/><br/><sub>Surah Details</sub></td>
  <td align="center"><img src="screenshots/juzz_screen.png" width="160"/><br/><sub>Juz Navigation</sub></td>
  <td align="center"><img src="screenshots/bookmark_screen.png" width="160"/><br/><sub>Bookmarks</sub></td>
</tr>
<tr>
  <td align="center"><img src="screenshots/settings_screen_1.png" width="160"/><br/><sub>Settings · Appearance</sub></td>
  <td align="center"><img src="screenshots/settings_screen_2.png" width="160"/><br/><sub>Settings · Reading</sub></td>
  <td align="center"><img src="screenshots/tajweed_rules_1.png" width="160"/><br/><sub>Tajweed Rules</sub></td>
</tr>
</table>

---

## Design decisions

Most Quran apps make a tradeoff between richness and focus. This one doesn't try to be a library, a learning platform, or a social product. It tries to be the clearest possible reading surface for the Arabic text — the way a well-printed mushaf works, but on a phone.

Every technical decision follows from that:

**Tajweed rendered inline, not as an overlay.**
`rn-tajweed-verse` marks each rule directly in the text using per-rule colors. No position calculations, no z-index stacking, no rendering artifacts on scroll. The Tajweed coloring is part of the text, not on top of it.

**All 114 surahs bundled at build time.**
There is no fetch, no cache warm-up, no loading spinner for content. The app opens to readable text immediately, including on first launch with no network. A startup integrity check validates every surah against its expected checksum — loudly in development, silently in production.

**Three Arabic typefaces, user-switchable.**
Scheherazade New, Noto Sans Arabic, and Literata Bold each render Arabic differently at reading sizes. Bundling all three and letting the reader choose respects that preference without requiring a download or a settings round-trip to a server.

**RTL-first, not RTL-patched.**
`I18nManager` is set at the root before any layout runs. The navigation, gestures, and text all assume right-to-left from the start rather than flipping a left-to-right layout afterward.

**No backend, no account, no tracking.**
There is no server, no analytics SDK, no login flow, and no data that leaves the device. Reading progress and bookmarks live in `AsyncStorage`. Nothing else is stored.

---

## Stack

| Layer | Choice | Why |
|---|---|---|
| Framework | React Native 0.81 · Expo SDK 54 | Managed workflow — single codebase, iOS + Android |
| Language | TypeScript | Type safety on navigation params, theme context, data shapes |
| Navigation | React Navigation v7 | Native stack + bottom tabs; RTL-aware |
| Quran data | `quran-json` | Complete, well-structured, bundleable at build time |
| Tajweed | `rn-tajweed-verse` | Inline rule coloring with no overlay layer |
| Fonts | `expo-font` | Loads at splash; held open until all three typefaces resolve |
| Storage | `@react-native-async-storage/async-storage` | Bookmarks + progress; survives crashes |
| Gestures | `react-native-gesture-handler` | Native gesture responder for swipe navigation |

---

## Run locally

```bash
git clone https://github.com/amajid17/Quran-reader.git
cd quran-reader
npm install
npx expo start
```

Scan the QR code in the terminal with Expo Go, or press `i` for iOS simulator / `a` for Android emulator.

### Build for production

```bash
# Android
npx eas build --platform android

# iOS
npx eas build --platform ios
```

Requires a free [Expo EAS](https://expo.dev/eas) account.

---

## Project structure

```
quran-reader/
├── App.tsx                     # Root: error boundary → fonts → theme → navigator
├── index.ts                    # Entry point
├── app.json                    # Expo config: bundle IDs, splash, orientation
├── assets/
│   ├── fonts/                  # Scheherazade New, Noto Sans Arabic, Literata Bold
│   └── qr_code.png             # Expo Go QR code
├── screenshots/                # Store listing + README screenshots
└── src/
    ├── navigation/
    │   └── AppNavigator.tsx    # Stack + tab structure
    ├── theme/
    │   └── ThemeContext.tsx    # Dark / light provider + useTheme hook
    └── data/
        └── integrityCheck.ts  # Validates all 114 surahs at startup
```

### How the layers fit together

```
App.tsx
 └── ErrorBoundary              catches screen-level crashes; bookmarks unaffected
      └── FontLoader            holds splash open until all 3 typefaces resolve
           └── ThemeProvider    dark / light context; ThemedStatusBar lives inside
                └── AppNavigator
                     ├── integrityCheck()   runs once after fonts are ready
                     └── screens...
```

**Error boundary placement** — The boundary wraps everything below font loading. If a screen throws, the recovery UI can still render because fonts are guaranteed ready. Bookmarks and reading progress (AsyncStorage) are outside the component tree and survive any render error.

**Theme isolation** — `ThemedStatusBar` is a separate component inside the provider so it can call `useTheme()` without being blocked by the class-based `ErrorBoundary` above it. Class components can't use hooks, so the status bar lives one level down.

---

## License

MIT- **Error boundary** — catches unexpected crashes gracefully and lets the user resume without losing progress
- **Fully offline** — all Quran data is bundled at build time; no network requests at runtime

---

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | React Native 0.81 + Expo SDK 54 |
| Language | TypeScript |
| Navigation | React Navigation v7 (Native Stack + Bottom Tabs) |
| Quran data | `quran-json` |
| Tajweed rendering | `rn-tajweed-verse` |
| Fonts | `expo-font` (Scheherazade New, Noto Sans Arabic, Literata Bold) |
| Storage | `@react-native-async-storage/async-storage` |
| Gestures | `react-native-gesture-handler` |

---

## Getting Started

### Prerequisites

- Node.js 18+
- Expo CLI: `npm install -g expo-cli`
- Expo Go app on your phone **or** a simulator

### Install & Run

```bash
git clone https://github.com/amajid17/Quran-reader.git
cd quran-reader
npm install
npx expo start
```

Scan the QR code with Expo Go (iOS / Android) to run instantly on your device.

### Build for Production

```bash
npx eas build --platform ios
npx eas build --platform android
```

Requires an [Expo EAS](https://expo.dev/eas) account (free tier is sufficient).

---

## Project Structure

```
quran-reader/
├── App.tsx                   # Root: error boundary, fonts, theme, navigator
├── app.json                  # Expo config (bundle IDs, splash, orientation)
├── assets/
│   └── fonts/                # Bundled Arabic & Latin fonts
└── src/
    ├── navigation/
    │   └── AppNavigator.tsx  # Stack + tab structure
    ├── theme/
    │   └── ThemeContext.tsx  # Dark/light mode provider + useTheme hook
    └── data/
        └── integrityCheck.ts # Validates bundled Quran data at startup
```

---

## Architecture Notes

**Fonts** — Loaded at startup via `expo-font`; splash screen is held open until fonts resolve, preventing a flash of unstyled Arabic text.

**Theme** — A React context wraps the entire tree. `ThemedStatusBar` is a small dedicated component inside the provider so it can call `useTheme()` without breaking the class-based `ErrorBoundary` above it.

**Integrity check** — Runs once after fonts are ready. In development, any checksum mismatch logs which surahs failed and points to the download script. In production, this check is silent to avoid alarming users over minor data discrepancies.

**Error boundary** — If a screen crashes, the boundary catches it, logs the component stack in dev, and shows a recovery screen with a "Try again" button. Bookmarks and reading progress (stored externally in AsyncStorage) are unaffected.

---

## License

MIT
