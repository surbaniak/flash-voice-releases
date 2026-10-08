# Prywatność we Flashu 2.0

Stan implementacji: 8 października 2026. Aplikacja nie wymaga konta Flash ani abonamentu i nie korzysta z serwera AppFly do przetwarzania nagrań.

Ścieżka przetwarzania to **Twój Mac → Groq → Twój Mac**, przez szyfrowane połączenie HTTPS. Groq przetwarza przesłane audio lub tekst i odsyła wynik bez serwera pośredniczącego Flasha. Klucz pozostaje w Pęku kluczy macOS i służy do uwierzytelnienia bezpośredniego żądania do Groq. Szyfrowanie połączenia chroni transmisję; nie oznacza, że dostawca nie otrzymuje treści do przetworzenia.

Nie obiecujemy bezwarunkowego zerowego przechowywania po stronie dostawcy. Obowiązują zasady Groq i ustawienia danych na koncie użytkownika, w tym dostępne ustawienie Zero Data Retention. To nie jest całkowicie lokalne przetwarzanie ani obietnica braku wszelkiego ryzyka.

## Dyktowanie i AI

Audio przekazane do transkrypcji trafia przez HTTPS bezpośrednio do Groq z kluczem użytkownika, do modelu `whisper-large-v3`. Krótkie dyktowanie jest sprawdzane lokalnie pod kątem mowy przed wysłaniem po Stop. W dłuższym zwykłym dyktowaniu zakończone fragmenty mogą być wysyłane już podczas nagrywania, gdy lokalny detektor wykryje mowę, a następnie odpowiednią pauzę. Fragmenty nie mają wspólnego audio. Anulowanie zatrzymuje dalszą wysyłkę i wklejenie, ale nie cofa fragmentów już przesłanych. Końcowa kontrola ciszy nie oznacza, że żaden wcześniejszy fragment nie został wysłany.

Włączona korekta działa dopiero po otrzymaniu wszystkich transkrypcji. Korekta, funkcja Zmień tekst poleceniem i własne akcje wysyłają potrzebny tekst, zaznaczenie oraz odpowiedni kontekst słownika do Groq (`openai/gpt-oss-20b`). Funkcja Zmień tekst poleceniem otrzymuje wyłącznie zaznaczony tekst, polecenie i metadane granic (np. środek/początek zdania); treść sąsiednich fragmentów nie jest wysyłana. Flash nie uruchamia narzędzi wyszukiwania WWW. Obowiązują warunki, limity i ustawienia danych konta Groq: https://console.groq.com/docs/your-data.

Teleprompter korzysta z Apple Speech. Zależnie od języka i dostępności Apple może rozpoznawać mowę online. Zgodę na mikrofon oraz rozpoznawanie mowy można nadać w samouczku lub Ustawieniach → Prywatność. Kliknięcie odpowiedniego przycisku wywołuje dialog macOS, jeśli decyzja nie została jeszcze podjęta; samo otwarcie panelu ani nadanie zgody nie uruchamia nagrywania. Teleprompter sprawdza uprawnienia również przy uruchomieniu, jeśli pominięto je wcześniej.

Klucz Groq i klucz szyfrowania historii znajdują się w osobnej przestrzeni macOS Keychain (`pl.appfly.flash.voice`). Nagranie dyktowania jest przetwarzane w pamięci; po błędzie może pozostać tam do ręcznego ponowienia, odrzucenia, rozpoczęcia nowego nagrania lub zamknięcia aplikacji. Aplikacja nie zapisuje plików nagrań dyktowania.

## Dane lokalne

W wersji 2.0.10 wskaźnik Pełny notch pokazuje także podgląd słów podczas nagrywania. Używa on wyłącznie lokalnego Apple Speech, po zgodzie macOS na rozpoznawanie mowy i tylko dla języka obsługiwanego na urządzeniu. Nie przełącza się na serwery Apple. Podgląd jest tymczasowy, może się zmieniać i nie jest źródłem tekstu wstawianego ani historii - końcowy wynik nadal pochodzi z Groq. Gdy lokalny podgląd jest niedostępny, samo dyktowanie działa dalej.

Podejrzany wynik zawierający znaną frazę o prawach autorskich może otrzymać dodatkową kontrolę przez lokalne Apple Speech, jeśli zgoda i język są już dostępne. Ta kontrola nie prosi samodzielnie o nowe uprawnienia, nie wysyła audio do Apple i nie zastępuje wyniku tekstem innego modelu. Przy wyraźnej sprzeczności Flash zachowuje nagranie w pamięci do ponowienia zamiast wklejać podejrzany wynik.

Animacja Voice Glow pochodzi z dołączonych plików na licencji MIT. Jej lokalny renderer otrzymuje jedynie poziom głośności i stan pauzy, bez audio, transkrypcji, kluczy ani połączeń z Libraries.dev.

`~/Library/Application Support/FlashVoice` przechowuje historię, słownik, akcje i skrypty. Historia (`history.enc`) jest szyfrowana AES-256-GCM. Słownik, akcje, skrypty i ich wersje są lokalnymi plikami JSON, nie są szyfrowane przez aplikację. Historia przechowuje również nazwę aplikacji docelowej lokalnie; nie jest ona wysyłana do analityki.

Import ze starego Flasha lub Noryna jest wyłącznie ręczny, pokazuje podsumowanie i tworzy kopię obecnych plików w `ImportBackups`. Kopie również mogą zawierać prywatne treści. Źródła nie są modyfikowane. Nie importujemy kluczy API, kont, licencji ani zgód analitycznych.

## Dwie niezależne, domyślnie wyłączone zgody

- **Wydajność i diagnostyka:** TelemetryDeck oraz PostHog otrzymują czas etapów dyktowania, powodzenie wstawienia, długość nagrania/liczbę słów, model i architekturę Maca, przedział RAM, wersję systemu/aplikacji oraz kody błędów. PostHog otrzymuje także oczyszczone techniczne raporty natywnych awarii.
- **Użycie funkcji:** PostHog otrzymuje uruchomienia, kroki samouczka, użyte kategorie funkcji i zdarzenia telepromptera.

Żadna z tych usług nie otrzymuje audio, transkrypcji, schowka, promptów, zaznaczonego tekstu, kluczy API, nazw skryptów/akcji ani nazw aplikacji docelowych. Raporty awarii usuwają tekst wyjątków i ścieżki użytkownika, zachowując adresy ramek i identyfikatory plików binarnych potrzebne do diagnozy. Symbole deweloperskie nie zawierają danych użytkowników.

Każda usługa używa osobnego losowego identyfikatora instalacji; nie identyfikujemy osoby ani konta. PostHog pomija geolokalizację po IP i nie uruchamia nagrywania ekranów/sesji. Dostawcy technicznie obsługują połączenia sieciowe, więc aplikacja nie obiecuje niewidoczności adresu IP dla infrastruktury dostawcy.

Zgody można niezależnie wyłączyć w Prywatności. Flash zatrzymuje nowe zdarzenia, usuwa oczekującą kolejkę i odrzuca stare raporty awarii po ponownym udzieleniu zgody. Żądania już wysłanego do dostawcy nie można cofnąć. Wyłączenie nie usuwa historycznych danych na koncie dostawcy.

Raport diagnostyczny do ręcznego skopiowania zawiera wersje, stan uprawnień i do 30 kodów operacyjnych. Nie zawiera treści dyktowania ani kluczy.

Nowe logi systemowe Flasha zapisują techniczne wyniki, czasy i kody, bez nazw własnych akcji, poleceń, opisów błędów zawierających dowolny tekst, identyfikatorów aplikacji docelowych ani nazw wpisów Keychain. Poprawka nie usuwa historycznych logów zapisanych przez wcześniejsze kompilacje.

## Aktualizacje

Flash korzysta z publicznego kanału aktualizacji. Pobiera manifest przez HTTPS i weryfikuje podpis Ed25519, hash/rozmiar paczki, tożsamość aplikacji, podpis Developer ID i notaryzację Apple. Sprawdzenie aktualizacji nie wymaga konta Flash ani tokenu GitHub. Nie stosujemy ukrytego serwera licencji ani konta do aktualizacji.
