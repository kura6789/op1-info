# Op1 · Strażnik Opinii

Informacja o aplikacji korzystającej z **Allegro REST API**.

Op1 to wewnętrzne narzędzie sprzedawcy do monitorowania ocen produktów we własnych ofertach na Allegro. Aplikacja **wyłącznie odczytuje dane** i niczego nie zmienia na koncie.

| | |
|---|---|
| **Nazwa** | Op1 (Strażnik Opinii) |
| **Wersja** | 1.0.0 |
| **User-Agent** | `Op1/1.0.0 (+https://TWOJ-LOGIN.github.io/op1-info/)` |
| **Typ** | aplikacja serwerowa, autoryzacja `device_code` |
| **Dostęp** | tylko odczyt (read-only) |
| **Użytkownicy** | wyłącznie właściciel konta sprzedawcy; aplikacja nie jest udostępniana ani sprzedawana innym podmiotom |

## Do czego służy

Op1 sprawdza, czy w ofertach sprzedawcy pojawiły się nowe oceny produktów. Porównuje średnią i rozkład gwiazdek z poprzednim pomiarem. Gdy przybędzie ocen lub średnia spadnie, wyświetla powiadomienie w wewnętrznym panelu sprzedawcy, żeby można było szybko zareagować na problem z produktem.

1. **Odczyt ofert:** lista aktywnych ofert sprzedawcy i przypisanych do nich produktów.
2. **Odczyt ocen:** średnia, rozkład 1–5★ i liczba ocen produktu w każdej ofercie.
3. **Porównanie:** wykrycie nowych ocen i spadków średniej, powiadomienie w panelu.

## Uprawnienia (scope)

| Scope | Po co |
|---|---|
| `allegro:api:sale:offers:read` | Odczyt listy ofert, produktu przypisanego do oferty i oceny produktu |

Aplikacja nie korzysta z żadnych uprawnień zapisu, zamówień, płatności ani wiadomości.

## Wywoływane zasoby i częstotliwość

| Zasób | Cel | Jak często |
|---|---|---|
| `GET /sale/offers` | Lista aktywnych ofert sprzedawcy (stronicowanie po 1000) | Raz na cykl, co 15 min |
| `GET /sale/offers/{offerId}/rating` | Średnia, rozkład i liczba ocen produktu | Raz na produkt w cyklu |
| `GET /sale/product-offers/{offerId}` | Identyfikator produktu przypisanego do oferty | Raz na ofertę, odświeżane co 7 dni |
| `POST /auth/oauth/device`, `POST /auth/oauth/token` | Autoryzacja właściciela konta i odświeżanie tokenu | Przy logowaniu i po wygaśnięciu tokenu |

## Obciążenie i limity

- Jeden cykl sprawdzania co 15 minut. Zapytania w cyklu wysyłane są kolejno, bez równoległych połączeń.
- Identyfikatory produktów są przechowywane lokalnie, więc ponowne zapytania o ofertę odbywają się najwyżej raz na 7 dni.
- Po odpowiedzi `429` aplikacja czeka zgodnie z nagłówkiem `Retry-After` i ponawia zapytanie najwyżej 3 razy.
- Każde zapytanie zawiera nagłówek User-Agent podany powyżej. Bez poprawnego nagłówka aplikacja nie łączy się z API.

## Dane i bezpieczeństwo

- Tokeny dostępu i pobrane dane są przechowywane wyłącznie na serwerze sprzedawcy, w lokalnej bazie danych.
- Dane z Allegro REST API nie są przekazywane ani sprzedawane osobom trzecim.
- Panel aplikacji jest chroniony hasłem i dostępny tylko dla sprzedawcy.
- Aplikacja nie przetwarza danych osobowych kupujących z zamówień.
- Treść publicznych recenzji produktów, widocznych dla każdego na stronach Allegro, może być pobierana osobnym modułem. Moduł ten nie używa klucza ani tokenu Allegro REST API.

## Historia wersji

| Wersja | Data | Zmiany |
|---|---|---|
| 1.0.0 | 2026-10-23 | Pierwsza wersja: monitoring ocen produktów we własnych ofertach, powiadomienia w panelu |

## Kontakt

W sprawach dotyczących działania aplikacji prosimy o kontakt przez dane właściciela konta sprzedawcy, do którego przypisana jest aplikacja Op1.
