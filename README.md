# Flash

Free dictation and a voice-following teleprompter for macOS, by Sebastian Urbaniak / AppFly | Flying Pixel.

## Download

**[Download Flash for macOS](https://github.com/surbaniak/flash-voice-releases/releases/latest/download/Flash.zip)**

macOS 14 or later. One Universal 2 app for Apple Silicon and Intel. Polish and English interface; the initial language follows your Mac's language preferences unless you have chosen a language in Flash.

Unzip the download, move **Flash.app** to **Applications**, and open it. Follow the setup guide to add your own Groq API key and grant the required macOS permissions. The app is Developer ID signed and notarized by Apple.

## What is included

- Dictation with Groq Whisper Large v3.
- Optional text cleanup and reusable text actions with Groq GPT OSS 20B.
- Voice-following teleprompter with Apple Speech.
- Local history, a custom dictionary, keyboard shortcuts, and light/dark appearance.

Flash requires no Flash account or subscription. Groq account limits and any provider charges are separate. Audio and text go directly from your Mac to Groq over HTTPS and the result returns to Flash, without a Flash intermediary server. Apple Speech may also process speech online. See [Privacy](PRIVACY.md).

## Updates and source code

This repository distributes official binary releases and public documentation only. Flash's application source code remains private. Free distribution does not make the application open source. Third-party notices are included in the app bundle.

Update manifests are signed with a separate release key. Flash also checks the archive checksum, publisher identity, application signature and notarization before installation.

- [Release notes](https://github.com/surbaniak/flash-voice-releases/releases)
- [Website](https://flash.appfly.pl)
- [Support](mailto:kontakt@appfly.pl)

## Po polsku

Flash to bezpłatne dyktowanie i teleprompter dla macOS 14+. Pobierz paczkę, rozpakuj ją i przenieś **Flash.app** do **Aplikacji**. Samouczek przeprowadzi Cię przez konfigurację własnego klucza Groq i uprawnień. Kod źródłowy aplikacji pozostaje prywatny. Zasady i limity usług Groq oraz Apple obowiązują niezależnie od Flasha.
