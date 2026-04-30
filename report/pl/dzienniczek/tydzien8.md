# Tydzień 8. (21-28.04.2026)

Ostatni tydzień projektu, ostatni tydzień zabawy. Zacznijmy więc:

**Dodanie DIP switcha do zmiany utworów**

Aby zapewnić brak stanów nieustalonych muszę użyć rezystorów „pull-down". Kiedy dany przycisk nie będzie załączony, pin będzie podłączony do masy (stan 0), a gdy go przełączymy - na pin dojdzie napięcie (stan 1). Ponieważ mam 5 pinów i DIP switch z 5 przełącznikami to muszę użyć 5 rezystorów (na każdy pin), ja użyłem tych o rezystancji 6k8.

<div align="center">
  <img src="../../photos/week8/week8_DIP.jpg" width="60%" alt="DIP Switch">
</div>

**Dogranie większej ilości utworów**

Napisałem ręcznie 9 utworów (+ strojenie), które znam. Są to:

- "Dla Elizy" - Ludwig van Beethoven (00000)
- "Taniec Węgierski no.5" - Johannes Brahms (00001)
- "Marsz Torreadora" z uwertury "Carmen" - George Bizet (00010)
- "Walc no.2" - Dymitr Szostakowicz (00011)
- "Włazł kotek na płotek" - piosenka ludowa (00100)
- "Były sobie kurki trzy" - piosenka ludowa (00101)
- "Megalovania" - Toby Fox, Undertale (00110)
- "Freedom motif" - Toby Fox, Deltarune (00111)
- "Fischia il vento" - włoska piosenka (01000)
- Strojenie (11111)

Wgrałem je do pamięci w taki sposób, że 1 utwór zaczyna się od adresu 0, drugi od adresu 256, trzeci - 512 itd. Dzięki temu mogę skorzystać z funkcjonalności DIP switcha.

Po przetestowaniu wszystkie utwory działają, wszystko ładnie gra i wreszcie działa jak powinno. Liczby w nawiasach w powyższej liście symbolizują ustawienie DIP switcha.

**Dokończenie sprawozdania**

No i cóż, w tym momencie właśnie kończę pisać sprawozdanie - projekt oficjalnie dobiega końca. Jestem niezmiernie dumny, że miałem możliwość wykonać taki a nie inny projekt. Najśmieszniejsze jest jednak to, że jeszcze rok temu w życiu bym nie pomyślał, że jestem zdolny zrobić takie rzeczy. Wszystko zmieniło się pod koniec 4 semestru, kiedy to oglądałem ostatnie wykłady z „Techniki Cyfrowej" - to właśnie te wykłady uświadomiły mi, że fizyka, elektronika i technika cyfrowa nie muszą być takie straszne. Wtedy to zacząłem bawić się mikrokontrolerami, wykonywać pierwsze projekty na Arduino czy też ESP32, a skończyło się na rzuceniu na głęboką wodę i oparciu mojej pracy inżynierskiej o mikrokontrolery właśnie. Wszystko dzięki świetnym wykładom mojego prowadzącego z „Techniki Cyfrowej" i „Złożonych Systemów Cyfrowych" - dr. Jacka Długopolskiego. W ramach podziękowania za odnalezienie mojej ścieżki w życiu przekazuję mój projekt w ręce prowadzącego - wierzę, iż posłuży długie lata, przejdzie przez masę studentów i być może przekona choć część z nich do spróbowania się w elektronice czy też technice cyfrowej. Można powiedzieć, że jestem chodzącym przykładem na to, że się da!

Ze względu na charakter projektu poniżej załączę obrazowy schemat układu wraz z dokładnym zdjęciem, aby w razie awarii móc szybko znaleźć jej przyczynę. Dołączę również spis komponentów wraz z cenami, za które mi udało się je kupić (gdyby ktoś chciał zreplikować projekt)

- [Spis komponentów](../spis_komponentow.md)
- [Finalny układ](../finalny_uklad.md)