# Szablon sprawozdania laboratoryjnego

**Przedmiot:** Uczenie głębokie do rozpoznawania obiektów

**Zajęcia nr / temat:** Trenowanie i strojenie klasyfikatora obrazów

**Autor / grupa:** Mateusz Pluta GL5.03

**Data:** 21.10.2026r.

## 1. Cel ćwiczenia

Celem ćwiczenia jest poprawa jakości i generalizacji bazowego klasyfikatora obrazów (MLP) na zbiorze FashionMNIST poprzez zastosowanie różnych technik regularyzacji.Obejmuje to zbadanie wpływu augmentacji danych, Batch Normalization, Dropoutu, Weight Decay oraz harmonogramu współczynnika uczenia (LR scheduler) na krzywe uczenia, a także pomiar luki generalizacyjnej pomiędzy zbiorem treningowym a wydzielonym zbiorem walidacyjnym.

## 2. Metoda i implementacja

**Wykorzystane narzędzia, modele i zbiory danych:**
PyTorch, torchvision, matplotlib

**Model:** MLP

**Zbiór danych:** FashionMNIST, podział na 50 000 (trening), 10 000 (walidacja), 10 000 (test)

**Link do repozytorium/notatnika**
https://github.com/mattty1/Uczenie-glebokie-do-rozpoznawania-obiektow.git

**Konfiguracja**
Trening przeprowadzono przez 15 epok, batch size = 128, optymalizator Adam (lr=1e-3). Stratę dla zbioru treningowego mierzono w trybie eval() na czystych danych bez augmentacji, aby rzetelnie obliczyć lukę. Przetestowano 4 konfiguracje:

Baseline - czysty model, brak augmentacji i regularyzacji

Wariant 1 - Tylko augmentacja: losowe odbicia i wycięcia z paddingiem

Wariant 2 - Augmentacja + Batch Normalization + Dropout p=0.3

Wariant 3 - Pełna regularyzacja: Wariant 2 + Weight Decay 1e-4 + StepLR
## 3. Wyniki

| Konfiguracja | Wybrana epoka | Accuracy walidacyjna | Luka train-validation (strata) |
|---|---|---|---|
| Baseline | 5 | 0.89 | 0.06 |
| Wariant 1 (Augmentacja) | 15 | 0.84 | 0.02 |
| Wariant 2 (Aug + BN + Dropout) | 15 | 0.83 | 0.017 |
| Wariant 3 (Pełna regularyzacja) | 15 | 0.82 | 0.016 |

Accuracy testowa dla końcowych modeli (ocena ostateczna):

Baseline: 0.8847

Najlepszy wariant (Baseline): 0.8847

![alt text](<Krzywe straty.png>)

![alt text](<Krzywe straty - najlepszy model.png>)

## 4. Analiza i interpretacja

Wyniki i wykresy krzywych uczenia jednoznacznie obrazują problem przeuczenia w modelu bazowym (Baseline). W przypadku Baseline strata treningowa szybko maleje, podczas gdy strata walidacyjna zatrzymuje się, a następnie rośnie – generując szeroką lukę generalizacyjną.

Wprowadzenie augmentacji i technik regularyzacji (szczególnie dropoutu) znacząco domyka tę lukę. Pomiary straty treningowej "w locie" podczas uczenia przy włączonym Dropoucie dają zniekształcony obraz, dlatego kluczowe było użycie funkcji evaluate() na czystych danych w celu faktycznej oceny błędu na zbiorze uczącym (linia "Czysta ocena treningu").

Zwężona luka w Wariancie 3 sugeruje odporność modelu na zapamiętywanie danych, a zastosowanie schedulera spowolniło proces i pomogło bezpiecznie obniżyć stratę walidacyjną, nie doprowadzając do niedouczenia.

## 5. Wnioski

Zastosowanie rygorystycznej regularyzacji i augmentacji uodparnia model na "wykuwanie na pamięć" zbioru treningowego, co z reguły prowadzi do wyższej dokładności (accuracy) na niewidzianym wcześniej zbiorze testowym. Do ewaluacji hiperparametrów i ostatecznego wyboru najlepszego modelu (najlepszego checkpointu) konieczne jest wydzielenie stałego zbioru walidacyjnego. 

---

## Kryteria oceny (rubryka)

| Element | Waga |
|---|---|
| Poprawność implementacji | 40% |
| Wyniki i ich interpretacja | 30% |
| Czytelność kodu (PEP8, komentarze) | 20% |
| Wnioski | 10% |

Próg zaliczenia sprawozdań (O1): 90%. Oddanie: do tygodnia po zajęciach.
