# Eventownik Solvro – Mobile Backend Legacy

> [!WARNING]
> To repozytorium zawiera backend dla starej wersji aplikacji mobilnej. Nowa wersja aplikacji rozwijana jest w repozytorium [mobile-eventownik-v2](https://github.com/Solvro/mobile-eventownik-v2).

<div align="center">

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![Django](https://img.shields.io/badge/django-%23092E20.svg?style=for-the-badge&logo=django&logoColor=white)
![Django REST Framework](https://img.shields.io/badge/DRF-ff1709?style=for-the-badge&logo=django&logoColor=white)
![Celery](https://img.shields.io/badge/celery-%2337814A.svg?style=for-the-badge&logo=celery&logoColor=white)
![Redis](https://img.shields.io/badge/redis-%23DD0031.svg?style=for-the-badge&logo=redis&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/postgres-%23316192.svg?style=for-the-badge&logo=postgresql&logoColor=white)
![Firebase](https://img.shields.io/badge/firebase-%23039BE5.svg?style=for-the-badge&logo=Firebase&logoColor=white)

</div>

## Uruchomienie lokalne

### Wymagania

- [Python 3.8+](https://www.python.org/)
- pip
- Serwer Redis (broker dla Celery i warstwa kanałów WebSocket)

### Instalacja

1. **Sklonuj repozytorium**

   ```bash
   git clone https://github.com/Solvro/backend-mobile-eventownik.git
   cd backend-mobile-eventownik
   ```

2. **Zainstaluj zależności**

   ```bash
   pip install -r requirements.txt
   ```

3. **Zainstaluj pre-commit hooki**, aby zapewnić jakość kodu

   ```bash
   pre-commit install
   ```

4. **Utwórz plik `.env`** w katalogu głównym projektu i dodaj poniższe zmienne środowiskowe

   ```env
   ALLOWED_HOSTS='*'
   CORS_ALLOW_ALL_ORIGINS=True
   SECRET_KEY="secret_key_(change_this)"
   EMAIL_BACKEND=django.core.mail.backends.console.EmailBackend
   ```

   > Domyślnie projekt korzysta z SQLite. Aby połączyć się z PostgreSQL, ustaw dodatkowo `DB_ENGINE=django.db.backends.postgresql`, `DB_NAME`, `DB_USER`, `DB_HOST` (i pozostałe zmienne `DB_*`).
   >
   > Powiadomienia push wymagają `FIREBASE_CREDENTIALS_JSON` (JSON konta serwisowego Firebase) lub pliku `oboz-studentow-pwr-firebase-adminsdk.json`.

5. **Zastosuj migracje**

   ```bash
   python manage.py migrate
   ```

6. **Utwórz konto superużytkownika**

   ```bash
   python manage.py createsuperuser
   ```

   i postępuj zgodnie z instrukcjami.

## Uruchomienie

Uruchom serwer deweloperski:

```bash
python manage.py runserver
```

Serwer deweloperski będzie dostępny pod adresem `http://localhost:8000`. Przechodząc pod `http://localhost:8000/admin/`, możesz zalogować się do panelu administracyjnego.

## Zadania w tle (Celery)

Część funkcji (m.in. codzienne powiadomienia BeReal, przypomnienia o warsztatach) wymaga uruchomionego brokera Redis oraz workera Celery:

```bash
celery -A obozstudentowProject worker -B -l info
```

## Testy E2E

1. Dodaj poniższe zmienne środowiskowe do serwera API, aby wyłączyć limity (throttling) dla testów:

   ```env
   ANON_THROTTLE_RATE = 'None'
   USER_THROTTLE_RATE = 'None'
   ```

2. Testy end-to-end frontendu (Cypress) uruchamiane są z poziomu repozytorium [mobile-eventownik](https://github.com/Solvro/mobile-eventownik) - upewnij się, że serwer API działa równolegle.

## Dostępne skrypty

| Komenda                    | Opis                                                |
| --------------------------- | ---------------------------------------------------- |
| `python manage.py runserver` | Uruchamia lokalny serwer deweloperski              |
| `python manage.py migrate`   | Stosuje migracje bazy danych                        |
| `python manage.py createsuperuser` | Tworzy konto administratora                  |
| `pip install -r requirements.txt` | Instaluje wszystkie wymagane zależności       |
| `pre-commit install`         | Instaluje hooki formatujące/kontrolujące kod        |
| `celery -A obozstudentowProject worker -B -l info` | Uruchamia workera Celery wraz z beatem |

## Struktura projektu

- **obozstudentowProject/** – Konfiguracja projektu Django (ustawienia, ASGI/WSGI, routing, Celery, middleware).
- **obozstudentow/** – Główna aplikacja: modele, panel admina, API oraz zadania w tle.
- **obozstudentow_async/** – Moduły asynchroniczne (czat, zapisy do domów) oparte o Django Channels.
- **bereal/** – Moduł funkcji w stylu BeReal.
- **bingo/** – Moduł gry Bingo ([dokumentacja](bingo/readme.md)).
- **tinder/** – Moduł dopasowywania uczestników.
- **static/** – Statyczne pliki frontendowe.
- **templates/** – Szablony HTML.
- **docs/** – Dokumentacja architektury, modelu danych i endpointów API ([docs/README.md](docs/README.md)).
- **manage.py** – Główne narzędzie do zarządzania projektem Django.
- **requirements.txt** – Lista zależności Pythona.

## Stack technologiczny

### Backend

- **Framework:** [Django 5.1](https://www.djangoproject.com/)
- **API:** [Django REST Framework](https://www.django-rest-framework.org/)
- **Autoryzacja:** [djangorestframework-simplejwt](https://django-rest-framework-simplejwt.readthedocs.io/) (JWT)
- **WebSockets:** [Django Channels](https://channels.readthedocs.io/) (Daphne, ASGI)

### Zadania w tle

- **Kolejka zadań:** [Celery](https://docs.celeryq.dev/) + [django-celery-beat](https://django-celery-beat.readthedocs.io/)
- **Broker/cache:** [Redis](https://redis.io/)

### Dane i integracje

- **Baza danych:** PostgreSQL (produkcja) / SQLite (lokalnie)
- **Import/eksport danych:** [django-import-export](https://django-import-export.readthedocs.io/)
- **Powiadomienia push:** [Firebase Admin SDK](https://firebase.google.com/docs/admin/setup)

## Dokumentacja

Szczegółowa dokumentacja architektury, modelu danych i endpointów API znajduje się w katalogu [`docs/`](docs/README.md).

---

## Licencja

Projekt jest udostępniany na warunkach licencji **AGPL (GNU Affero General Public License)**.

---

<div align="center">

Stworzone przez [KN Solvro](https://github.com/Solvro) dla studentów Politechniki Wrocławskiej

</div>
