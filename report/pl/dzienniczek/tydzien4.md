# Tydzień 4. (24-31.03.2026)

Do pracy mogłem przystąpić dopiero pod koniec tygodnia z powodu natłoku różnych spraw… Mimo tego, w międzyczasie miałem czas na spokojne pomyślenie nad projektem. Udało mi się zauważyć dwa problemy:

- Na ten moment z EEPROMa sygnał wychodzi niezależnie od tego czy jest to nuta czy jej czas (innymi słowy do dekodera mogą wchodzić tylko nuty, a nie ich czas)

- Układ zajmuje dużo miejsca na płytkach, a za niedługo braknie go całkowicie

Najpierw zajmę się tym drugim problemem, gdyż dotychczasowy układ da się lepiej rozplanować na płytkach i przy okazji zyskać trochę cennego miejsca:

<div align="center" style="display: flex; justify-content: center; gap: 20px;">
  <div style="text-align: center;">
    <img src="../../photos/week4/week4_bef.jpg" width="40%" alt="Przed">
    <p><strong>Przed</strong></p>
  </div>
  <div style="text-align: center;">
    <img src="../../photos/week4/week4_aft.jpg" width="40%" alt="Po">
    <p><strong>Po</strong></p>
  </div>
</div>


Na swoim miejscu został tylko zegar oraz EEPROM, sterownik udało się zdecydowanie pomniejszyć, a na zaoszczędzone miejsce na dolnej płytce przeniosłem liczniki pamięci i czasu. Dzięki temu połączenia licznik - EEPROM będą o wiele prostsze do zrealizowania. A skoro już przy połączeniach - zaczęły powoli kończyć mi się zworki z zestawu który kupiłem na początku. Średnio chciałem wydawać kolejne 15zł, ponieważ jedno - cena, a drugie - przewody były za grube i za twarde, więc często ciężko je było włożyć do płytki. Zawitałem więc do Castoramy, gdzie udało mi się znaleźć przewód (docelowo do użytku przy dzwonkach do drzwi) 8-io żyłowy na metry. Każda żyła to drut (single wire) więc nie musiałbym bawić się w cynowanie końcówek, a dodatkowo mają trochę mniejszą średnicę, przy wyginaniu drut zostaje w swojej pozycji i jednocześnie nie jest aż tak twardy jak standardowe zworki. Najlepsza jest jednak cena - 2,50zł za metr! Bez zawahania kupiłem dwa metry, na początek wystarczy a zawsze mogę dokupić. W końcu dwa metry takiego przewodu to 8\*2=16 metrów zworek w różnych kolorach. Gdy podłączałem liczniki do EEPROMa, komfort pracy z tymi przewodami był nie do opisania - mogę je doginać i prowadzić ścieżki jak tylko chcę, a ponadto są na tyle cienkie, że mogę wsadzić dwa przewody do jednej dziurki, co znacząco ułatwi modelowanie niektórych wiązek.

Dobra, koniec gadania o przewodach - po przearanżowaniu układu udało się zaoszczędzić około pół płytki, więc płynnie przechodzę do drugiego (a właściwie pierwszego) problemu. Na ten moment gdybym podłączył EEPROM bezpośrednio do dekodera, to dekodowałby on nie tylko nuty (na nieparzystych adresach), ale również i ich długości (na parzystych adresach). Oczywiście nie jest to zamierzone, długość nuty ma znaczenie tylko dla licznika malejącego! W związku z tym chcę, aby na dekoder szedł tylko i wyłącznie sygnał nuty, czyli wtedy, gdy na najmłodszym bicie licznika pamięci znajduje się wartość 1. W takim razie, wystarczy „zANDować" sygnał LSB z każdą z pięciu linii wyjściowych EEPROM, ale i również z każdą z znegowanych pięciu linii wyjściowych EEPROM (bo używam też bramek NOT w dekoderze). Czyli potrzebuję 5 bramek NOT i 10 bramek AND

<div align="center" style="display: flex; justify-content: center; gap: 20px;">
  <img src="../../photos/week4/week4_and.png" width="30%" alt="Układ AND">
  <img src="../../photos/week4/week4_not.png" width="30%" alt="Układ NOT">
</div>

Mam układy HEF4049BP (6 bramek NOT) i SN74HC08 (4 dwuwejściowe bramki AND), a więc biorę jeden układ HEF… i trzy układy SN… i wstawiam dokładnie w to zaoszczędzone wcześniej miejsce na płytce. Sygnał z EEPROM podłączam do bramek NOT, a następnie podłączam zarówno sygnał zanegowany jak i prosto z pamięci do bramek AND wraz z sygnałem z LSB.

<div align="center">
  <img src="../../photos/week4/week4_fix.jpg" width="60%" alt="Finalny Multisim">
</div>

Po przetestowaniu (podłączeniu na wyjścia bramek diod LED) układ działa jak powinien - sygnał przechodzi tylko wtedy, gdy z pamięci wychodzi nuta. Teraz zajmę się wykonaniem docelowego dekodera dla nut, a tak właściwie dekoderów dla grupy i dla nuty w grupie (dla przypomnienia bity N4 N3 symbolizują grupę, a N2 N1 - nutę w danej grupie). Do tego potrzebne będzie mi 8 bramek AND (po 4 na każdy „dekoder"), czyli dwa układy SN74HC08. Po prostu „ANDuję" bity każdy z każdym (w obrębie N4 N3 a potem N2 N1) i tym sposobem dostanę 8 wyjść - 4 grupy po 4 nuty:

<div align="center">
  <img src="../../photos/week4/week4_group1.jpg" width="60%" alt="Dekoder grup i nut">
</div>

<div align="center">
  <img src="../../photos/week4/week4_group2.jpg" width="60%" alt="Dekoder grup i nut - wyjaśnienie">
</div>


Tym sposobem w dolnym rzędzie znajdują się nuty, a w górnym - grupy.

Teraz trzeba podłączyć nuty z grupami w taki sposób, aby ich kombinacje tworzyły faktyczne sekwencje nut (również zbiega to do podłączenia każdy z każdym). W jednej oktawie jest 12 nut, a więc potrzebuję 12 bramek AND czyli 3 układy:

<div align="center">
  <img src="../../photos/week4/week4_notes.jpg" width="60%" alt="Dekoder nut">
</div>

Zrobione, sprawdzę więc czy nie pomyliłem się gdzieś w połączeniach. Najlepiej będzie po prostu do EEPROM wgrać sekwencję przejścia po całej oktawie po kolei (00000, 00001, 00010 itp.)

Po wgraniu i włączeniu układu diody LED trochę wariują, czasem się zapalają, czasem nie - musiałem się gdzieś pomylić.

Po n-tej próbie poprawki ręczne resetowanie układu poprzez odłączenie i podłączenie zasilania było lekko mówiąc denerwujące. Dodatkowo czasami po szybszym resecie liczniki pamiętały swoją wartość, co było na początku zastanawiające (nie myślałem że tak się może dziać), a potem już tylko wkurzające. Skoro problem leży w licznikach, to trzeba coś z nimi pokombinować. Tak się składa, że oba liczniki mają pin do resetu (dobra wiadomość), ale jeden licznik ma ten pin active-low, a drugi - active-high (zła wiadomość). Czyli aby zresetować wszystkie liczniki, na jednym muszę podać wartość 1, a na drugim w tym samym czasie wartość 0. Na początku miałem zagwozdkę jak to zrobić za pomocą przycisku. Przecież jeśli przycisk nie będzie wciśnięty to nie jest to traktowane jako wartość logiczna 0, tylko stan nieustalony! (przycisk wtedy podłączyłbym do VCC, a stan nieustalony bo pin nie ma odprowadzenia do masy tylko wisi!). Przypomniałem sobie jednak, że przecież mam pod ręką od groma bramek NOT - wystarczy że na licznik active-high podam napięcie przez przycisk, a na drugi zanegowaną wartość (układ z NOTami ma osobne zasilanie, a co za tym idzie przy wartości 0 odprowadza do masy). Tak więc zrobiłem, a debugowanie układu stało się o wiele prostsze. Ponadto, taki układ przyda się nawet w docelowym produkcie do resetu utworu.

Po kilku poprawkach układ działa jak powinien - zapalają się pojedynczo diody reprezentujące dźwięki zaczynając od C, a na B kończąc - super!

Na ten tydzień tyle, wreszcie mam początek dekodera, który poprawnie wyświetla nuty! Projekt powoli zbliża się ku końcowi - teraz tylko dokończyć dekoder i złożyć drugą część układu - generator dźwięku.

Następne zajęcia za 2 tygodnie z powodu przerwy świątecznej, myślę, że uda mi się dokończyć projekt do tego terminu. Martwi mnie tylko ilość płytek prototypowych jakie aktualnie posiadam, może być krucho z miejscem ale istnieje szansa, że się zmieszczę.

## TODO (Tydzień 4)

- Dokńczenie dekodera nut (dokładniej dekoder oktawy)
- Wykonanie generatora dźwięku
