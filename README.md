# Akitu Polaczenia Plugin (QGIS) <img width="70" height="70" alt="icon" src="https://github.com/user-attachments/assets/5c1ea8e9-12b2-41d2-a97a-69094a1d297b" />

Wtyczka QGIS do zarządzania połączeniami sieciowymi w PostgreSQL/PostGIS z użyciem dedykowanego formularza Qt Designer.

## Co robi wtyczka

Wtyczka umożliwia:
- wskazanie Punktu Elastyczności (PE) bezpośrednio na mapie,
- dynamiczne filtrowanie pól formularza (QComboBox),
- dodawanie, edycję i usuwanie połączeń światłowodowych,
- przypisywanie połączeń do tras logicznych,
- podgląd danych w tabelach dla każdej zakładki,
- zapis zmian przyciskiem `Apply` bez zamykania formularza.

Obsługiwane typy połączeń:
- Włókno–Włókno
- Włókno–Spliter
- Włókno–Patchcord
- Patchcord–Patchcord
- Patchcord–Włókno

## Wymagania

- QGIS 4.x
- PostgreSQL + PostGIS

## Sposób użycia (skrót)

1. Aktywuj wtyczkę w QGIS.
2. Otwórz formularz „Akitu Polaczenia”.
3. Wskaż PE na mapie (`punkty_elastycznosci`).
4. Uzupełnij dane w wybranej zakładce.
5. Kliknij `Apply`, aby zapisać rekord.
6. Użyj `Delete record`, aby usunąć zaznaczony wpis.
7. Zakończ pracę przyciskiem `OK` lub `Anuluj`.

## Ważne informacje

- Warstwa `punkty_elastycznosci` powinna być załadowana i posiadać `pe_id` (opcjonalnie `pe_name`).
- Wtyczka korzysta z połączeń bazodanowych zdefiniowanych w QGIS.
- Formularz `.ui` musi znajdować się w katalogu wtyczki.

## Status i dostęp

Kod źródłowy projektu pozostaje w repozytorium prywatnym.
Ta strona zawiera publiczny opis funkcjonalności i sposobu użycia.

## Kontakt

Autor: **Karol Orzepowski**  
Data opisu: **3 czerwca 2025**
