# State of the Art (SoA)

## Analiza wpływu pojemności sieci neuronowych i generowania perturbacji na generalizację estymatorów funkcji score w modelach dyfuzyjnych

**Wnioskodawca i wykonawca:** mgr Marzena Halama  
**Kierownik zespołu / promotor:** dr hab. inż. Joanna Domańska  
**Okres realizacji projektu:** 01.08.2026–30.11.2026  

---

## 1. Kontekst badawczy

Modele dyfuzyjne należą do klasy modeli generatywnych, w których rozkład danych jest stopniowo zaburzany szumem, a następnie odtwarzany przy użyciu procesu odwrotnego. Kluczowym składnikiem takiego modelu jest zależna od czasu funkcja score:

$$
s^\star(x,t)=\nabla_x \log p_t(x),
$$

gdzie \(p_t\) oznacza rozkład danych po zaburzeniu odpowiadającym chwili \(t\). Funkcja score wskazuje kierunek wzrostu logarytmu gęstości i dostarcza informacji potrzebnej do odszumiania próbek oraz prowadzenia procesu generowania.

W praktyce funkcja \(s^\star(x,t)\) jest zastępowana estymatorem neuronowym \(s_\theta(x,t)\). Parametry \(\theta\) są najczęściej uczone za pomocą odszumiającego dopasowania funkcji score (*denoising score matching*, DSM). Dla zaburzenia postaci

$$
x_t=\alpha(t)x_0+\sigma(t)\varepsilon,
\qquad
\varepsilon\sim\mathcal{N}(0,I),
$$

jedna z typowych postaci celu treningowego ma postać:

$$
\mathcal{L}_{\mathrm{DSM}}(\theta)
=
\mathbb{E}_{x_0,t,\varepsilon}
\left[
\lambda(t)
\left\|
s_\theta(x_t,t)
+
\frac{\varepsilon}{\sigma(t)}
\right\|_2^2
\right].
$$

Minimalizacja straty DSM nie wymaga znajomości gęstości danych ani jej stałej normalizacyjnej. W skończonym eksperymencie wartość oczekiwana jest jednak zastępowana przybliżeniem Monte Carlo, którego własności zależą m.in. od liczby danych, liczby realizacji szumu, sposobu ich generowania, pojemności sieci i przebiegu optymalizacji.

---

## 2. Pojemność modelu, interpolacja i podwójny spadek błędu

Klasyczne ujęcie kompromisu obciążenie–wariancja sugeruje, że nadmierne zwiększanie złożoności modelu powinno prowadzić do przeuczenia i wzrostu błędu testowego. W nowoczesnych sieciach neuronowych obserwuje się jednak bardziej złożone zachowanie. Po początkowym spadku błąd testowy może wzrosnąć w pobliżu progu interpolacji, a następnie ponownie maleć w silnie nadparametryzowanym reżimie. Zjawisko to określa się jako *double descent*.

Nakkiran i współautorzy wykazali, że podwójny spadek może występować nie tylko jako funkcja liczby parametrów, ale również liczby epok i liczby przykładów uczących [1]. Autorzy podkreślili znaczenie efektywnej złożoności modelu, zależnej nie tylko od architektury, lecz także od algorytmu uczenia i czasu optymalizacji. Z tego względu próg interpolacji nie powinien być utożsamiany automatycznie z prostą równością liczby parametrów i liczby danych. W projekcie musi on zostać wyznaczony empirycznie jako obszar, w którym model po raz pierwszy uzyskuje bardzo mały błąd treningowy dla analizowanego celu.

W odniesieniu do modeli dyfuzyjnych problem jest trudniejszy niż w klasyfikacji. Cel DSM zawiera losową realizację zakłócenia, a więc sieć może dopasowywać zarówno regularną strukturę prawdziwej funkcji score, jak i losowe składniki skończonego celu treningowego. Niski błąd treningowy nie musi zatem oznaczać dokładnej estymacji \(s^\star(x,t)\).

---

## 3. Estymacja funkcji score na rozkładach niskowymiarowych

Chen i współautorzy analizują aproksymację oraz estymację funkcji score, gdy dane są skoncentrowane w nieznanej podprzestrzeni o małym wymiarze [2]. Praca dostarcza teoretycznych wyników dotyczących złożoności próbkowej i pokazuje, że odpowiednio dobrana architektura może dokładnie aproksymować funkcję score oraz efektywnie estymować rozkład.

Wyniki te uzasadniają wykorzystanie kontrolowanego rozkładu dwuwymiarowego jako pierwszego środowiska eksperymentalnego. W takim ustawieniu można:

1. wyznaczyć prawdziwą funkcję \(s^\star(x,t)\) analitycznie;
2. ocenić predykcję sieci na regularnej siatce punktów i na próbkach Monte Carlo;
3. rozdzielić błąd estymacji funkcji score od błędu dopasowania losowego celu DSM;
4. wizualizować pole wektorowe estymatora i jego odchylenie od rozwiązania analitycznego;
5. analizować zachowanie modelu w pobliżu obszarów o małej gęstości, granic między modami i punktów o dużej krzywiźnie log-gęstości.

Jeżeli rozkład bazowy zostanie zdefiniowany jako mieszanina rozkładów Gaussa, rozkład zaburzony \(p_t\) również będzie mieszaniną Gaussów. Jego score można obliczyć jako ważoną kombinację składowych, co zapewnia dokładny punkt odniesienia dla wszystkich badanych poziomów szumu.

---

## 4. Generalizacja i zapamiętywanie w modelach dyfuzyjnych

Literatura wskazuje na pozorny paradoks. Z jednej strony modele dyfuzyjne generują nowe próbki i wykazują praktyczną zdolność generalizacji. Z drugiej strony empiryczny minimizer celu score matching może odpowiadać rozkładowi skoncentrowanemu wokół przykładów treningowych i prowadzić do ich odtwarzania.

Gu i współautorzy zauważają, że teoretycznie optymalne rozwiązanie typowego celu dyfuzyjnego może generować repliki danych treningowych, a skłonność do memorization zależy m.in. od rozmiaru zbioru, konfiguracji modelu i procedury uczenia [3]. Baptista i współautorzy pokazują, że przy zastosowaniu empirycznego rozkładu danych minimizer celu score matching jest związany z zależną od czasu mieszaniną Gaussów, a wierne odwzorowanie tego rozwiązania może kierować proces odwrotny do próbek treningowych [4]. W tej interpretacji ograniczona pojemność sieci, wczesne zatrzymanie lub regularyzacja mogą działać jako mechanizmy wygładzające i zapobiegać dokładnemu odtworzeniu empirycznej funkcji score.

Zeno i współautorzy analizują przepływ prawdopodobieństwa dla płytkich sieci ReLU o minimalnej normie [6]. W uproszczonych ustawieniach trajektorie mogą zbiegać do punktów treningowych, sum punktów lub innych punktów rozmaitości. Wyniki pokazują, że wynik generowania zależy od indukcyjnego obciążenia modelu, liczby przykładów oraz harmonogramu czasu dyfuzji. Shah i współautorzy wskazują ponadto, że memorization jest szczególnie związane z zadaniami odszumiania przy małych poziomach szumu, odpowiadającymi odtwarzaniu szczegółów o wysokiej częstotliwości [7].

Najnowsze prace rozwijają interpretację generalizacji jako efektu wygładzania empirycznej funkcji score. Zhou, Zhang i Wright argumentują, że score empirycznej mieszaniny może być lokalnie zdominowany przez pojedyncze przykłady treningowe, podczas gdy estymator neuronowy może nauczyć się gładszego pola, wykorzystującego strukturę lokalnej rozmaitości zamiast pojedynczych punktów [8]. Oznacza to, że większa zgodność z empirycznym celem treningowym nie zawsze jest równoznaczna z lepszą generalizacją względem rozkładu populacyjnego.

---

## 5. Znaczenie liczby i sposobu generowania realizacji szumu

Szczególnie bliska założeniom projektu jest analiza DSM z wykorzystaniem losowych cech neuronowych przedstawiona przez George’a, Veigę i Macrisa [5]. Autorzy wyprowadzają precyzyjne krzywe błędu treningowego i testowego w uproszczonym reżimie wysokowymiarowym. Wyniki wskazują, że zachowanie generalizacyjne zależy zarówno od złożoności modelu, jak i liczby realizacji szumu przypisanych do jednego przykładu danych.

W praktyce można wyróżnić dwa protokoły:

### 5.1. Ponowne losowanie zakłóceń

Dla każdego użycia przykładu \(x_i\) losowane są nowe wartości \(t\) i \(\varepsilon\). Model otrzymuje potencjalnie nieograniczoną liczbę perturbacji tego samego punktu. Taki protokół lepiej przybliża wartość oczekiwaną w populacyjnym celu DSM i utrudnia zapamiętanie konkretnych realizacji szumu.

### 5.2. Stały zbiór zaszumionych przykładów

Przed treningiem konstruowany jest skończony zbiór krotek \((x_i,t_{ij},\varepsilon_{ij},x_{t,ij})\), który jest wielokrotnie wykorzystywany. Wysokopojemny model może wówczas dopasować nie tylko regularną zależność wynikającą z rozkładu danych, ale również przypadkowe odchylenia charakterystyczne dla ustalonego zbioru perturbacji.

Protokoły te mogą prowadzić do podobnego błędu treningowego, ale odmiennego błędu względem świeżych perturbacji oraz prawdziwej funkcji score. Różnica między nimi stanowi centralny element planowanego projektu.

---

## 6. Luka badawcza

Istniejące prace dostarczają teorii aproksymacji funkcji score, opisują memorization w modelach dyfuzyjnych oraz wskazują na znaczenie pojemności modelu i liczby próbek szumu. Nadal brakuje jednak kontrolowanego badania eksperymentalnego, które jednocześnie:

- zmienia szerokość sieci i liczbę parametrów w zakresie obejmującym próg interpolacji;
- porównuje ponowne losowanie zakłóceń z treningiem na ustalonym zbiorze perturbacji;
- wykorzystuje rozkład, dla którego prawdziwa zależna od czasu funkcja score jest znana analitycznie;
- mierzy oddzielnie błąd treningowy, błąd dla świeżych perturbacji i błąd względem prawdziwego score;
- bada wpływ liczby realizacji szumu przypadających na pojedynczy przykład;
- rozróżnia zapamiętanie danych czystych od zapamiętania konkretnych realizacji szumu;
- ocenia stabilność wniosków względem inicjalizacji, rozmiaru zbioru i ustawień optymalizatora.

Projekt wypełnia tę lukę, łącząc analizę krzywych podwójnego spadku z bezpośrednim pomiarem jakości estymacji funkcji score.

---

## 7. Pozycjonowanie projektu

Projekt nie ma na celu wykazania z góry, że zjawisko double descent musi wystąpić. Jego celem jest określenie, czy pojawia się ono w estymacji zależnej od czasu funkcji score, w jakim zakresie pojemności i przy jakiej konstrukcji zbioru uczącego.

Kluczowym elementem odróżniającym projekt od wielu badań nad modelami generatywnymi jest dostęp do rozwiązania analitycznego. Pozwala to uniknąć oceniania generalizacji wyłącznie na podstawie jakości wygenerowanych próbek lub wartości empirycznej funkcji straty.

Planowane badanie rozdziela trzy poziomy dopasowania:

1. **dopasowanie do skończonego zbioru celów treningowych** – zdolność modelu do odtwarzania ustalonych krotek z szumem;
2. **generalizację do nowych perturbacji znanych danych** – jakość predykcji dla świeżych \(t\) i \(\varepsilon\) przypisanych do punktów treningowych;
3. **generalizację populacyjną** – zgodność z analityczną funkcją score na niezależnych punktach przestrzeni.

---

## 8. Główne pytanie i pytania szczegółowe

### Główne pytanie badawcze

**W jaki sposób pojemność sieci neuronowej i sposób generowania zakłóceń wpływają na jakość estymacji funkcji score, zapamiętywanie danych oraz występowanie podwójnego spadku błędu?**

### Pytania szczegółowe

1. Czy wzrost liczby parametrów prowadzi do monotonicznej poprawy błędu względem prawdziwej funkcji score?
2. Czy w pobliżu empirycznie wyznaczonego progu interpolacji pojawia się maksimum błędu walidacyjnego lub błędu prawdziwego score?
3. Czy stałe perturbacje zwiększają różnicę między błędem treningowym a błędem dla świeżych realizacji szumu?
4. Czy ponowne losowanie zakłóceń przesuwa lub wygładza próg interpolacji?
5. Czy wzrost liczby realizacji szumu na przykład zmniejsza memorization i poprawia estymację populacyjną?
6. Czy ewentualny drugi spadek błędu odpowiada rzeczywistej poprawie estymacji score, czy tylko lepszemu dopasowaniu empirycznego celu?
7. Jak wyniki zależą od poziomu szumu \(t\), szczególnie w obszarze małych wartości \(\sigma(t)\)?
8. Jak stabilne są obserwacje względem inicjalizacji, optymalizatora, tempa uczenia i rozmiaru zbioru?

---

## 9. Hipotezy robocze

- **H1:** W reżimie stałych perturbacji zwiększanie pojemności sieci początkowo poprawia estymację score, lecz w pobliżu progu interpolacji zwiększa błąd dla świeżych perturbacji.
- **H2:** Ponowne losowanie szumu ogranicza zapamiętywanie konkretnych realizacji \(\varepsilon\) i zmniejsza wysokość ewentualnego maksimum błędu.
- **H3:** Bardzo duże modele mogą ponownie zmniejszać błąd populacyjny, tworząc drugi spadek, ale efekt będzie zależny od optymalizacji i liczby realizacji szumu.
- **H4:** Rozbieżność między stratą DSM a błędem prawdziwego score będzie największa przy małych poziomach szumu.
- **H5:** Liczba perturbacji na przykład jest niezależną osią złożoności problemu i może przesuwać empiryczny próg interpolacji.

Hipotezy mają charakter falsyfikowalny. Brak double descent lub brak istotnej różnicy między protokołami również będzie wartościowym wynikiem, o ile zostanie potwierdzony w odpowiednio szerokim zakresie konfiguracji.

---

## 10. Operacyjne miary oceny

W celu rozdzielenia generalizacji od memorization należy raportować co najmniej:

- stratę DSM na zbiorze treningowym;
- stratę DSM na ustalonym zbiorze walidacyjnym;
- stratę dla świeżo losowanych perturbacji;
- średni błąd kwadratowy względem \(s^\star(x,t)\);
- błąd względny normy i błąd kierunku pola score;
- błąd w funkcji czasu dyfuzji \(t\);
- różnicę między błędem dla danych treningowych z nowym szumem a błędem dla nowych danych;
- położenie empirycznego progu interpolacji;
- wariancję wyników pomiędzy inicjalizacjami;
- miary jakości rozkładu próbek, np. MMD lub sliced Wasserstein distance;
- odległość generowanych próbek od najbliższych przykładów treningowych.

---

## 11. Oczekiwany wkład

Oczekiwanym wkładem projektu będzie:

1. powtarzalne środowisko do badania estymacji funkcji score na rozkładach o znanym rozwiązaniu;
2. mapa zależności między pojemnością sieci, liczbą perturbacji i błędem generalizacji;
3. procedura empirycznego wyznaczania progu interpolacji dla celu DSM;
4. rozdzielenie zapamiętywania danych od zapamiętywania realizacji szumu;
5. ocena warunków pojawienia się lub braku double descent;
6. publiczne repozytorium kodu, konfiguracji, wyników i dokumentacji;
7. materiał stanowiący podstawę publikacji naukowej oraz rozszerzenia badań na proste zbiory obrazowe.

---

## 12. Bibliografia

[1] P. Nakkiran, G. Kaplun, Y. Bansal, T. Yang, B. Barak, I. Sutskever, *Deep Double Descent: Where Bigger Models and More Data Hurt*, Journal of Statistical Mechanics: Theory and Experiment, 2021, 124003. DOI: 10.1088/1742-5468/ac3a74.

[2] M. Chen, K. Huang, T. Zhao, M. Wang, *Score Approximation, Estimation and Distribution Recovery of Diffusion Models on Low-Dimensional Data*, Proceedings of the 40th International Conference on Machine Learning, PMLR 202:4672–4712, 2023.

[3] X. Gu, C. Du, T. Pang, C. Li, M. Lin, Y. Wang, *On Memorization in Diffusion Models*, arXiv:2310.02664, 2023.

[4] R. Baptista, A. Dasgupta, N. B. Kovachki, A. Oberai, A. M. Stuart, *Memorization and Regularization in Generative Diffusion Models*, arXiv:2501.15785, 2025.

[5] A. J. George, R. Veiga, N. Macris, *Denoising Score Matching with Random Features: Insights on Diffusion Models from Precise Learning Curves*, arXiv:2502.00336, 2025.

[6] C. Zeno, H. Manor, G. Ongie, N. Weinberger, T. Michaeli, D. Soudry, *When Diffusion Models Memorize: Inductive Biases in Probability Flow of Minimum-Norm Shallow Neural Nets*, Proceedings of the 42nd International Conference on Machine Learning, PMLR 267:74240–74270, 2025.

[7] K. Shah, A. Kalavasis, A. Klivans, G. Daras, *Does Generation Require Memorization? Creative Diffusion Models using Ambient Diffusion*, Proceedings of the 42nd International Conference on Machine Learning, PMLR 267:54143–54166, 2025.

[8] X. Zhou, J. Zhang, S. J. Wright, *Smoothing the Score Function for Generalization in Diffusion Models: An Optimization-based Explanation Framework*, Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 2026; arXiv:2601.19285.

---

## 13. Status strony

**Status:** wersja robocza 0.1  
**Następna aktualizacja:** po ustaleniu dokładnego rozkładu 2D, harmonogramu szumu i bazowej architektury sieci.  
**Powiązane planowane strony wiki:** `Research-Questions`, `Experimental-Design`, `Metrics`, `Repository-Structure`, `Reproducibility`, `Experiment-Registry`.
