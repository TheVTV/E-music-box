# Tydzień 5 i 6. (31.03 - 14.04.2026)

Jak wspominałem, zajęcia miały być za dwa tygodnie z powodu przerwy świątecznej, a jak przerwa świąteczna to i powrót do domu. Zabrałem układ ze sobą aby dłubać sobie w nim w wolnych chwilach podczas świąt, ale suma summarum nie udało się zrobić prawie nic

Z plusów, miałem baardzo dużo czasu na zastanowienie się jak rozmieścić resztę komponentów, aby układ nie był zbyt duży. Miałem w głowie kilka pomysłów, które nawet po części udało się zrealizować już w domu (częściowe złożenie generatora nut bez testowania), także nie jest aż tak źle.

Gdy wróciłem już do Krakowa, zawitałem do sklepu elektronicznego po płytki prototypowe i po tranzystory do generatora dźwięku.

Swoją drogą, myślałem o tym jak mój układ miałby docelowo wyglądać. Myślę, że zamknę się w 6 płytkach prototypowych, a przenoszenie tego układu tak o jest lekko mówiąc kłopotliwe - płytki trzymają się między sobą tylko na trzech zatrzaskach. Dodatkowo podczas podnoszenia wyginają się, a to grozi powypadaniem przewodów. W związku z tym trzeba jakoś usztywnić te płytki, a chyba najsensowniejszym rozwiązaniem będzie przykleić je do kawałka deski. Oczywiście wiąże się to z tym, że nie uda się już odkleić tych płytek, ale raczej nie powinno być takiej potrzeby - tak jak wspominałem, zakładam że zmieszczę się na sześciu płytkach i nie trzeba będzie nic dodawać. Wymierzyłem więc jakie wymiary będzie miał mój układ i wybrałem się do Castoramy w celu zakupienia tejże sklejki. Udało się nawet dociąć ją na miejscu, a całość kosztowała mnie 10zł z hakiem - przystępnie. Przykleję układ dopiero na końcu.

Myślałem cały czas o tym przycisku do resetowania układu, bo suma summarum bardzo przyśpieszył mi debugowanie układu, a zarazem przyda się do docelowego. Wtedy do głowy przyszedł mi też pomysł na inne przyciski sterujące, które na pewno by się przydały - przycisk play i pauza! Skoro to pozytywka (a w sumie to odtwarzacz muzyki), to takie przyciski nie tyle mogą, co muszą się tam znaleźć. Zastanawiałem się jak to rozegrać, ale ostatecznie postawiłem na najzwyklejszy przerzutnik RS - w końcu to SET - RESET, czyli to czego potrzebuję.

Nie chciałem kupować kolejnego układu z samymi przerzutnikami, bo po pierwsze - potrzebny mi jest tylko jeden, a po drugie - zajmowałby dużo miejsca. A więc ponownie użyłem tranzystorów - zajmują śmiesznie mało miejsca, a do zrealizowania tego przerzutnika są potrzebne tylko dwa.

Nauczony lekcją z tranzystorami i sterownikiem sygnału zegara oraz zapachem spalonych komponentów najpierw zasięgnąłem porad w Internecie. Okazało się, że taki przerzutnik jest wręcz banalny w wykonaniu i połączeniu tranzystorów - otóż wystarczy połączyć nóżki tak, aby baza tranzystora 1 była podpięta do kolektora tranzystora 2 i na odwrót (oczywiście przy użyciu rezystorów bazowych i na kolektorach).

<div align="center">
  <img src="../../photos/week5_6/week5_6_SR.gif" width="60%" alt="Przerzutnik SR">
</div>

Po zażyciu tejże wiedzy przystąpiłem do wykonania układu na płytce. Dodałem też diody LED aby sygnalizowały stan przerzutnika (a zarazem i układu). Po testowaniu układ działa jak powinien, ale teraz trzeba go wykorzystać do sterowania sygnałem zegara (bo w sumie od niego zależne jest działanie reszty komponentów). Jest to również banalnie proste, bo wystarczy użyć bramki AND aby sprawdzić stan wyjścia Q przerzutnika oraz sygnału zegara. Oczywiście również użyłem do tego tranzystorów aby zaoszczędzić miejsce. Po spięciu wszystkiego w całość i przetestowaniu stwierdzam, iż play i stop działa jak powinien! Super, przyda się to nie tylko w docelowym układzie jako element poboczny, ale również m.in. do strojenia dźwięków (specjalnie jest to zaprojektowane tak, aby pauza pauzowała tylko liczniki, dzięki temu dany dźwięk będzie dalej grał - będzie można trzymać ten dźwięk aby go dostroić).

Po wykonaniu tego przerzutnika spojrzałem na przycisk reset, który był wykonany na bramce NOT. Po pierwsze - zajmuje dużo miejsca, a po drugie - z 6 bramek które są dostępne używam tylko jednej, co jest kompletnym marnotrawstwem komponentu. Na szybko przebudowałem układ tak, aby użyć tam tranzystora (bramkę NOT można wykonać przy użyciu tylko jednego tranzystora).

<div align="center">
  <img src="../../photos/week5_6/week5_6_play.jpg" width="60%" alt="Przerzutnik SR implementacja">
</div>

Dobra, sterowanie w całości ogarnięte, teraz zajmę się bardziej istotną częścią - generatorem dźwięku.

<div align="center">
  <img src="../../photos/week5_6/week5_6_generator1.jpg" width="60%" alt="Cały układ">
</div>


Tutaj musiałem się bardzo zastanowić jak to rozegrać - na dwóch płytkach prototypowych musiałem zmieścić:
- dekoder oktawy
- zegar generujący dźwięk
- 24 zestawy tranzystor + potencjometr z rezystorem
- 24 rezystory bazowe
- głośnik

I w tym momencie zaczęła się największa "szkoła druciarstwa" jakiej w życiu doświadczyłem - było dużo testowania ale końcowo udało się wszystko dobrze spiąć. Miejsca na płytkach było tak mało (już po dokupieniu), że musiałem podjąć radykalne kroki w celu jego zaoszczędzenia:
- potencjometry mają 3 nóżki - musiałem uciąć jedną której nie używam
- tranzystory są idealnie dopasowane i powyginane tak, żeby nie zajmowały dużo miejsca
- rezystory do potencjometrów są zamontowane pionowo
- rezystory bazowe są w kompletnie innym miejscu układu i też zmieszczone na styk

Zacząłem budować jednocześnie dekoder oktawy i generator dźwięku - głównie dlatego, żeby się ze wszystkim pomieścić, ale też aby testować układ na bieżąco. Do dekodera oktawy wystarczyło tylko „zANDować" poszczególne sygnały grup i nut, następnie wyjście tej bramki przepuścić przez rezystor, aby następnie podłączyć do bazy tranzystora odpowiadającego za ten dźwięk. Wtedy tranzystor dla danego dźwięku załączał dany układ rezystorów pomiędzy piny 6 i 7 zegara NE555, czyniąc rolę rezystora R2.

Najpierw złożyłem pierwsze cztery dźwięki i przystąpiłem do ich strojenia. Ze wzoru wychodziło mi, że aby uzyskać dźwięk C4 (261 Hz) należy użyć R2 = około 27k (przy założeniu R1 = 1k, C = 100nF). W związku z tym, iż używam potencjometrów 10k, użyłem rezystora 20k (aby mieć regulację 20-30k). Podłączyłem więc układ do zasilania i wcisnąłem pauzę aby zatrzymać się na dźwięku C4. Gdy usłyszałem dźwięk z głośnika od razu wiedziałem, że coś jest nie tak - dźwięk był o wiele za wysoki jak na C4, ale jakież było moje zdziwienie gdy na aplikacji mobilnej do pomiaru częstotliwości zamiast 260Hz pojawiło się 800! Spodziewałem się, iż częstotliwość będzie się lekko różniła od tej na papierze, ale nie o ponad 300%! Nie wiem czemu tak się dzieje, idę linia po linii szukając błędów i liczę jeszcze raz rezystancję ze wzorów.

Po trzech godzinach batalii z układem i szukania informacji po Internecie się poddałem. Na ostatnią chwilę wpadłem jeszcze na pomysł zmierzenia parametrów moich rezystorów i kondensatorów, lecz nie miałem miernika. Pożyczyłem więc jeden od znajomego i zacząłem sprawdzać po kolei. Wszystkie wartości rezystorów były w granicach tolerancji producenta poprawne. Ostatnie co zmierzyłem to pojemność kondensatora C przy zegarze - zamiast obiecanych 100nF, miał tylko 40…

Pierwsze co zrobiłem gdy się o tym dowiedziałem to bez słowa wszedłem na Allegro aby kupić wreszcie ten miernik uniwersalny który siedział w moim koszyku od kilku miesięcy…

Na następny dzień poszedłem do sklepu elektronicznego aby kupić faktyczny kondensator o pojemności 100nF, i po podłączeniu go do układu do moich uszu dotarł piękny dźwięk C4, który należało tylko lekko dostroić.

Po tych zmarnowanych godzinach wyciągnąłem dwa wnioski:
- multimetr to bardzo potrzebne urządzenie
- nigdy więcej nie kupować podejrzanie tanich komponentów

A więc, po walce z kondensatorem złożyłem w końcu pierwsze 4 dźwięki…

<div align="center">
  <img src="../../photos/week5_6/week5_6_notes1.jpg" width="60%" alt="Cztery dźwięki">
</div>

…potem kolejne 4 dźwięki…

<div align="center">
  <img src="../../photos/week5_6/week5_6_notes2.jpg" width="60%" alt="Osiem dźwięków">
</div>

… i kolejne 4 dźwieki …

<div align="center">
  <img src="../../photos/week5_6/week5_6_notes3.jpg" width="60%" alt="Pierwsza oktawa">
</div>

…aby w końcu uzyskać pełną oktawę dźwięków.

Wszystko gra tak jak powinno, co bardzo mnie cieszy. Na razie nie wgrywałem żadnego utworu i cały czas w EEPROMie siedziała sekwencja do strojenia. Potem już z marszu zrobiłem drugą oktawę:

<div align="center">
  <img src="../../photos/week5_6/week5_6_notes4.jpg" width="60%" alt="Druga oktawa">
</div>

(Te dwa wiszące przewody przy prawych górnych potencjometrach są autorstwa mojej dziewczyny, też chciała dołożyć swoją cegiełkę)

No i koniec, wszystko podłączone. Jest jednak parę problemów:

- strojenie jest bardzo uciążliwe, czasem po przejściu przez wszystkie dźwięki i ich strojeniu trzeba od nowa stroić pierwsze parę dźwięków
- w niektórych sytuacjach mimo tego, że układ działa i liczniki przeskakują na kolejne wartości, układ nie wydaje żadnego dźwięku.

Jeśli chodzi o problem drugi, winię tutaj luźne przewody - jest szansa że są faktycznie zbyt cienkie i czasem nie stykają z płytką prototypową, gdyż po dociśnięciu układ czasem działa, muszę to potem dokładnie obadać.

A jeśli chodzi o pierwszy, to mam paru pretendentów do powodowania tego problemu:

- zegar wariuje przy tak szybkiej zmianie R2
- potencjometry/tranzystory/rezystory nie stykają

Na całe szczęście najpierw zacząłem badać tranzystory - przy wyginaniu nóżek niektóre się naderwały i to powodowało te problemy. Po wymienieniu ich na nowe - problem zniknął.

Zdążyłem, udało się skończyć układ na zajęcia (co prawda z godzinnym opóźnieniem, ale „lepiej późno niż wcale"). Przyniosłem więc układ na zajęcia i zaprezentowałem prowadzącemu. Po pokazie obiecałem, że jeszcze do końca zajęć dodam parę rzeczy i zaprezentuję zmiany:

- chciałem polepszyć zasilanie układu
- chciałem dodać przynajmniej jeden faktyczny utwór

Zasilanie do tej pory było realizowane w sposób łopatologiczny, poprzez piny mikrokontrolera RP2040. To jedyny mikrokontroler jaki miałem pod ręką który miał dostępny pin 5V. Aby nie bawić się jeszcze z lepszym zasilaniem na początku projektu stwierdziłem, że na ten moment to wystarczy, jednak teraz pod koniec fajnie by było dorobić jakiś sensowniejszy układ zasilania. Miałem parę pomysłów:
- baterie paluszki/9V - odpada, gdyż szybko by się rozładowywały, ponadto trzeba by było dopasować napięcie pod 5V
- akumulatory 18650 - już bardziej sensowne bo można je ładować, ale wymagają osobnego sterownika do ładowania i również trzeba dopasować napięcie - odpada
- zasilanie np. z kostki do telefonu - dobre rozwiązanie, bo standardowa kostka będzie w stanie dać napięcie 5V, ale musiałem odrzucić ten pomysł ze względu na zakłócenia (robiłem inny projekt na mikrokontrolerach który wykonywał EKG i widać było przebicia spowodowane przez prąd zmienny ze ściany, ale pomagała zmiana źródła zasilania na np. powerbank lub laptop)

W związku z tym postawiłem na zasilanie z przewodu USB, podpiętego do powerbanka. Cały pomysł na układ zasilania był bardzo prosty - ucinam przewód USB, znajduję 5V i GND, lutuję do płytki i dołączam przewody do wpięcia do płytki prototypowej. Jednak wprowadziłem do tego pomysłu parę ważnych modyfikacji:

- zaraz po uciętym przewodzie wstawię kondensator 10 mikro Faradów, w celu jeszcze lepszego ustabilizowania napięcia
- nie bez powodu pozostawiłem w tym miejscu dwie linie zasilania płytek prototypowych:

<div align="center">
  <img src="../../photos/week5_6/week5_6_notes5.png" width="60%" alt="Linie zasilania">
</div>


Otóż aby dodatkowo zapobiec zakłóceniom dźwięku i go odszumić, postanowiłem rozdzielić generator dźwięku i resztę układu. Toteż w moim „zasilaczu" zamiast jednego wyjścia zasilania zastosuję dwa.

Układ zlutowałem i przetestowałem, działa jak powinien.

A więc została druga rzecz do zrobienia na zajęciach, wgranie utworu. Nie zastanawiałem się długo nad tym jaki to będzie utwór, wiedziałem to zanim jeszcze zacząłem projekt - Katiusza. Utwór ten fascynuje mnie od dawna, wiele lat przed rozpoczęciem konfliktu. Być może to będzie powodować kontrowersje, ale moje uwielbienie do samej melodii nie pozwala mi wybrać innego utworu niż ten.

A więc napisałem program w Pythonie który będzie przyjmował sekwencję nut w postaci literowej wraz z długością z pliku tekstowego, a następnie połączy się przez port COM z Arduino Mega i wgra melodię do EEPROM. Przygotowałem więc i sam utwór i wgrałem go do pamięci, aby przetestować już ostatecznie mój projekt.

Po wciśnięciu przycisku play z głośnika zaczęła wybrzmiewać melodia Katiuszy, i w dokładnie tym momencie poczułem wielką ulgę, że to co zajęło mi ostatnie 6 tygodni nie skończyło się wielkim fiaskiem. Jestem dumny, po prostu dumny z siebie, że mi się udało.

Po chwili zadumy pokazałem projekt prowadzącemu, który również cieszył się ukończeniem projektu. Udało się nawet podłączyć układ „na krótko" do głośników poprzez złącze jack, i dźwięk był nie tylko głośniejszy, ale też i czystszy. Pomyślałem więc, że fajnie by było dodać funkcjonalność odtwarzania dźwięku przez dowolne głośniki przewodowe.

Oczywiście podczas pokazu musiał wystąpić problem z niegrającym układem mimo skaczących liczników, lecz tym razem dociśnięcie przewodów nic nie dało. Spróbowałem na krótko podpiąć diodę LED do wyjść EEPROM (aby sprawdzić czy dźwięki idą dalej), a przy którymś z kolei podłączeniu układ zresetował się i zaczął grać. Zdziwiło to i mnie i prowadzącego, gdyż to jakiś głębszy problem niż nie stykające przewody. Muszę na to zwrócić uwagę.

Ostatnia rzecz jaka przykuła moją uwagę to sam sposób odtwarzania utworu. Otóż w momencie zaczytywania długości dźwięku do licznika malejącego była przerwa w odtwarzania dźwięku. Niestety, trwała ona cały cykl zegara, więc zaburzało to wszelki rytm utworu. Być może dla zwykłej osoby nie stanowiłoby to większego problemu, lecz moje 6 lat szkoły muzycznej niestety nie pozwoliło mi zostawić tego w ten sposób (to też dlatego użyłem 24 potencjometrów do strojenia zamiast zwykłych liczników dzielących częstotliwości po pół). Muszę się też temu przyjrzeć.

Na ten tydzień tyle, udało się wybrnąć z tygodniowej obsuwy i wreszcie przedstawić początkową wersję projektu. Myślę, że za tydzień oddam już finalną wersję.

## TODO (Tydzień 5 i 6)

- Dolutowanie gniazda jack
- Poszukanie powodu niegrającego układu
- Poszukanie rozwiązania przerwy w dźwięku podczas grania utworu
- Dogranie paru innych utworów do pamięci EEPROM