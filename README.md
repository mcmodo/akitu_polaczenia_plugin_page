<img width="70" height="70" alt="icon" src="https://github.com/user-attachments/assets/5c1ea8e9-12b2-41d2-a97a-69094a1d297b" />

# Akitu Połączenia Plugin — Instrukcja użytkownika
 
Szczegółowy opis formularza wtyczki „Akitu Polaczenia” dla QGIS: pasek narzędzi oraz każda z 7 zakładek połączeń światłowodowych.
 
## Spis treści
- [1. Wprowadzenie](#1-wprowadzenie)
- [2. Wymagania i warstwy wymagane](#2-wymagania-i-warstwy-wymagane)
- [3. Pasek narzędzi formularza](#3-pasek-narzędzi-formularza)
- [4. Zakładka: Włókno–Włókno](#4-zakładka-włókno–włókno)
- [5. Zakładka: Splitter (Włókno/Pigtail–Splitter)](#5-zakładka-splitter-włóknopigtail–splitter)
- [6. Zakładka: Włókno–Pigtail](#6-zakładka-włókno–pigtail)
- [7. Zakładka: Pigtail–Pigtail](#7-zakładka-pigtail–pigtail)
- [8. Zakładka: Pigtail–Włókno](#8-zakładka-pigtail–włókno)
- [9. Zakładka: Pigtail–Device](#9-zakładka-pigtail–device)
- [10. Zakładka: Device–Device](#10-zakładka-device–device)
- [11. Wspólne zasady walidacji i statusy](#11-wspólne-zasady-walidacji-i-statusy)
- [12. Rozwiązywanie problemów](#12-rozwiązywanie-problemów)
---
 
## 1. Wprowadzenie
 
Formularz wtyczki (`akitu_polaczenia.ui`) to jedno okno dialogowe z 7 zakładkami (`QTabWidget`). Każda zakładka odpowiada jednemu typowi połączenia fizycznego w sieci światłowodowej i zapisuje rekordy do osobnej tabeli w bazie PostgreSQL/PostGIS. Wszystkie zakładki działają w kontekście jednego, aktualnie wybranego **Punktu Elastyczności (PE)** — patrz [rozdział 3](#3-pasek-narzędzi-formularza).
 

## 2. Wymagania i warstwy wymagane
 
Przy starcie formularz sprawdza obecność w projekcie QGIS poniższych warstw wraz z wymaganymi polami (jeśli którejś brakuje, formularz się nie otworzy):
 
| Warstwa | Wymagane pola |
|---|---|
| `punkty_elastycznosci` | `pe_id`, `pe_name` |
| `linie_kablowe` | `lk_id`, `lk_name`, `pe_id` |
| `wlokno` | `wl_id`, `wl_nr`, `lk_id`, `pe_id`, `sp_id`, `wl_status` |
| `splitery` | `sp_id`, `sp_name`, `pe_id`, `sp_typ` |
| `pigtail` | `pig_id`, `pig_name`, `pig_type`, `pe_id`, `pig_status` |
| `porty` | `por_id`, `por_name_nr`, `por_type`, `por_status` |
| `pole_komutacyjne` | `pk_id`, `pk_nr`, `pe_id` |
| `slot_nr` | `snr_id`, `snr_slot_nr`, `pk_id`, `snr_status` |
| `wlokno_wlokno` | `ww_id`, `pe_id`, `wl_a_id`, `wl_b_id`, `snr_id` |
| `wlokno_pigtail` | `wp_id`, `pe_id`, `wl_id`, `pig_id`, `snr_id` |
| `pigtail_pigtail` | `pp_id`, `pe_id`, `pig_id_a`, `pig_id_b`, `snr_id` |
| `pigtail_wlokno` | `pw_id`, `pe_id`, `pig_id`, `wl_id`, `snr_id` |
| `pigtail_port` | `ppor_id`, `pig_id`, `por_id`, `pe_id` |
| `device` | `dev_id`, `dev_name`, `pe_id`, `dev_type` |
| `patchcord` | `patch_id`, `patch_name`, `patch_type`, `pe_id`, `patch_status` |
| `spliter_wlokno_pigtail` | `swp_id`, `wl_id`, `pig_id`, `snr_id`, `pe_id`, `sp_id` |
 
**[⬆ powrót do spisu treści](#spis-treści)**
 

## 3. Pasek narzędzi formularza
 
Górna część okna, wspólna dla wszystkich zakładek:
 
| Kontrolka | Nazwa w `.ui` | Działanie |
|---|---|---|
| **Wskaż Punkt Elastyczności na mapie** | `btnPickPE` | Aktywuje narzędzie identyfikacji na mapie — kliknięcie obiektu w warstwie `punkty_elastycznosci` ustawia bieżące PE dla całego formularza, odświeża wszystkie listy rozwijane i tabele. |
| **Odśwież** | `btnRefresh` | Przeładowuje dane warstw (bez naruszania warstw w trybie edycji), odświeża listy `QComboBox`, modele tabel i cache zapytań. Użyj po zmianach wykonanych poza wtyczką. |
| **Generuj schemat połączeń dla PE** | `pushButton_rozplyw` | Otwiera okno ze schematem (SVG) rozpływu połączeń światłowodowych dla aktualnie wybranego PE. Wymaga wskazanego PE. |
| **[nazwa PE]** | `pe_name` (etykieta) | Wyświetla nazwę aktualnie wybranego PE (lub jego identyfikator, jeśli brak nazwy). |
| **GitHub Docs** | `pushButton` | Otwiera w domyślnej przeglądarce publiczną stronę dokumentacji wtyczki: `https://mcmodo.github.io/akitu_polaczenia_plugin_page/`. |
| **OK / Anuluj** | `Form_buttonBox` | Zamyka formularz (odpowiednio: akceptuje / odrzuca). |
 
> Bez wskazania PE (`btnPickPE`) większość pól i przycisków `Apply` na zakładkach jest nieużywalna — formularz zwróci komunikat „Wskaż Punkt Elastyczności”.
 
**[⬆ powrót do spisu treści](#spis-treści)**
 

## 4. Zakładka: Włókno–Włókno
 
*Nazwa zakładki: „Włókno-Włókno” · obiekt `.ui`: `tabWW`*
 
Łączy ze sobą dwa końce włókna (spawanie/łączenie bezpośrednie) w obrębie tacki spawów.
 
| Pole | Kontrolka | Opis |
|---|---|---|
| Włókno A (From) → Linia kablowa | `WW_lk_name` | Wybór linii kablowej w bieżącym PE, filtruje listę numerów włókna. |
| Włókno A (From) → Numer Włókna | `WW_wl_nr` | Numer konkretnego włókna wybranej linii kablowej. |
| Włókno B (To) → Linia kablowa | `WW_lk_name2` | Jak wyżej, dla drugiej strony połączenia. |
| Włókno B (To) → Numer Włókna | `WW_wl_nr2` | Jak wyżej, dla drugiej strony połączenia. |
| Nr zaszeregowania w PE | `WW_pk_nr` | Numer pola komutacyjnego (tacki spawów) w PE. |
| Numer slotu | `WW_snr_slot_nr` | Wolny slot w wybranej tacce spawów. |
 
**Tabela podglądu:** `tableViewWW` (zawartość tabeli `wlokno_wlokno`).
 
**Apply (`btnWW_apply`):** wymaga wybrania włókna A, włókna B i slotu. Blokuje próbę połączenia tego samego końca włókna z samym sobą oraz duplikaty połączenia A↔B/B↔A w tym PE. Po zapisie ustawia status obu włókien na `zaj` (zajęte) i status slotu na `zaj`.
 
**Delete record (`btnWW_delete`):** usuwa zaznaczony rekord z tabeli `wlokno_wlokno`.
 
**[⬆ powrót do spisu treści](#spis-treści)**
 

## 5. Zakładka: Splitter (Włókno/Pigtail–Splitter)
 
*Nazwa zakładki: „Splitter” · obiekt `.ui`: `tabWS`*
 
Podłącza wejście splittera (`wl_nr = 0`) do włókna liniowego albo — jeśli splitter jest stacyjny — do pigtaila. Tryb wybiera się przełącznikiem:
 
| Kontrolka | Opis |
|---|---|
| `radioButton_SP` — „Jeżeli splitter stacyjny” | **Niezaznaczony (domyślnie):** tryb WŁÓKNO — wejście splittera podłączane jest do włókna liniowego (pola `WS_lk_name` / `WS_wl_nr`). **Zaznaczony:** tryb PIGTAIL — wejście splittera podłączane jest do pigtaila (pola `WS_pig_name` / `WS_pig_type`). |
| Splitter name | `WS_sp_name` — wybór splittera w bieżącym PE. |
| Nr zaszeregowania w PE | `WS_pk_nr` — pole komutacyjne / tacka spawów. |
| Numer slotu | `WS_snr_slot_nr` — wolny slot. |
 
**Tabela podglądu:** `tableViewWS` (zawartość tabeli `spliter_wlokno_pigtail`).
 
**Apply (`btnWS_apply`):** zależnie od trybu zapisuje rekord z `pig_id` (tryb pigtail) lub z `pig_id = NULL` (tryb włókno). Zawsze sprawdza, czy wejście splittera (`wl_nr = 0`) nie jest już zajęte, czy dany pigtail/włókno nie jest już podłączone gdzie indziej oraz czy wybrany slot jest wolny w tym splitterze. Po zapisie aktualizuje status pigtaila (`spaw`) lub włókna (`zaj`) oraz status slotu (`zaj`).
 
**Delete record (`btnWS_delete`):** usuwa zaznaczony rekord z tabeli `spliter_wlokno_pigtail`.
 
**[⬆ powrót do spisu treści](#spis-treści)**
 

## 6. Zakładka: Włókno–Pigtail
 
*Nazwa zakładki: „Włókno-Pigtail” · obiekt `.ui`: `tabWP`*
 
Łączy włókno liniowe z pigtailem na tacce spawów.
 
| Pole | Kontrolka | Opis |
|---|---|---|
| Włókno → Linia kablowa | `WP_lk_name` | Wybór linii kablowej w PE. |
| Włókno → Numer Włókna | `WP_wl_nr` | Numer włókna wybranej linii. |
| Pigtail → Typ pigtailu | `WP_pig_type` | Typ złącza pigtaila. |
| Pigtail → Pigtail name | `WP_pig_name` | Konkretny pigtail (filtrowany po typie). |
| Nr zaszeregowania w PE | `WP_pk_nr` | Pole komutacyjne / tacka spawów. |
| Numer slotu | `WP_snr_slot_nr` | Wolny slot. |
 
**Tabela podglądu:** `tableViewWP` (zawartość tabeli `wlokno_pigtail`).
 
**Apply (`btnWP_apply`):** wymaga wybrania włókna, pigtaila i slotu. Po zapisie ustawia status włókna na `zaj`, status pigtaila na `spaw` i status slotu na `zaj`.
 
**Delete record (`btnWP_delete`):** usuwa zaznaczony rekord z tabeli `wlokno_pigtail`.
 
**[⬆ powrót do spisu treści](#spis-treści)**
 

## 7. Zakładka: Pigtail–Pigtail
 
*Nazwa zakładki: „Pigtail-Pigtail” · obiekt `.ui`: `tabPP`*
 
Łączy dwa pigtaile bezpośrednio (np. w polu komutacyjnym), bez udziału włókna liniowego.
 
| Pole | Kontrolka | Opis |
|---|---|---|
| Pigtail A → Typ złącza | `PP_pig_type1` | Typ złącza pierwszego pigtaila. |
| Pigtail A → Pigtail name | `PP_pig_name1` | Wybór pierwszego pigtaila. |
| Pigtail B → Typ złącza | `PP_pig_type2` | Typ złącza drugiego pigtaila. |
| Pigtail B → Pigtail name | `PP_pig_name2` | Wybór drugiego pigtaila. |
| Nr zaszeregowania w PE | `PP_pk_nr` | Pole komutacyjne. |
| Numer slotu | `PP_snr_slot_nr` | Wolny slot. |
 
**Tabela podglądu:** `tableViewPP` (zawartość tabeli `pigtail_pigtail`).
 
**Apply (`btnPP_apply`):** wymaga obu pigtaili i slotu, blokuje wybór tego samego pigtaila po obu stronach, wymaga aby oba pigtaile miały status `spaw`, i blokuje duplikaty A↔B/B↔A w tym PE. Po zapisie ustawia status obu pigtaili na `zaj` i status slotu na `zaj`.
 
**Delete record (`btnPP_delete`):** usuwa zaznaczony rekord z tabeli `pigtail_pigtail`.
 
**[⬆ powrót do spisu treści](#spis-treści)**
 

## 8. Zakładka: Pigtail–Włókno
 
*Nazwa zakładki: „Pigtail-Włókno” · obiekt `.ui`: `tabPW`*
 
Odwrotność zakładki Włókno–Pigtail — łączy pigtail z włóknem liniowym.
 
| Pole | Kontrolka | Opis |
|---|---|---|
| Pigtail A → Typ złącza | `PW_pig_type` | Typ złącza pigtaila. |
| Pigtail A → Pigtail name | `PW_pig_name` | Wybór pigtaila. |
| Włókno B → Linia kablowa | `PW_lk_name` | Linia kablowa w PE. |
| Włókno B → Numer Włókna | `PW_wl_nr` | Numer włókna wybranej linii. |
| Nr zaszeregowania w PE | `PW_pk_nr` | Pole komutacyjne / tacka spawów. |
| Numer slotu | `PW_snr_slot_nr` | Wolny slot. |
 
**Tabela podglądu:** `tableViewPW` (zawartość tabeli `pigtail_wlokno`).
 
**Apply (`btnPW_apply`):** wymaga włókna, pigtaila i slotu. Po zapisie ustawia status włókna na `zaj`, status pigtaila na `spaw` i status slotu na `zaj`.
 
**Delete record (`btnPW_delete`):** usuwa zaznaczony rekord z tabeli `pigtail_wlokno`.
 
**[⬆ powrót do spisu treści](#spis-treści)**

 
## 9. Zakładka: Pigtail–Device
 
*Nazwa zakładki: „Pigtail-Device” · obiekt `.ui`: `tabPD`*
 
Podłącza pigtail bezpośrednio do portu urządzenia (`device`).
 
| Pole | Kontrolka | Opis |
|---|---|---|
| Device port → Device | `PD_device_name` | Wybór urządzenia w PE. |
| Device port → Port type | `PD_porty_type` | Typ portu urządzenia. |
| Device port → Port name | `PD_porty_name` | Konkretny port (filtrowany po typie). |
| Pigtail → Linia kablowa lub splitter | `PD_lk_name` | Źródło pigtaila (linia kablowa lub splitter). |
| Pigtail → Pigtail name | `PD_pig_name` | Wybór pigtaila. |
 
**Tabela podglądu:** `tableViewPD` (zawartość tabeli `pigtail_port`).
 
**Apply (`btnPD_apply`):** wymaga pigtaila i portu, blokuje duplikat tego samego połączenia w danym PE. Po zapisie ustawia status pigtaila na `zaj` i status portu na `zaj`.
 
**Delete record (`btnPD_delete`):** usuwa zaznaczony rekord z tabeli `pigtail_port`.
 
**[⬆ powrót do spisu treści](#spis-treści)**

 
## 10. Zakładka: Device–Device
 
*Nazwa zakładki: „Device-Device” · obiekt `.ui`: `tabDD`*
 
Łączy dwa urządzenia patchcordem między portami. Urządzenie B może pochodzić spoza bieżącego PE.
 
| Pole | Kontrolka | Opis |
|---|---|---|
| Device port_A → Device | `DD_device_name_A` | Urządzenie A (w bieżącym PE). |
| Device port_A → Port type | `DD_porty_type_A` | Typ portu urządzenia A. |
| Device port_A → Port name | `DD_porty_name_A` | Port urządzenia A. |
| Device port_B → Device | `DD_device_name_B` | Urządzenie B (w bieżącym PE) — alternatywnie wybierz przyciskiem poniżej. |
| Device port_B → Port type | `DD_porty_type_B` | Typ portu urządzenia B. |
| Device port_B → Port name | `DD_porty_name_B` | Port urządzenia B. |
| Jeżeli poza PE | `btnPickDevice` | Aktywuje wskazanie na mapie urządzenia **spoza** bieżącego PE jako Device B (warstwa `device`). |
| Patchcord → Patch. type | `DD_patch_type` | Typ patchcordu. |
| Patchcord → Patch name | `DD_patch_name` | Konkretny patchcord użyty do połączenia. |
 
**Tabela podglądu:** `tableViewDD` (zawartość tabeli `device_device`).
 
**Apply (`btnDD_apply`):** wymaga portu A, portu B i patchcordu; blokuje wybór tego samego portu po obu stronach. Oba porty muszą mieć status `wol` (wolny), a wybrany patchcord musi mieć status `wol`. Blokuje duplikat połączenia A↔B/B↔A w tym PE.
 
**Delete record (`btnDD_delete`):** usuwa zaznaczony rekord z tabeli `device_device`.
 
**[⬆ powrót do spisu treści](#spis-treści)**

 
## 11. Wspólne zasady walidacji i statusy
 
- **Bieżące PE jest wymagane** dla każdej operacji zapisu — wskaż je przyciskiem `Wskaż Punkt Elastyczności na mapie`.
- **Duplikaty są blokowane**: formularz sprawdza istniejące połączenia w obu kierunkach (A↔B oraz B↔A) w obrębie tego samego PE, zanim zapisze nowy rekord.
- **Statusy elementów** (`wl_status`, `pig_status`, `por_status`, `snr_status`, `patch_status`) są aktualizowane automatycznie po udanym `Apply`. Najczęściej spotykane wartości: `wol` (wolny/dostępny), `zaj` (zajęty), `spaw` (zaspawany/podłączony pigtail).
- **Zajęte elementy nie znikają z formularza automatycznie** — po każdym `Apply` listy `QComboBox` są czyszczone (`_clear_comboboxes_after_apply`), a dane odświeżane (`refresh_data` / `update_all_table_views` / `populate_all_comboboxes`), by odzwierciedlić nowy stan.
- **Błędy zapisu** (np. utrata połączenia z bazą, brak warstwy) są zgłaszane komunikatem i powodują wycofanie transakcji (`rollBack`) — żaden częściowy rekord nie zostaje zapisany.
  
**[⬆ powrót do spisu treści](#spis-treści)**

 
## 12. Rozwiązywanie problemów
 
| Objaw | Prawdopodobna przyczyna | Rozwiązanie |
|---|---|---|
| Formularz nie otwiera się / komunikat o brakującej warstwie | W projekcie QGIS brakuje jednej z wymaganych warstw (zob. [rozdział 2](#2-wymagania-i-warstwy-wymagane)) lub brakuje w niej wymaganego pola | Dodaj/popraw warstwę i jej pola, uruchom wtyczkę ponownie |
| „Wskaż Punkt Elastyczności” mimo kliknięcia na mapie | Kliknięto poza obiektem warstwy `punkty_elastycznosci` | Kliknij dokładnie na punkt PE na mapie |
| Listy rozwijane są puste | Nie wskazano PE lub dane w bazie zostały zmienione poza wtyczką | Wskaż PE ponownie / kliknij `Odśwież` |
| „Takie połączenie już istnieje” | Rekord A↔B (lub B↔A) już jest zapisany dla tego PE | Sprawdź tabelę podglądu na danej zakładce, usuń istniejący rekord jeśli to pomyłka |
| „Wybrane porty/pigtail'e muszą mieć status …” | Element jest już zajęty (status inny niż wymagany, np. `wol`/`spaw`) | Wybierz inny, wolny element lub usuń kolidujące połączenie |
| Przycisk `GitHub Docs` nic nie robi | Brak domyślnej przeglądarki w systemie lub blokada `QDesktopServices` | Otwórz ręcznie: `https://mcmodo.github.io/akitu_polaczenia_plugin_page/` |
 
**[⬆ powrót do spisu treści](#spis-treści)**
 
---
Autor: **Karol Orzepowski**
