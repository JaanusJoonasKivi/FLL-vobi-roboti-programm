## 📦 Imported Modules

- **color_sensor**
- **hub**
    - button
    - light_matrix
    - port
- **math**
- **motor**
- **motor_pair**
- **runloop**


## 🔹 Function `kaugus_ruudus()`
Arvutab 4-mõõtmelises ruumis asuva kolmnurga hüpotenuusi.
Kasutab kahe 4-mõõtmelise punkti vahelise kauguse ruudu leidmiseks Eukleidese meetodit.

### Parameetrid:
- **cal_punkt** (`list[int]`):
    4-elementiline arvude list, mis esindab kalibreerimispunkti koordinaate.
    Kõik väärtused tõstetakse ruutu.

- **moot_punkt** (`list[int]`):
    4-elementiline arvude list, mille väärtused saadakse värviandurilt tõstetakse ruutu.
    Sellest lahutatakse maha 'cal_punkt', mis on ruutu tõstetud.

### Tagastab:
- **int** – Kahe punkti vahelise kauguse 4 dimensioonis.

## 🔹 Async Function `paar1_soitmine()`
Juhib SPIKE roboti mootorit, võimaldades määrata pööramise suunda, kiirust ja kraadide arvu.
See funktsioon on loodud, kuna SPIKE enda kutsung oli kohmakas.

### Parameetrid:
- **pooramine** (`int`):
    Väärtus vahemikus **-100 kuni 100**, mis määrab mootori pööramise suuna ja kiiruse.
    - **Negatiivne** – pöörab vasakule
    - **Positiivne** – pöörab paremale

- **kiirus** (`int`):
    Mootori kiirus. Täpsed piirangud sõltuvad SPIKE dokumentatsioonist.

- **kraadid** (`int`):
    Pööratavate kraadide arv:
    - `0` – pöörab lõputult, kuni käsitsi peatatakse
    - Positiivne väärtus – pöörab määratud kraadide võrra

- **stop** (`bool`, vaikimisi `False`):
    Kui `True`, peatab mootori enne uue liikumise alustamist.

- **kiirendus** (`int`, vaikimisi `750`):
    Kiirenduse väärtus. Vaata SPIKE dokumentatsioonist maksimaalset lubatud väärtust.

- **aeglustus** (`int`, vaikimisi `750`):
    Aeglustuse väärtus. Vaata SPIKE dokumentatsioonist maksimaalset lubatud väärtust.

## 🔹 Async Function `pooramine()`
Juhib SPIKE roboti mootoreid, kasutades pöördenurki ja protsentuaalset võimsust,
et saavutada täpne pööramine.

### Parameetrid:
- **poor_kraadid** (`int`):
    Pöördenurk kraadides.
    - **Positiivne väärtus** – pöörab vasakule
    - **Negatiivne väärtus** – pöörab paremale

- **vas_mot** (`int`, vaikimisi `50`):
    Vasaku ratta võimsus protsentides (**0–100**).
    - `0%` – ainult parem ratas pöörab
    - `50%` – mõlemad rattad pööravad võrdselt
    - `100%` – ainult vasak ratas pöörab

- **par_mot** (`int`, vaikimisi `50`):
    Parema ratta võimsus protsentides (**0–100**).
    - `0%` – ainult vasak ratas pöörab
    - `50%` – mõlemad rattad pööravad võrdselt
    - `100%` – ainult parem ratas pöörab

- **kiirendus** (`int`, vaikimisi `500`):
    Mootori kiirenduse väärtus.

- **aeglustus** (`int`, vaikimisi `500`):
    Mootori aeglustuse väärtus.

- **kiirus** (`int`, vaikimisi `1050`):
    Maksimaalne mootori kiirus pööramise ajal.

### **Tööloogika:**
1. Arvutab rataste pöördenurga, et saavutada soovitud pöördenurk.
2. Kui mõlemad rattad töötavad **50/50**, siis pöörab võrdselt.
3. Kui üks ratas töötab väiksema protsendiga, siis arvutab teise ratta kiiruse, et pööramine toimuks sünkroonis.
4. Kui vasaku ja parema ratta protsentide summa pole **100**, siis annab veateate.

### **Vead:**
- Kui vasaku ja parema ratta protsentide summa pole täpselt **100**, siis väljastatakse veateade.

## 🔹 Async Function `varvi_kalib()`
Kalibreerib värvisensori jaoks moodulite värvi, mõõtes mitmesuguseid värviandmeid ja salvestades need.

### **Tööloogika:**
1. Kuvab ekraanil kalibreeritava missiooni numbri.
2. Ootab, kuni kasutaja vajutab vasakut nuppu.
3. Kogub 10 mõõtmist värvisensorilt ja arvutab keskmise väärtuse:
    - **Punane**
    - **Roheline**
    - **Sinine**
    - **Tugevus**
4. Salvestab keskmised väärtused kalibreerimisandmetesse.
5. Kuvab "ok?" ja ootab kinnitust või tagasilükkamist:
    - **Parem nupp** – kinnitab kalibreerimise
    - **Vasak nupp** – tühistab kalibreerimise

### **Globaalmuutujad:**
- **varvi_andm** (`dict`):
    Sõnastik, kuhu salvestatakse iga missiooni kalibreerimisandmed.
    - Võti – missiooni number
    - Väärtus – `[punane_kesk, roheline_kesk, sinine_kesk, tugevus_kesk]`

- **kalib_tehtud** (`bool`):
    Märgib, kas kalibreerimine õnnestus (`True`) või katkestati (`False`).'

## 🔹 Function `varvi_tuvastus()`
Tuvastab, milline värvimoodul on all, kasutades vektorarvutust lähima sobivuse määramiseks.

### **Tööloogika:**
1. Loeb värvisensorilt RGBI väärtused (punane, roheline, sinine, tugevus).
2. Arvutab iga kalibreeritud värviandmestiku ja hetkel loetud andmete vahelise kauguse.
    - Kasutab **Eukleidese kaugust ruudus**.
3. Kui minimaalne kaugus on alla määratud läve, tagastab vastava värvimooduli numbri.
4. Kui kaugus on üle läve, tagastab `0` (ei tuvasta värvi).

### **Tagastusväärtus:**
- **int** – tuvastatud värvimooduli number (1, 2, 3, …) või `0`, kui tuvastamine ebaõnnestub.

### **Globaalmuutujad:**
- **varvi_andm** (`dict`):
    Sõnastik, mis sisaldab kalibreeritud värviandmeid.
    - Võti – missiooni number
    - Väärtus – `[punane_kesk, roheline_kesk, sinine_kesk, tugevus_kesk]`

## 🔹 Async Function `soit_1()`
Käivitab esimese missiooni, juhib roboti liikumist ja pööramist vastavalt ettenähtud ülesannetele.

### Tööloogika:
1. Algsättimine: Liikumine ja pööramine.
2. Joondumine haiga ja koralliga: Liikumine määratud kraadide ja kiirusena.
3. Lõpusättimine: Valmistab mootori järgmise sõidu jaoks.

### Sammud:
- Liikumine: 200 kraadi edasi, 40 kraadi pöördega.
- Joondumine haiga ja koralliga, samuti vajalikud pööramised.
- Valmistab mootori ette järgmise sõidu jaoks.

### Globaalmuutujad:
- **motor**: Mootorite juhtimiseks.
- **paar1_soitmine** ja **pooramine**: Funktsioonid liikumiseks ja pööramiseks.

## 🔹 Async Function `soit_2()`
Käivitab teise missiooni, mille käigus robot liigub paadi purje juurde, varastab aarde ja viib sukelduja ära.

### Tööloogika:
1. Liikumine ja pööramine paadi purje juurde.
2. Aarde ja sukelduja liikumisülesanded.
3. Valmistab mootori ette järgmise liikumiseks.

### Sammud:
- Liikumine 910 kraadi edasi, pööramine 47 kraadi.
- Liikumine 300 kraadi edasi, pööramine veel 47 kraadi.
- Liikumine 1200 kraadi edasi aeglustusega.
- Mootor liigub 105 kraadi edasi, 350 kraadi pöördega.
- Tagasi liikumine ja valmistab mootori järgmise liikumiseks.

### Globaalmuutujad:
- **motor**: Mootorite juhtimiseks.
- **paar1_soitmine** ja **pooramine**: Funktsioonid liikumiseks ja pööramiseks.

## 🔹 Async Function `soit_3()`
Käivitab kolmanda missiooni, mille käigus robot viib hai ära, võtab 4 jala ja suundub teise kodu alale.

### Tööloogika:
1. Liikumine ja pööramine hai viimiseks.
2. Liikumine 4 jala võtmiseks ja liikumine teise kodu alale.

### Sammud:
- Liikumine 100 kraadi edasi, pööramine 65 kraadi.
- Liikumine 1400 kraadi edasi.
- Liikumine 4 jala eemaldamiseks ja liikumine teise kodu alale.

### Globaalmuutujad:
- **motor**: Mootorite juhtimiseks.
- **paar1_soitmine** ja **pooramine**: Funktsioonid liikumiseks ja pööramiseks.

## 🔹 Async Function `soit_4()`
Käivitab neljanda missiooni, mille käigus robot lükkab kala ära ja võtab vee proovi.

### Tööloogika:
1. Liikumine kala ära lükkamiseks.
2. Liikumine vee proovi võtmiseks ja liikumine tagasi.

### Sammud:
- Liikumine 200 kraadi edasi, pööramine -54 kraadi.
- Liikumine 2000 kraadi edasi, pööramine 58 kraadi.
- Liikumine 800 kraadi edasi, liikumine tagasi -530 kraadi.

### Globaalmuutujad:
- **motor**: Mootorite juhtimiseks.
- **paar1_soitmine** ja **pooramine**: Funktsioonid liikumiseks ja pööramiseks.

## 🔹 Async Function `soidu_tuvastus()`
Peamine meetod, mis juhib ülesannete lahendamist värvituvastuse põhjal.

### **Tööloogika:**
1. Kontrollib, kas värvid on kalibreeritud (`kalib_tehtud`).
    - Kui EI OLE kalibreeritud → käivitab kalibreerimise (`varvi_kalib`).
2. Kui kalibreerimine on tehtud:
    - Kontrollib, milline missioon on aktiivne (`varvi_tuvastus`).
    - Kuvab aktiivse missiooni numbri LED-ekraanil (`light_matrix.write`).
    - Kui missioon on tuvastatud ja vasak nupp on vajutatud:
        - **1** → käivitab `soit_1()`
        - **2** → käivitab `soit_2()`
        - **3–5** → kuvab konsoolis teate "veel üks sõit?"
    - Kui missioon puudub (`0`) → kuvab `0` LED-ekraanil.

### **Globaalmuutujad:**
- **kalib_tehtud** (`bool`) – Kas värvid on kalibreeritud (`True` või `False`).
- **varvi_tuvastus** – Funktsioon, mis tuvastab hetkel oleva värvi põhjal missiooni numbri.
- **light_matrix** – LED-moodul, mis kuvab aktiivse missiooni numbri.
- **button** – Nuppude olekut jälgiv objekt.

### **Kutsutavad meetodid:**
- `varvi_kalib()` – Kalibreerib värvisensorid.
- `soit_1()` – Käivitab esimese missiooni.
- `soit_2()` – Käivitab teise missiooni.
- `soit_3()` – Käivitab kolmanda missiooni.
- `soit_4()` – Käivitab neljanda missiooni.

### **Vead ja erandid:**
- Kui kalibreerimine ebaõnnestub, võib värvituvastus anda vale tulemuse.
- Kui nuppude olekut ei registreerita õigesti, võib ülesannete käivitamine ebaõnnestuda.


---

🚀 *This Markdown was generated by [SPIKEPyDocstringsToMarkdown](https://lonlof.github.io/SPIKEPyDocstringsToMarkdown/).* 

🔗 *View the project on [GitHub](https://github.com/LonLoF/SPIKEPyDocstringsToMarkdown/).*