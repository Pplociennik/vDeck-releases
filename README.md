# vDeck

[![Najnowsza wersja](https://img.shields.io/github/v/release/Pplociennik/vDeck-releases?label=wersja&sort=semver)](https://github.com/Pplociennik/vDeck-releases/releases/latest)
[![Data wydania](https://img.shields.io/github/release-date/Pplociennik/vDeck-releases?label=wydano)](https://github.com/Pplociennik/vDeck-releases/releases/latest)
[![Pobrania](https://img.shields.io/github/downloads/Pplociennik/vDeck-releases/total?label=pobrania)](https://github.com/Pplociennik/vDeck-releases/releases)
![Platforma](https://img.shields.io/badge/platforma-Windows%20x64-0078D6)
![Java](https://img.shields.io/badge/Java-25%20w%20paczce-ED8B00)
![Język](https://img.shields.io/badge/j%C4%99zyk-polski-DC143C)

Lokalny panel webowy do zarządzania dedykowanym serwerem **Valheim** na Windows x64.
vDeck instaluje się obok `valheim_server.exe`, działa na tej samej maszynie i jest obsługiwany
z przeglądarki: pozwala edytować ustawienia serwera, uruchamiać go, zatrzymywać z kopią świata
i obserwować jego stan oraz konsolę.

> To repozytorium zawiera wyłącznie gotowe wydania. Kod źródłowy vDeck nie jest publiczny.
> Projekt jest we wczesnej fazie rozwoju, a interfejs panelu jest dostępny tylko po polsku.

## Pobieranie

Pobierz plik **`vDeck-dist-<wersja>.zip`** z [najnowszego wydania](https://github.com/Pplociennik/vDeck-releases/releases/latest).
Opis każdego wydania zawiera listę nowości, zmian i poprawek tej wersji.

## Co potrafi panel

- ustawienia serwera w czterech panelach: Ogólne, Dostęp, Świat i zapis, Kopie automatyczne;
- ustawienia gry, w tym zestawy ustawień świata z opisem ich działania;
- Start, Stop z kopią świata, ponowne uruchomienie i potwierdzane wymuszone zakończenie (Kill);
- podgląd stanu serwera i jego konsoli na żywo;
- listy graczy: bany, administratorzy i biała lista (Steam i Xbox);
- automatyczne aktualizacje i lista zmian po aktualizacji.

Zarządzanie światami i kopiami, przywracanie kopii, RCON, mody i harmonogramy nie są jeszcze dostępne.

## Wymagania

- Windows x64.
- Zainstalowany Valheim Dedicated Server (`valheim_server.exe`).
- Przeglądarka internetowa i prawo zapisu w katalogu instalacji serwera.
- Wolne miejsce na dane świata i jego kopię.
- Dostęp do internetu tylko dla automatycznych aktualizacji (opcjonalnie).

Javy nie trzeba instalować: paczka zawiera pełne środowisko Eclipse Temurin JRE 25.

## Instalacja

1. Pobierz `vDeck-dist-<wersja>.zip` z [najnowszego wydania](https://github.com/Pplociennik/vDeck-releases/releases/latest).
2. Wypakuj **całą** zawartość do katalogu Valheim Dedicated Server, tak aby `start-vDeck.bat` leżał
   obok `valheim_server.exe`.
3. Uruchom `start-vDeck.bat`. Po starcie przeglądarka sama otworzy `http://127.0.0.1:8085`.
4. Zaloguj się jako `admin` / `admin` i ustaw własne hasło — panel wymaga tego przy pierwszym logowaniu.

Pełna instrukcja obsługi ze zrzutami ekranu to plik `vDeck-instrukcja.pdf` w paczce.

### Zawartość paczki

```text
start-vDeck.bat            # jedyny plik, który uruchamiasz
README.md, README.txt      # skrócony opis
vDeck-instrukcja.pdf       # pełna instrukcja obsługi
lib/                       # programy vDeck
jre/                       # dołączona Java
.vDeck/config/             # pliki ustawień
```

Wszystko, co vDeck tworzy podczas pracy (baza, logi, kopie świata, pliki aktualizacji), trafia do
katalogu `.vDeck`.

## Ustawienia

Wszystkie ustawienia edytowalne ręcznie leżą w `.vDeck\config\`:

| Plik | Zawartość |
|---|---|
| `vDeck-start.properties` | adres i port panelu oraz włączenie automatycznych aktualizacji |
| `vDeck.properties` | ustawienia samego panelu (np. zachowanie serwera przy zamknięciu panelu, logi) |
| `valheim-server.properties` | profil uruchomienia serwera Valheim, edytowany zwykle z panelu |

Domyślnie panel nasłuchuje tylko na `127.0.0.1:8085`, czyli jest dostępny wyłącznie z tego komputera.
Zmień `panel.address` na adres w sieci lokalnej albo `0.0.0.0` tylko w zaufanej sieci: panel działa
przez zwykłe HTTP. Jeśli port `8085` jest zajęty, zmień `panel.port`.

## Aktualizacje

Przy każdym uruchomieniu `start-vDeck.bat` sprawdza, czy w tym repozytorium jest nowsza wersja,
i instaluje ją przed startem panelu. Aktualizacja:

- odbywa się tylko wtedy, gdy serwer Valheim jest zatrzymany;
- zachowuje ustawienia, bazę panelu i światy — nowe ustawienia dostają wartości domyślne,
  a Twoje wartości zostają;
- działa w transakcji z dziennikiem: przerwana (np. przez zanik prądu) zostaje wycofana albo
  dokończona przy następnym uruchomieniu;
- nigdy nie obniża wersji i nie wymaga uprawnień administratora;
- w razie problemu z siecią lub pobraniem po prostu uruchamia zainstalowaną wersję.

Aby wyłączyć automatyczne aktualizacje, ustaw `update.enabled=false` w
`.vDeck\config\vDeck-start.properties`. Ręczna aktualizacja polega wtedy na wypakowaniu nowej paczki
w to samo miejsce.

## Weryfikacja paczki

Wydania są niezmienne (immutable releases): opublikowanego pliku nie da się podmienić. Przy każdym
pliku wydania GitHub pokazuje jego skrót SHA-256 i ten sam skrót sprawdza launcher przed instalacją.
Pobraną paczkę możesz sprawdzić ręcznie w PowerShell:

```powershell
Get-FileHash .\vDeck-dist-<wersja>.zip -Algorithm SHA256
```

Wynik musi być równy skrótowi `sha256:` podanemu przy pliku na stronie wydania.

## Bezpieczeństwo danych świata

- Przed każdym zatrzymaniem (Stop) vDeck wykonuje kompletną kopię plików świata, a dopiero potem
  wysyła do serwera sygnał CTRL+C. Nieudany Stop pozostawia serwer uruchomiony.
- Wymuszone zakończenie (Kill) wymaga ręcznego potwierdzenia.
- Jedna instalacja obsługuje jeden serwer; vDeck nie pozwoli uruchomić go dwukrotnie i po ponownym
  starcie panelu przejmuje serwer, który dalej działa.

## Zgłaszanie problemów

Błędy i propozycje zgłaszaj w zakładce [Issues](https://github.com/Pplociennik/vDeck-releases/issues).
Dołącz numer wersji (widoczny na dole menu bocznego panelu) oraz log z `.vDeck\logs\` z chwili
wystąpienia problemu. Logi nie zawierają haseł.

---

<sub>Panel vDeck jest tworzony ze wsparciem Claude Code oraz OpenAI Codex, pod nadzorem doświadczonego
inżyniera oprogramowania.</sub>
