Skip to content
mcmodo
Akitu_polaczenia_plugin
Repository navigation
Code
Issues
Pull requests
Agents
Actions
Projects
Wiki
Security and quality
Insights
Settings
Owner avatar
Akitu_polaczenia_plugin
Public
mcmodo/Akitu_polaczenia_plugin
Go to file
t
T
Name		
mcmodo
mcmodo
Delete index.html
b7adace
 · 
38 minutes ago
LICENSE
Initial commit
49 minutes ago
README.md
Enhance README with plugin details and usage
45 minutes ago
Repository files navigation
README
MIT license
Akitu Polaczenia Plugin (QGIS)
Wtyczka QGIS do zarządzania połączeniami sieciowymi w PostgreSQL/PostGIS z użyciem dedykowanego formularza Qt Designer.

Co robi wtyczka
Wtyczka umożliwia:

wskazanie Punktu Elastyczności (PE) bezpośrednio na mapie,
dynamiczne filtrowanie pól formularza (QComboBox),
dodawanie, edycję i usuwanie połączeń światłowodowych,
przypisywanie połączeń do tras logicznych,
podgląd danych w tabelach dla każdej zakładki,
zapis zmian przyciskiem Apply bez zamykania formularza.
Obsługiwane typy połączeń:

Włókno–Włókno
Włókno–Spliter
Włókno–Patchcord
Patchcord–Patchcord
Patchcord–Włókno
Wymagania
QGIS 4.x
PostgreSQL + PostGIS
Sposób użycia (skrót)
Aktywuj wtyczkę w QGIS.
Otwórz formularz „Akitu Polaczenia”.
Wskaż PE na mapie (punkty_elastycznosci).
Uzupełnij dane w wybranej zakładce.
Kliknij Apply, aby zapisać rekord.
Użyj Delete record, aby usunąć zaznaczony wpis.
Zakończ pracę przyciskiem OK lub Anuluj.
Ważne informacje
Warstwa punkty_elastycznosci powinna być załadowana i posiadać pe_id (opcjonalnie pe_name).
Wtyczka korzysta z połączeń bazodanowych zdefiniowanych w QGIS.
Formularz .ui musi znajdować się w katalogu wtyczki.
Status i dostęp
Kod źródłowy projektu pozostaje w repozytorium prywatnym. Ta strona zawiera publiczny opis funkcjonalności i sposobu użycia.

Kontakt
Autor: Karol Orzepowski
Data opisu: 3 czerwca 2025

About
No description, website, or topics provided.
Resources
 Readme
License
 MIT license
 Activity
Stars
 0 stars
Watchers
 0 watching
Forks
 0 forks
Releases
No releases published
Create a new release
Deployments
2
 github-pages 37 minutes ago
Packages
No packages published
Publish your first package
Contributors
1
@mcmodo
mcmodo
Footer
© 2026 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Community
Docs
Contact
Manage cookies
Do not share my personal information
