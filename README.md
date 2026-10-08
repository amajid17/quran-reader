# Quran Reader

A clean, offline-capable Arabic Quran reader built with React Native and Expo. Designed for focused, distraction-free reading with full Tajweed color support, RTL layout, and no network dependency at runtime.

---

## Try it now — Expo Go

Scan with the **Expo Go** app (iOS or Android):

<p align="center">
  <img src="assets/qr_code.png" alt="Scan to open in Expo Go" width="180" />
</p>

<p align="center">
  <a href="https://expo.dev/accounts/robert_19/projects/quran-reader/updates/0d0bf5eb-7135-4814-95bb-6d3630dd29ed">
    Open update page on Expo →
  </a>
</p>

> Requires [Expo Go](https://expo.dev/go) on your device. The app runs entirely offline once loaded — no account, no network, no tracking.

---

## Screenshots

| Home (Light) | Home (Dark) | Surah View |
|:---:|:---:|:---:|
| ![Home light](screenshots/homepage_1.png) | ![Home dark](screenshots/homepage_dark_mode.png) | ![Surah opened](screenshots/surah_details_2.png) |

| Surah Details | Juz List | Bookmarks |
|:---:|:---:|:---:|
| ![Surah details](screenshots/surah_details_1.png) | ![Juz navigation](screenshots/juzz_screen.png) | ![Bookmarks](screenshots/bookmark_screen.png) |

| Settings – Appearance | Settings – Reading & Data |
|:---:|:---:|
| ![Settings appearance](screenshots/settings_screen_1.png) | ![Settings reading](screenshots/settings_screen_2.png) |

| Tajweed Rules – Part 1 | Tajweed Rules – Part 2 |
|:---:|:---:|
| ![Tajweed rules overview](screenshots/tajweed_rules_1.png) | ![Tajweed rules detailed](screenshots/tajweed_rules_2.png) |

---

## Features

- **Tajweed coloring** — rules rendered inline via `rn-tajweed-verse`; every rule is visually marked without overlays or post-processing
- **RTL-first layout** — proper right-to-left Arabic rendering using `I18nManager`
- **Three bundled Arabic fonts** — Scheherazade New, Noto Sans Arabic, and Literata Bold; user-switchable at runtime
- **Bookmarks and reading progress** — resume exactly where you left off, persisted across sessions
- **Dark / Light theme** — stored locally; status bar adapts automatically
- **Data integrity check** — validates all 114 surahs against expected checksums on launch; loud in development, silent in production
- **Error boundary** — catches unexpected crashes and surfaces a recovery screen; bookmarks and progress are unaffected
- **Fully offline** — all Quran data is bundled at build time; zero network requests at runtime

---

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | React Native 0.81 · Expo SDK 54 |
| Language | TypeScript |
| Navigation | React Navigation v7 (Native Stack + Bottom Tabs) |
| Quran data | `quran-json` |
| Tajweed rendering | `rn-tajweed-verse` |
| Fonts | `expo-font` — Scheherazade New, Noto Sans Arabic, Literata Bold |
| Storage | `@react-native-async-storage/async-storage` |
| Gestures | `react-native-gesture-handler` |

---

## Getting Started

### Prerequisites

- Node.js 18+
- [Expo Go](https://expo.dev/go) on your phone, or an iOS/Android simulator

### Run locally

```bash
git clone https://github.com/amajid17/Quran-reader.git
cd quran-reader
npm install
npx expo start
```

Scan the terminal QR code with Expo Go to run on your device instantly.

### Build for production

```bash
npx eas build --platform ios
npx eas build --platform android
```

Requires a free [Expo EAS](https://expo.dev/eas) account.

---

## Project Structure

```
quran-reader/
├── App.tsx                   # Root: error boundary, fonts, theme, navigator
├── app.json                  # Expo config (bundle IDs, splash, orientation)
├── index.ts                  # Entry point
├── assets/
│   └── fonts/                # Bundled Arabic typefaces
├── scripts/
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

**Fonts** — Loaded at startup via `expo-font`. The splash screen stays open until all three typefaces resolve, preventing a flash of unstyled Arabic text on first render.

**Theme** — A React context wraps the entire tree. `ThemedStatusBar` is a dedicated component inside the provider so it can call `useTheme()` without conflicting with the class-based `ErrorBoundary` above it.

**Integrity check** — Runs once after fonts are ready. In development, any checksum mismatch logs exactly which surahs failed and points to the download script. In production the check is silent.

**Error boundary** — If a screen throws, the boundary catches it, logs the component stack in development, and shows a recovery screen with a "Try again" button. Bookmarks and reading progress stored in AsyncStorage are unaffected.

---

## License

MIT
