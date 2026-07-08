# Linux Setup – Debian 13 + Sway

Minimalny setup oparty na Debian 13 i menedżerze okien Sway.
Celem było pozbycie się ciężkich środowisk (GNOME) i skonfigurowanie lekkiego, ręcznie sterowanego systemu, dostosowanego do specyfiki sprzętowej pod Waylandem.

## Komponenty

* **Sway** – główny window manager
* **Waybar** – panel statusu z natywnymi i autorskimi modułami
* **Wofi** – launcher i menu wyboru (także jako `--dmenu` w skryptach)
* **Foot** – terminal
* **Micro** – lekki edytor tekstu
* **Pakiety dodatkowe i systemowe:** `grim`, `slurp`, `wl-clipboard`, `libinput-tools`, `wev`, `wireplumber`, `bluez`, `network-manager`, `brightnessctl`

## Struktura repo

* `sway/` → konfiguracja Sway + `manifest.yaml`
* `waybar/` → `config.jsonc`, `style.css`, skrypty (`brightness-menu.sh`, `audio-menu.sh`, `wifi-menu.sh`, `power-menu.sh`)
* `wofi/` → `config` + `style.css`
* `foot/` → `foot.ini` (kolory terminala)
* `desktop-files/` → własne launchery `.desktop` (Firefox profiles, ChatGPT Chromium, Discord, Telegram, WhatsApp)

## Najważniejsze ustawienia i architektura

**Autologowanie**
Po usunięciu GNOME/GDM system startuje w trybie tekstowym (tty1). 
W `~/.profile` znajduje się blok:
```bash
if [[ -z $DISPLAY ]] && [[ $(tty) == /dev/tty1 ]]; then
    exec sway
fi
```
Logowanie na tty1 automatycznie uruchamia Sway.

**Waybar i sterowanie systemem**
Z powodu konfliktów sprzętowych z klawiszami funkcyjnymi (XF86) na poziomie jądra/Waylanda, zrezygnowano z bindowania mediów w konfigu Sway. Sterowanie przeniesiono na pasek Waybar:
* **Audio (`wireplumber`)**: Natywny moduł z wbudowaną obsługą przewijania (scroll) do płynnej regulacji głośności. Kliknięcie wywołuje `audio-menu.sh` (Wofi) do zarządzania urządzeniami/BT.
* **Jasność (`custom/brightness`)**: Moduł autorski oparty na `brightnessctl`. Obsługuje zdarzenia `on-scroll-up` i `on-scroll-down`. Kliknięcie wywołuje `brightness-menu.sh` (Wofi z `--allow-input`) do ręcznego wpisania wartości.
* **Sieć / Bateria**: Zewnętrzne skrypty bash oparte na Wofi (`wifi-menu.sh`, `power-menu.sh`).

Odświeżanie konfiguracji Waybara bez restartu WM: `killall -SIGUSR2 waybar`.

**Wofi**
Działa jako launcher (`wofi --show drun`) i używany jest w trybie `--dmenu` we wszystkich skryptach Waybara. Posiada własny plik `style.css`.

**Foot**
Pastelowy schemat kolorów (foreground `#a7a2a8`, background `#241b2a`).

## Manifest
Szczegółowy opis wszystkich zainstalowanych paczek, wersji plików konfiguracyjnych i potwierdzonych zachowań znajduje się w pliku `sway/manifest.yaml`.

## Cel projektu
* Użyteczny, minimalistyczny desktop na Debianie 13.
* Dokumentacja procesu dla osób przechodzących z ciężkich środowisk na Sway i architekturę PipeWire/WirePlumber.
* Publiczny przykład pełnego setupu opartego na konfiguracji skryptowej z pominięciem błędów warstwy sprzętowej jądra.

## ChatGPT w Chromium

Dla ChatGPT dostępny jest osobny launcher Chromium z odseparowanym katalogiem profilu użytkownika. Pozwala to trzymać sesję ChatGPT poza głównym profilem przeglądarki bez publikowania lokalnych szczegółów konfiguracji systemu.

Launcher znajduje się w `desktop-files/chromium-chatgpt.desktop` i powinien trafić do `~/.local/share/applications/chromium-chatgpt.desktop`. Po instalacji pojawia się w Wofi jako `ChatGPT Chromium`.
