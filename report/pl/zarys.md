# Odtwarzacz melodii z pamięci EEPROM (Elektroniczna pozytywka)

**Wykonanie:** Bartosz Wójcik

## Ogólny zamysł

Chciałbym zbudować na płytkach prototypowych odtwarzacz melodii zapisanych w formacie "nutowym" na pamięci EEPROM. Do tego celu chciałbym użyć jak najbardziej podstawowych układów elektronicznych, takich jak np. bramki logiczne, liczniki, przerzutniki.

## Budowa

*(Podkreśleniem oznaczone są funkcjonalności/zmiany powstałe podczas wykonywania projektu)*

Sam system można by podzielić na dwie części:
### 1. Czytnik nut z pamięci

### 2. Generator dźwięku

## Czytnik nut z pamięci (Ad.1)  
Zegar -> układ liczników -> pamięć EEPROM -> dekoder nut -> tranzystor NPN

W układzie pamięci znajdowałaby się melodia w przykładowym formacie:

00000 - nuta C4  
00001 - nuta C#4  
00010 - nuta D4  
…

01011 - nuta B4  
10000 - nuta C5 (wyższa oktawa)  
10001 - nuta C#5  
10010 - nuta D5

…

11011 - nuta B5  
11111 - bit STOP  
01111 - bit PAUZA (przerwa pomiędzy nutami)

(czyli kolejne adresy to kolejne nuty, dla każdego adresu najstarszy bit symbolizuje numer oktawy, a pozostałe 4 - nutę)

Sygnał traktuję jako: O N4 N3 N2 N1 (gdzie O - oktawa, N4-1 - zakodowana nuta)

Sama pamięć działałaby w trybie równoległym - 8 pinów wejściowych, 5 pinów wyjściowych, toteż wyjście z liczników podłączone do wejścia EEPROM generowałoby sygnał który później będziemy interpretować jako nuty (częstotliwość)

Sam „dekoder" można podzielić na dwie części: dekoder nut i dekoder oktawy:

Dekoder nut miałby 4 wejścia i 13 wyjść (12 nut + bit STOP/PAUZA - bit mówiący o oktawie nie musi być uwzględniany, bo sam o sobie mówi).

Dekoder oktawy miałby 13 wejść i 26 wyjść (2 \* 12 nut - dwie oktawy + bit STOP + bit PAUZA). Jest on o tyle prosty, że to zwykły AND z bitem O - oktawa 5, AND z znegowanym bitem O - oktawa 4.

Chciałbym cały ten dekoder zbudować na bramkach.

## Generator dźwięku (Ad.2)

Tranzystor NPN -> rezystor -> potencjometr -> zegar -> tranzystor NPN -> głośnik/buzzer

Częstotliwość zegara (wybrałem układ NE555) zależy od R1, R2 i C, w tym przypadku R1 i C będą stałe a to od R2 będzie zależna częstotliwość zegara. Dla każdego wyjścia nutowego z dekodera będzie osobny układ rezystorów pełniący rolę R2, dzięki temu dla każdego sygnału uzyskamy inną częstotliwość. Aby uzyskać czysty dźwięk dla danej nuty potrzebna jest jej dokładna częstotliwość, a aby ją uzyskać potrzebujemy rezystorów o dokładnej „specyficznej wartości", a więc aby ułatwić pracę dla każdej wiązki dodam potencjometr 10k + dobrany rezystor, aby móc „stroić" dźwięk.

Zegar generuje sygnał prostokątny, dzięki któremu poprzez tranzystor wprawiamy w ruch membranę głośnika i generujemy dźwięk. Być może uda się stworzyć inny sygnał, np. sinusoidę.

### Przykładowe przejście cyklu zegara w celu wygenerowania dźwięku D4

- Zegar (generuje sygnał prostokątny o zadanej częstotliwości)
- Licznik (przełącza na kolejną wartość, załóżmy że teraz ma 00000)
- EEPROM (odczytuje co ma na adresie 00000, załóżmy 00010)
- Dekoder (dostaje 0010 i przekształca na D)
- ANDujemy wyjście z dekodera z bitem O i dostajemy D4
- Załączamy tranzystor który zmienia układ rezystorów na odpowiadający dźwiękowi D4
- Zegar generuje zadaną częstotliwość i wypuszcza sygnał na tranzystor
- Tranzystor załącza i odłącza zasilanie głośnika generującego dźwięk

## Dodatki

Do pełni szczęścia i „przyjemniejszej" pracy warto byłoby dodatkowo zrobić:

- Programator EEPROM - niby można kupić, ale wychodzę z założenia „po co kupować, jak można zrobić samemu". Do tego celu można użyć mikrokontrolera, użyłbym ESP32 (bo mam je pod ręką). Jedyny problem to różnica napięć - esp pracuje na 3.3V, a EEPROM (jak dobrze pamiętam) na 5V, ale jakoś to na pewno załatwię. Docelowo chciałbym wykonać ten układ nie na płytce prototypowej, lecz na uniwersalnej płytce PCB - taki układ nie byłby jednorazowego użytku. Ostatecznie aby nie bawić się rejestrami i małą ilością pinów zdecydowałem się na użycie Arduino MEGA.

- Generator sygnału prostokątnego - docelowo do użytku czysto debugowego. Zegar można by było ustawić w tryb monostabilny i puszczać pojedyncze sygnały prostokątne (do debugowania np. liczników, odczytu z EEPROM, strojenia dźwięków). Docelowo również rozważam zlutowanie układu na uniwersalnej płytce drukowanej.

- Miernik częstotliwości - nie zagłębiałem się w układ, więc nie wiem jak bardzo byłby skomplikowany. Przydałby się do dokładnego strojenia dźwięków, aby nie robić tego „na ucho" lub z użyciem aplikacji na telefon, ale być może dmucham na zimne i nie będzie to potrzebne. Dźwięk nie musi być dostrojony idealnie co do setnych Hz, a aplikacja radzi sobie z tym bardzo dobrze - pomysł porzucam (a nawet lepiej, kupiłem sobie po prostu miernik uniwersalny z możliwością pomiaru częstotliwości)