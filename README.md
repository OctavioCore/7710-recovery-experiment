## 1. Zasilanie główne / wejście CPU VRM

| Punkt                       | Stan                 |      Wynik |
| --------------------------- | -------------------- | ---------: |
| **PL1100 pin 2 / +PWR_SRC** | zasilanie podłączone | **19,6 V** |
| **PL1100 pin 1 / +CPU_B+**  | zasilanie podłączone | **19,6 V** |
| **PC1219**                  | zasilanie podłączone | **19,6 V** |

Czyli napięcie wejściowe sekcji CPU jest obecne.

---

## 2. Szyny ALW

| Punkt                      | Stan      |     Wynik |
| -------------------------- | --------- | --------: |
| **U637 pin 1 / +3.3V_ALW** | zasilanie | **3,3 V** |
| **JIO2 pin 35 / +5V_ALW**  | zasilanie |   **5 V** |

Obie podstawowe szyny ALW są obecne.

---

## 3. Sygnał Power Button

**RC241 pin 2 / `SIO_PWRBTN#`**

* przed Power: **~3,3 V**
* podczas naciskania Power: spadki kolejno do około **1,8 V** i **0 V**
* po zwolnieniu: powrót do **~3,3 V**

Czyli przycisk i wejście EC reagują.

---

## 4. Reset / sleep signals

### `PCH_RSMRST#` — R892 pin 2

* przed Power: **3,3 V**
* podczas Power: **3,3 V**
* po Power: **3,3 V**

Nie zaobserwowaliśmy zaniku tego sygnału.

### `SIO_SLP_S5#` — PR804 pin 1

* przed Power: **~3,4 V**
* po Power: **~3,4 V**

### `SIO_SLP_S4#` — PR206 pin 1

* przed Power: **0 V**
* po Power: **~3,3 V**
* podczas zmiany: bardzo krótki spadek do około **2,6 V**, po czym powrót do **3,3 V**

### `SIO_SLP_S3#` — JIO2 pin 96

* przed Power: **0 V**
* po Power: **3,3 V**

---

## 5. `IMVP_VR_ON`

**PR507 pin 2**

* przed Power: **0 V**
* po Power: **3,3 V**

---

## 6. `PCH_PWROK`

**R1114 pin 2** — według naszego odczytu BoardView:

* przed Power: **0 V**
* po Power: **3,3 V**

Czyli sygnał PCH_PWROK pojawia się.

---

# 7. Główne napięcia CPU / PCH po Power

### `+VCC_CORE`

**PC1236 pin 1**

* przed Power: **0 V**
* podczas startu: krótki skok do około **2 V**
* następnie stabilizuje się około **1,1 V**

### `+VCC_SA`

**PC1706 pin 1**

* przed Power: **0 V**
* po Power: **~1,0–1,1 V**

### `+1.0V_VCCST`

* przed Power: **0 V**
* po Power: **~1,0 V**

Te trzy napięcia pojawiają się po naciśnięciu Power.

---

# 8. Najważniejszy problem — `+VCC_GT`

### `+VCC_GT`

**PC1422 pin 1**

* przed Power: **0 V**
* po Power: tylko bardzo krótki skok około **0,3 V**
* następnie: **0 V**

### `+VCC_GT`

**PC1429 pin 1**

* przed Power: **0 V**
* po Power: bardzo krótki skok około **0,3 V**
* następnie: **0 V**

Czyli oba punkty zachowują się identycznie.

---

# 9. Rezystancje do GND — `+VCC_GT`

Pomiar przy **całkowicie odłączonym zasilaniu**.

### +VCC_GT

* **PC1422 pin 1 → GND:** ~**3 Ω**
* **PC1429 pin 1 → GND:** ~**3 Ω**
* **PL1400 pin 4 (+VCC_GT) → GND:** ~**3 Ω**

Po oczyszczeniu sond ponownie zmierzyłeś PL1400 i nadal było około **3 Ω**.

---

# 10. Sekcja trzech faz VCCGT

Mamy trzy cewki:

* **PL1400**
* **PL1401**
* **PL1402**

Początkowo dostałeś około **4,5–5,5 Ω**, później po oczyszczeniu sond PL1400 ponownie około **3 Ω**.

Następnie sprawdziliśmy dostęp do węzłów fazowych:

* **SW1_3PH_B** przez **R1145 pin 2**
* **SW2_3PH_B**
* **SW3_3PH_B**

### Rezystancja do GND, płyta bez zasilania

* **SW1_3PH_B / R1145 pin 2 → GND:** ~**3 Ω**
* **SW2_3PH_B → GND:** ~**3 Ω**
* **SW3_3PH_B → GND:** ~**3 Ω**

Czyli wszystkie trzy fazy dają podobny wynik.

To jest ważne, bo **nie znaleźliśmy jednej fazy, która wyraźnie odstaje**.

---

# 11. PU1400 — wejście i enable

Na PU1400:

### Pin 6 — VCC

* około **5 V**

### Pin 2 — `DISB#`

* przed Power: **0 V**
* po Power: **5 V**

### Pin 20 — VIN

* przed Power: **19,6 V**
* po Power: **19,5 V**

Czyli układ ma:
**VCC ≈ 5 V + VIN ≈ 19,5 V + DISB# aktywne.**

---

# 12. Sterowanie MOSFET-ami PU1400

### Pin 8 — `GL`

* przed Power: **0 V**
* po Power: po chwili pojawia się około **5 V**

### Pin 28 — `GH`

* podczas startu: **wyraźny impuls**

### PL1400 / `SW1_3PH_B`

**R1145 pin 2 → GND podczas startu:**

* krótki spadek do około **−50…−60 mV**
* następnie powrót do **0 V**

Przy zwykłym multimetrze nie traktujemy tego jako wiarygodnego pomiaru PWM — to tylko odpowiedź DC na bardzo krótki przebieg.

---

# 13. `VCCGT_SENSE`

Podczas startu:

* przed Power: **0 V**
* podczas próby: około **300 mV**
* potem: **0 V**

Czyli SENSE również śledzi krótką próbę podniesienia `+VCC_GT`.

---

# 14. Zachowanie całej płyty

Zaobserwowane praktycznie:

* normalny Power → **brak obrazu**
* brak podświetlenia / brak normalnego POST
* wcześniej brak reakcji Caps Lock
* wentylatory pracują
* CPU Xeon E3-1545M relatywnie szybko się nagrzewa bez coolera - raczej normalne dla tej klasy CPU
* przy braku RAM płyta generuje prawidłowy kod błędu pamięci
* dGPU zostało wyjęte — **bez poprawy**
* recovery **Ctrl+Esc + podłączenie zasilacza działa**
* po pewnych testach płyta czasami wymagała odłączenia/ponownego podłączenia AC, zanim Power znów zadziałał

---

# 15. Test BIOS / ME

Wykonaliśmy clean-ME z Twojego własnego dumpu.

Oryginalny ME:

* **CSME 11.8.79.3722**
* **Corporate H**
* **DA**
* **SVN 3**
* MFS zawierał dane z wcześniej uruchomionego systemu.

Po przygotowaniu `outimage.bin`:

* **11.8.79.3722**
* **Corporate H**
* **Production**
* **TCB SVN 3**
* **Production Ready: Yes**
* **File System State: Configured**
* BIOS Region pozostał zachowany.

Po flashowaniu **zachowanie płyty nie zmieniło się** — nadal dokładnie ten sam objaw: recovery działa, normalny start nie.

---

# 16. RUNPWROK

Podczas startu:

* przed Power: **0 V**
* potem: **3,3 V**

# 17. PCH_DPWROK

Zarówno przed, jak i po power, 3,3v.

## Najważniejszy obraz sytuacji

Na dziś mamy bardzo mocny łańcuch:

**19,5 V → PU1400 ma VIN/VCC → DISB# aktywuje układ → GH/GL próbują sterować → `+VCC_GT` tylko ~0,2–0,3 V → natychmiast spada do 0 V.**

# 18. SYS_PWROK_R

Nawet bez wciśnięcia power, 3.3v.

Jednocześnie:

**VCORE ~1,1 V, VCCSA ~1,0–1,1 V, VCCST ~1,0 V** są obecne.

A **clean-ME nie zmienił zachowania**.

Kolejne kroki: **diagnostyka sprzętowa sekcji VCCGT / obciążenia GT** -> ustalenie, **co dokładnie dzieje się na fazach VCCGT w czasie tych pierwszych kilkuset mikrosekund/milisekund**.


