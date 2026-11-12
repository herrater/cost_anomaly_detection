Detekcja anomalii w kosztach transportowych
Repozytorium zawiera kod wykorzystany w części badawczej pracy magisterskiej pt.  
„Porównanie wybranych metod detekcji anomalii w analizie kosztów transportowych na podstawie danych operacyjnych”.
Celem analizy jest porównanie trzech podejść do identyfikacji nietypowych kosztów transportowych:
metody regułowej opartej na 99. percentylu,
Isolation Forest,
Local Outlier Factor (LOF).
Analiza obejmuje przygotowanie danych, agregację do poziomu pojedynczej wysyłki, utworzenie wskaźników kosztowych, porównanie wskazań metod, analizę stabilności oraz ocenę wpływu zestawu cech na wyniki modeli.
Wymagania
Analiza została przygotowana w środowisku Python 3.10.
Wymagane biblioteki:
pandas
numpy
matplotlib
scikit-learn
Jupyter Notebook / JupyterLab
Instalacja zależności:
```bash
python -m pip install -r requirements.txt
```
Pozyskanie danych
W analizie wykorzystano publiczny zbiór Supply Chain Shipment Pricing Data / SCMS Delivery History Dataset.
Źródło danych:
Kaggle: Supply Chain Shipment Pricing Data
pierwotne źródło danych: United States Agency for International Development (USAID)
Strona zbioru na Kaggle:
https://www.kaggle.com/datasets/apoorvwatsky/supply-chain-shipment-pricing-data
Po pobraniu danych plik CSV należy umieścić w katalogu:
```text
data_raw/
```
Notebook wykorzystuje plik:
```text
SCMS_Delivery_History_Dataset_20150929.csv
```
Jeżeli pobrany z Kaggle plik ma nazwę:
```text
SCMS_Delivery_History_Dataset.csv
```
należy zmienić jego nazwę na:
```text
SCMS_Delivery_History_Dataset_20150929.csv
```
lub odpowiednio zmienić nazwę pliku w ścieżce odczytu w notebooku.
Dane źródłowe nie są przechowywane w repozytorium. Repozytorium zawiera wyłącznie kod analizy oraz instrukcję pozyskania danych.
Struktura repozytorium
Zalecana struktura katalogów:
```text
cost_anomaly_detection/
│
├── README.md
├── requirements.txt
├── data_check_rep.ipynb
│
├── data_raw/
│   └── SCMS_Delivery_History_Dataset_20150929.csv
│
└── data_interim/
    └── shipments_cost_cleaned.csv
```
Katalog `data_raw/` zawiera dane źródłowe pobrane przez użytkownika.
Katalog `data_interim/` jest tworzony podczas analizy i służy do zapisania przygotowanego zbioru na poziomie wysyłki.
Uruchomienie analizy
Sklonuj repozytorium:
```bash
git clone https://github.com/herrater/cost_anomaly_detection.git
cd cost_anomaly_detection
```
Opcjonalnie utwórz środowisko wirtualne:
```bash
python -m venv .venv
```
Windows:
```bash
.venv\Scripts\activate
```
Linux / macOS:
```bash
source .venv/bin/activate
```
Zainstaluj wymagane biblioteki:
```bash
python -m pip install -r requirements.txt
```
Pobierz zbiór danych z Kaggle i umieść plik CSV w katalogu `data_raw/`.
Uruchom Jupyter Notebook lub otwórz notebook w Visual Studio Code:
```bash
jupyter notebook
```
Otwórz notebook:
```text
data_check(7).ipynb
```
Uruchom wszystkie komórki kolejno od początku do końca.
Przebieg analizy
Notebook realizuje następujące etapy:
Wczytanie danych źródłowych z kodowaniem `latin1`.
Kontrolę struktury danych i braków.
Konwersję kosztu frachtu i masy do postaci numerycznej.
Identyfikację rekordów referencyjnych na poziomie ASN/DN.
Agregację liczby jednostek i wartości towaru do poziomu pojedynczej wysyłki.
Usunięcie rekordów uniemożliwiających poprawne obliczenie wskaźników.
Utworzenie wskaźników:
`cost_per_kg`,
`cost_per_unit`,
`freight_to_value_ratio`.
Utworzenie cech względnych wobec median właściwych dla `Shipment Mode`.
Identyfikację anomalii metodą 99. percentyla.
Standaryzację cech za pomocą `StandardScaler`.
Zastosowanie Isolation Forest.
Zastosowanie Local Outlier Factor.
Porównanie wskazań metod za pomocą współczynnika Jaccarda.
Analizę wybranych obserwacji anomalnych.
Analizę stabilności względem:
liczby drzew w Isolation Forest,
liczby sąsiadów w LOF,
parametru `contamination`.
Analizę wpływu różnych zestawów cech na wyniki modeli.
Konfiguracja modeli
Konfiguracja główna Isolation Forest:
```text
n_estimators = 300
contamination = 0.0261
random_state = 42
```
Konfiguracja główna Local Outlier Factor:
```text
n_neighbors = 20
contamination = 0.0261
```
Wartość `contamination = 0.0261` odpowiada udziałowi obserwacji wskazanych przez metodę percentylową i została przyjęta w celu zachowania porównywalnej liczby wskazań pomiędzy metodami.
Wartości kontrolne
Po poprawnym wykonaniu głównego pipeline'u powinny zostać uzyskane następujące wartości:
```text
Wiersze w zbiorze źródłowym:                     10 324
Unikalne ASN/DN w zbiorze źródłowym:              7 030
Wysyłki z numerycznym kosztem frachtu i masą:      6 175
Finalny zbiór analityczny:                         6 162

Anomalie – metoda percentylowa:                      161
Anomalie – Isolation Forest:                         161
Anomalie – Local Outlier Factor:                     161
```
Łącznie przynajmniej jedna metoda wskazuje 320 obserwacji jako potencjalnie anomalne.
Porównanie liczby metod wskazujących obserwację:
```text
0 metod: 5842
1 metoda: 199
2 metody: 79
3 metody: 42
```
Współczynnik Jaccarda dla konfiguracji głównych:
```text
Percentyl vs Isolation Forest: 0.5047
Percentyl vs LOF:              0.1882
Isolation Forest vs LOF:       0.1667
```
Eksperyment z zestawami cech
Dodatkowo analizowany jest wpływ sposobu reprezentacji danych na wyniki modeli.
Porównano:
4 cechy podstawowe,
6 cech kosztowych i względnych,
pełny zestaw 10 cech.
Współczynnik Jaccarda względem pełnego zestawu:
```text
Isolation Forest:
- cechy podstawowe:            0.3036
- cechy kosztowe i względne:   0.3529

Local Outlier Factor:
- cechy podstawowe:            0.1709
- cechy kosztowe i względne:   0.3197
```
Eksperyment pokazuje, że zestaw cech wejściowych istotnie wpływa na to, które obserwacje są identyfikowane jako anomalne.
Uwagi dotyczące interpretacji
Zbiór nie zawiera rzeczywistych etykiet określających, które wysyłki stanowią potwierdzone anomalie biznesowe. Z tego względu zgodność wskazań kilku metod nie jest traktowana jako dowód ich rzeczywistej skuteczności ani jako potwierdzenie nieprawidłowości transportu.
Wyniki modeli należy interpretować jako wskazanie obserwacji wymagających dalszej analizy.
Autor
Szymon Cwener  
Praca magisterska, Informatyka stosowana – Technologie informatyczne w Big Data
