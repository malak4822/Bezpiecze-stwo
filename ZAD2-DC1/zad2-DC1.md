# DC-1

**Repozytorium GitHub:** [https://github.com/malak4822/Bezpiecze-stwo](https://github.com/malak4822/Bezpiecze-stwo)

---

## 1. Rozpoznanie 

Pierwszym etapem było zidentyfikowanie adresu IP maszyny w sieci lokalnej oraz sprawdzenie otwartych portów.

### Skanowanie portów
Wykonano skanowanie narzędziem **Nmap** z flagą `-sV` w celu identyfikacji wersji usług:
`nmap -sV [IP_OFIARY]`

<div align="center">

![Rysunek 1: Wynik skanowania Nmap - otwarty port 80 (HTTP) oraz system CMS Drupal 7](image.png){width=80%}

</div>

Wynik skanowania ujawnił otwarty port **80 (HTTP)** działający na serwerze Apache oraz informację o systemie CMS: **Drupal 7**.

### Analiza podatności webowych
W celu potwierdzenia wersji i znalezienia potencjalnych błędów konfiguracyjnych użyto skanera **Nikto**:
`nikto -h [IP_OFIARY]`

<div align="center">

![Rysunek 2: Skanowanie Nikto - potwierdzenie przestarzałej wersji Drupal 7 i wykrytych podatności](image-1.png){width=80%}

</div>

Narzędzie potwierdziło, że mamy do czynienia z przestarzałą wersją Drupala 7, co sugeruje wysoką podatność na znane ataki.

---

## 2. Eksploitacja - Uzyskanie dostępu do panelu

Przeszukano bazę exploitów pod kątem Drupala 7. Znaleziono podatność pozwalającą na wstrzyknięcie SQL lub zdalne wykonanie kodu (Drupalgeddon).

<div align="center">

![Rysunek 3: Wyszukiwanie exploitów dla Drupal 7 - znalezienie podatności Drupalgeddon](image-2.png){width=80%}

</div>

Wybrano exploit pozwalający na utworzenie nowego konta administratora poprzez manipulację bazą danych (SQLi). Uruchomienie skryptu:

<div align="center">

![Rysunek 4: Uruchomienie exploita SQLi - wstrzyknięcie SQL w celu dodania konta administratora](image-3.png){width=80%}

</div>

Atak zakończył się sukcesem. Hasło administratora zostało zresetowane/dodano nowego użytkownika, co pozwoliło na zalogowanie się do panelu webowego.

<div align="center">

![Rysunek 5: Sukces exploita - potwierdzenie utworzenia/zmodyfikowania konta administratora](image-4.png){width=80%}

</div>

### Zdobycie Flagi nr 3
Po zalogowaniu się do panelu administracyjnego (GUI), w sekcji **Dashboard** odnaleziono jedną z flag widoczną tylko dla zalogowanych użytkowników.

<div align="center">

![Rysunek 6: Flaga nr 3 widoczna w Dashboardzie po zalogowaniu jako administrator](image-11.png){width=80%}

</div>

---

## 3. Eskalacja do powłoki systemowej

Dostęp przez stronę WWW był niewystarczający. W celu uzyskania dostępu do terminala (CLI) zdecydowano się użyć exploita **EDB-44449** (Ruby), który pozwala na zdalne wykonanie kodu (Remote Code Execution).

### Problemy z zależnościami
Podczas próby uruchomienia exploita wystąpił błąd związany z brakiem odpowiedniej biblioteki w środowisku Ruby:

<div align="center">

![Rysunek 7: Błąd uruchomienia exploita - brak biblioteki highline w Ruby](image-5.png){width=80%}

</div>

Analiza błędu wykazała, że zainstalowana wersja Ruby jest zbyt nowa i brakuje kompatybilnej wersji biblioteki `highline`.

<div align="center">

![Rysunek 8: Analiza błędu - niezgodność wersji biblioteki highline z nową wersją Ruby](image-6.png){width=80%}

</div>

**Rozwiązanie:** Ręczna instalacja starszej wersji biblioteki `highline`:
`sudo gem install highline -v 2.1.0`

<div align="center">

![Rysunek 9: Naprawa zależności - instalacja kompatybilnej wersji biblioteki highline](image-7.png){width=80%}

</div>

### Uzyskanie powłoki 
Po naprawieniu zależności, exploit został uruchomiony ponownie, celując w maszynę ofiary. Tym razem skrypt zadziałał poprawnie, otwierając sesję powłoki.

<div align="center">

![Rysunek 10: Uzyskanie dostępu CLI - interaktywna powłoka systemowa na maszynie DC-1](image-8.png){width=80%}

</div>

---

## 4. Post-eksploitacja

Będąc w systemie, przeprowadzono rekonesans katalogów. W głównym katalogu serwera WWW (`/var/www`) odnaleziono plik `flag1.txt`.

**Treść flagi nr 1:**

<div align="center">

![Rysunek 11: Zawartość flagi nr 1 znalezionej w katalogu /var/www](image-10.png){width=80%}

</div>

No i koniec