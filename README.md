# Analiza wpływu pojemności sieci neuronowych i generowania perturbacji na generalizację estymatorów funkcji score w modelach dyfuzyjnych

Repozytorium badawcze projektu zgłoszonego w ramach **wniosku o przyznanie finansowania z Funduszu Badań Własnych IITiS PAN**.

## Informacje o projekcie

- **Wnioskodawca i wykonawca:** mgr Marzena Halama
- **Kierownik zespołu / promotor:** dr hab. inż. Joanna Domańska
- **Okres realizacji:** 01.08.2026–30.11.2026


## Cel projektu

Celem projektu jest zbadanie:

1. wpływu pojemności sieci neuronowej na jakość estymacji zależnej od czasu funkcji *score* (*time-dependent score function*);
2. wpływu sposobu generowania zakłóceń na generalizację modelu, zapamiętywanie danych oraz występowanie zjawiska podwójnego spadku błędu (*double descent*).

W modelach dyfuzyjnych sieć neuronowa wyznacza kierunek zmian zaszumionej próbki, prowadzących do przybliżenia jej do rozkładu danych. Jakość estymacji funkcji *score* wpływa bezpośrednio na poprawność odszumiania i jakość generowanych próbek.

Projekt koncentruje się na sprawdzeniu, czy zwiększanie liczby parametrów rzeczywiście poprawia odwzorowanie prawdziwej funkcji *score*, czy może prowadzić przede wszystkim do zapamiętywania danych treningowych i konkretnych realizacji szumu.

## Główne pytanie badawcze

> W jaki sposób pojemność sieci neuronowej i sposób generowania zakłóceń wpływają na jakość estymacji funkcji *score*, zapamiętywanie danych oraz występowanie podwójnego spadku błędu?

## Cele szczegółowe

- weryfikacja poprawności implementacji odszumiającego dopasowania funkcji *score* przez porównanie predykcji sieci z funkcją znaną analitycznie;
- zbadanie wpływu szerokości sieci i liczby parametrów na błąd treningowy, walidacyjny oraz błąd względem prawdziwej funkcji *score*;
- porównanie uczenia z ponownym losowaniem zakłóceń z uczeniem na stałym zbiorze zaszumionych przykładów;
- wyznaczenie progu interpolacji;
- analiza warunków występowania zjawiska *double descent*;
- ocena stabilności wyników dla różnych inicjalizacji, rozmiarów zbioru danych i ustawień optymalizatora;
- przygotowanie odtwarzalnego środowiska eksperymentalnego wraz z kodem i dokumentacją.

## Metodyka

Eksperymenty będą prowadzone na kontrolowanym rozkładzie dwuwymiarowym, dla którego prawdziwa funkcja *score* jest znana analitycznie. Umożliwi to bezpośrednie porównanie predykcji sieci z wartością referencyjną.

Porównane zostaną dwa sposoby uczenia:

1. **Ponowne losowanie zakłóceń**  
   Nowe realizacje szumu są generowane w każdym kroku uczenia.

2. **Stały zbiór perturbacji**  
   Model jest uczony na wcześniej przygotowanym, niezmiennym zbiorze zaszumionych przykładów.

Analiza obejmie między innymi:

- błąd treningowy i walidacyjny;
- błąd estymacji względem analitycznej funkcji *score*;
- zależność błędu od liczby parametrów modelu;
- zachowanie modelu w pobliżu progu interpolacji;
- stopień zapamiętywania danych i realizacji szumu;
- stabilność wyników pomiędzy niezależnymi uruchomieniami.

## Planowana struktura repozytorium

```text
.
├── configs/          # konfiguracje eksperymentów
├── data/             # dane wejściowe i wygenerowane zbiory
├── docs/             # dokumentacja projektu
├── experiments/      # skrypty uruchamiające eksperymenty
├── notebooks/        # analizy eksploracyjne i wizualizacje
├── results/          # metryki, wykresy i podsumowania wyników
├── src/              # kod źródłowy
│   ├── data/         # generowanie danych i perturbacji
│   ├── models/       # definicje architektur sieci
│   ├── training/     # procedury uczenia
│   └── evaluation/   # metryki i ewaluacja funkcji score
├── tests/            # testy jednostkowe i integracyjne
├── requirements.txt
└── README.md
```

Struktura może ulec zmianie wraz z rozwojem projektu.

## Uruchomienie

Instrukcja instalacji środowiska i odtwarzania eksperymentów zostanie uzupełniona po przygotowaniu pierwszej wersji kodu.

Planowany sposób uruchomienia:

```bash
python -m venv .venv
source .venv/bin/activate

pip install -r requirements.txt
```

W systemie Windows:

```powershell
python -m venv .venv
.venv\Scripts\activate

pip install -r requirements.txt
```

## Odtwarzalność eksperymentów

Każdy eksperyment powinien zapisywać:

- konfigurację modelu;
- liczbę parametrów;
- sposób generowania perturbacji;
- ziarno losowe;
- parametry optymalizatora;
- rozmiar zbioru treningowego i walidacyjnego;
- historię błędów;
- wyniki względem analitycznej funkcji *score*;
- datę i identyfikator uruchomienia.

## Planowane rezultaty

Efektem projektu będzie:

- implementacja środowiska do kontrolowanych eksperymentów z estymacją funkcji *score*;
- analiza zależności między pojemnością modelu a generalizacją;
- porównanie dwóch sposobów generowania perturbacji;
- identyfikacja warunków występowania progu interpolacji i zjawiska *double descent*;
- przygotowanie wykresów, tabel i dokumentacji wyników;
- przygotowanie publikacji naukowej planowanej do zgłoszenia do czasopisma *Neurocomputing*.

## Powiązane publikacje

1. Nakkiran, P. et al. “Deep Double Descent: Where Bigger Models and More Data Hurt.” *Journal of Statistical Mechanics: Theory and Experiment*, 2021.
2. Chen, M. et al. “Score Approximation, Estimation and Distribution Recovery of Diffusion Models on Low-Dimensional Data.” *Proceedings of the International Conference on Machine Learning*, PMLR, 2023.
3. Zeno, C. et al. “When Diffusion Models Memorize: Inductive Biases in Probability Flow of Minimum-Norm Shallow Neural Nets.” *Proceedings of the 42nd International Conference on Machine Learning*, PMLR, 2025.

## Status projektu

Projekt w trakcie realizacji. Repozytorium będzie sukcesywnie uzupełniane o kod, konfiguracje eksperymentów, wyniki i dokumentację.

## Autorstwo

**Wnioskodawca i wykonawca:**  
mgr Marzena Halama

**Kierownik zespołu / promotor:**  
dr hab. inż. Joanna Domańska

## Informacja o finansowaniu

Repozytorium zostało przygotowane na potrzeby projektu zgłoszonego w ramach wniosku o przyznanie finansowania z Funduszu Badań Własnych. 
