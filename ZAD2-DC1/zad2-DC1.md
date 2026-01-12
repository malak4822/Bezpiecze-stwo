# DC-1

## 1. Rozpoznanie 

Pierwszym etapem było zidentyfikowanie adresu IP maszyny w sieci lokalnej oraz sprawdzenie otwartych portów.

### Skanowanie portów
Wykonano skanowanie narzędziem **Nmap** z flagą `-sV` w celu identyfikacji wersji usług:
`nmap -sV [IP_OFIARY]`

![Wynik skanowania Nmap](image.png)

Wynik skanowania ujawnił otwarty port **80 (HTTP)** działający na serwerze Apache oraz informację o systemie CMS: **Drupal 7**.

### Analiza podatności webowych
W celu potwierdzenia wersji i znalezienia potencjalnych błędów konfiguracyjnych użyto skanera **Nikto**:
`nikto -h [IP_OFIARY]`

![Skanowanie Nikto](image-1.png)

Narzędzie potwierdziło, że mamy do czynienia z przestarzałą wersją Drupala 7, co sugeruje wysoką podatność na znane ataki.

---

## 2. Eksploitacja - Uzyskanie dostępu do panelu

Przeszukano bazę exploitów pod kątem Drupala 7. Znaleziono podatność pozwalającą na wstrzyknięcie SQL lub zdalne wykonanie kodu (Drupalgeddon).

![Wyszukiwanie exploitów](image-2.png)

Wybrano exploit pozwalający na utworzenie nowego konta administratora poprzez manipulację bazą danych (SQLi). Uruchomienie skryptu:

![Uruchomienie exploita SQLi](image-3.png)

Atak zakończył się sukcesem. Hasło administratora zostało zresetowane/dodano nowego użytkownika, co pozwoliło na zalogowanie się do panelu webowego.

![Sukces exploita](image-4.png)

### Zdobycie Flagi nr 3
Po zalogowaniu się do panelu administracyjnego (GUI), w sekcji **Dashboard** odnaleziono jedną z flag widoczną tylko dla zalogowanych użytkowników.

![Flaga nr 3 w Dashboardzie](image-11.png)

---

## 3. Eskalacja do powłoki systemowej

Dostęp przez stronę WWW był niewystarczający. W celu uzyskania dostępu do terminala (CLI) zdecydowano się użyć exploita **EDB-44449** (Ruby), który pozwala na zdalne wykonanie kodu (Remote Code Execution).

### Problemy z zależnościami
Podczas próby uruchomienia exploita wystąpił błąd związany z brakiem odpowiedniej biblioteki w środowisku Ruby:

![Błąd uruchomienia exploita](image-5.png)

Analiza błędu wykazała, że zainstalowana wersja Ruby jest zbyt nowa i brakuje kompatybilnej wersji biblioteki `highline`.

![Analiza błędu Ruby](image-6.png)

**Rozwiązanie:** Ręczna instalacja starszej wersji biblioteki `highline`:
`sudo gem install highline -v 2.1.0`

![Naprawa zależności gem](image-7.png)

### Uzyskanie powłoki 
Po naprawieniu zależności, exploit został uruchomiony ponownie, celując w maszynę ofiary. Tym razem skrypt zadziałał poprawnie, otwierając sesję powłoki.

![Uzyskanie dostępu CLI](image-8.png)

---

## 4. Post-eksploitacja

Będąc w systemie, przeprowadzono rekonesans katalogów. W głównym katalogu serwera WWW (`/var/www`) odnaleziono plik `flag1.txt`.

**Treść flagi nr 1:**
![Treść flagi nr 1](image-10.png)

No i koniec