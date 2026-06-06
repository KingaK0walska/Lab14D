## Autor : Kinga Kowalska

## Opis
W ramach zadania zbudowano prosty plik docker-compose.yml, który uruchamia stack LEMP wraz z phpMyAdmin. Aplikacja zawiera cztery kontenery: Nginx, PHP(PHP-FPM), MySQL, phpMyAdmin.

## Użyte polecenia i ich wyniki

1. **Uruchomienie środowiska w tle:**
   Polecenie: `docker compose up -d`

2. **Weryfikacja działających kontenerów:**
   Polecenie: `docker compose ps`

3. **Weryfikacja sieci:**
   Sieci zostały stworzone poprawnie, co można sprawdzić używając:
   `docker network ls`

Wyniki
![Zrzut ekranu](ss/docker.png)

## Dowody poprawnego działania

* **Strona startowa PHP (Nginx na porcie 4001):** 
  Strona odpowiada pod adresem `http://localhost:4001` wyświetlając komunikat "Stack LEMP działa!" oraz informację o wersji php. 
  ![Zrzut ekranu](ss/localhost4001.png)

* **Dostęp do phpMyAdmin (Port 6001) i inicjalizacja bazy:**
  Aplikacja jest dostępna pod adresem `http://localhost:6001`. Dzięki odpowiednim zmiennym środowiskowym przekazanym w pliku docker-compose.yaml, proces uwierzytelniania na konto administratora (root) odbywa się całkowicie automatycznie. Po załadowaniu interfejsu od razu widoczna jest zainicjowana testowa_baza.
  ![Zrzut ekranu](ss/localhost6001.png)
  Baza zainicjalizowana ręcznie
  ![Zrzut ekranu](ss/nowa_baza.png)


## Uzasadnienie przypisania sieci dla phpMyAdmin
Zgodnie z założeniami technicznymi, poszczególne kontenery zostały przypisane do sieci w następujący sposób:
* **`lemp_nginx`**: Podłączony do sieci `frontend` oraz `backend`. Jako jedyny przyjmuje bezpośredni ruch HTTP z zewnątrz i przekazuje go dalej.
* **`lemp_php`** oraz **`lemp_mysql`**: Ulokowane wyłącznie w odizolowanej sieci `backend`. Blokuje to bezpośredni dostęp do bazy danych i interpretera z Internetu.
* **`lemp_phpmyadmin`**: Kontener ten został przypisany **wyłącznie do sieci `backend`**. Ponieważ jego jedyną funkcją systemową jest zarządzanie bazą danych MySQL, musi on mieć możliwość bezpośredniej komunikacji sieciowej z kontenerem `lemp_mysql`. Wykorzystanie mapowania portów (`ports: - "6001:80"`) pozwala na bezpieczne udostępnienie interfejsu graficznego użytkownikowi bezpośrednio na porcie hosta, eliminując potrzebę wprowadzania tego kontenera do sieci `frontend`. Podnosi to bezpieczeństwo i zachowuje klarowny podział logiczny infrastruktury.
