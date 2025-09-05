# Peeter
**Haru**: main

### Eeltöötlus
Eeltöötluse etapid Jupyter Notebookidena kaustas dataset_preprocessing:
    
1) **restructure_corpora.ipynb** - korpuste wav-failide ja txt-failide eraldamiseks eraldi kaustadesse ja lausete nummerduse muutmine.

2) **resample_folder.sh** - skript wav failide resamplemiseks (22050)
<br>`bash resample_folder.sh <input_directory> <output_directory>`
<br>Nt: `bash resample_folder.sh data/peeter/peeter_wav data/peeter/peeter_wav_resampled`

3) **training_data_transformations_limited_size.ipynb** - eeltöötlus, mis koostab TXT ja WAV kaustade info põhjal treeningfailid Matchale sobivas formaadis. Eemaldab esmalt ka liialt lühikesed ja pikad laused (tekitasid treenimisel OOMi). 

Üksiklausetega kombineeritud eksperiment (eksperiment 3_2):

1) Üksiklausete kausta resample eelneva skriptiga

2) **trim_yksiklaused.sh** - vaikuse eemaldamine, et oleks sarnasem otsekõne ja jutustaja korpustega (kogu vaikus algusest ja 0,5 s lõpust)

3) **yksiklaused.ipynb** - üksiklause korpuse töötlemine ja eelnevatesse treeningandmetesse integreerimine.


### Koodimuudatustest

Lisatud on eesti keele jaoks sobiv "cleaner": *matcha\text\cleaners.py*
<br>Olenevalt treeningandmtest võib olla vaja lisada sümboleid faili: *\matcha\text\symbols.py*, sellega kaasneb ka vastav `n_vocab` parameetri arvu muutmine failis *configs\model\matcha.yaml*

### Treenimine ja kasutamine


Üldine juhend: https://github.com/eisandra/Matcha-TTS?tab=readme-ov-file#train-with-your-own-dataset

*configs/data/peeter.yaml* failis muuta `train_filelist_path` ja `valid_filelist_path` suunama õigetele failidele (või luua uus konfiguratsioon)

Uue korpuse puhul võib luua uued `mel_mean` ja `mel_std` parameetrid  (vt Matcha mudeli treenimise juhendi 5. punkti) ja see samas konfifailis muuta.

Eksperimendi loomine kaustas *configs/data/experiment* (nt fail peeter.yaml)

Jooksutamine sel juhul: 
`python train.py experiment=peeter`

Süntees (kui phonetizer on eeltöötluse osa):
`matcha-tts --text "<INPUT TEXT>" --vocoder --path`


### Ekspermentide kirjeldused:
**Eksperiment 2**
<br>Kasutatud Peetri otsekõne ja jutustaja korpust, eemaldatud liiga lühikesed ja pikad laused. 

**Eksperiment 3.2**
<br>Otsekõne ja jutustaja põhjal tehtud mudeli edasitreenimine üksiklausete korpust lisades. Eelnevalt oli treenitud 114 epohhi otsekõne ja jutustaja peal. Pärast edasitreemist kokku treenitud 250 epohhi.

Lisandunud andmed: Otsekõne ja jutustaja korpusele on lisatud üksiklausete korpus, samuti eemaldatud lühikesed ja pikad näited. Võrreldes eksperimendiga 2 on validatsioonihulka suurendatud, et validationil hindamine oleks sabiilsem. 

# Eva

### Erisus koodis
Tekstitöötluses on phonetizer eemaldatud. Kasutusel on estonian_cleaners_plain_text (ehk ei muudeta mitte midagi). See tähendab, et sünteesi CLIst samamoodi ei saa kasutada nagu Peetri puhul.

### Eksperimentide kirjeldused
**eva_single_speaker**
<br>haru: eva_dev

Treenitud kogu Eva üksiksõnade- ja lausete andmestikul korraga. Helikvaliteet oli väga kõikuv. 

**Eva_multispeaker**
<br>haru: eva_multispeaker_dev

Helikvaliteedi järgi jagasin korpusematerjali kaheks "kõnelejaks". Katsetasin lootuses, et see stabiliseerib single speaker mudeli juures märgatud helikvaliteedi kõikumist. Lahendus paistis ka toimivat, st sünteesikatseuste heli kõikumine oli ilmselt tekkinud seetõttu, et korpused olid salvestatud erineval ajal. Eraldiseisvalt olid "kõnelejad" stabiilsemad, samas kõne kvaliteet kehvemaks ei muutunud.

* spk_0 = yksiksonad_1 + yksiklaused 
* spk_1 = yksiksonad_2

### Treenimine ja kasutamine
Treenimine samamoodi kui Peetri puhul, aga muudetud on cleaner (phonetizer pole kasutusel) ja vajalikud parameetrid.

Süntees (kui phonetizerit pole kasutatud):
<br>Vaata vastavas harus (kas *eva_dev* või *eva_multispeaker_dev*) asuvat **synthesis.ipynb** Notebooki.
