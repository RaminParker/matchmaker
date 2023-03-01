
# Spinfood Matchmaker


<img src="images\Spinfood_Logo.png"  width="100" height="100">

[Spinfood](https://www.spinfood.de/) - Ein Drei-Gänge-Menü durch deine Stadt

Abhängig vom Los wirst du und dein Kochpartner\*in die Vorspeise, den Hauptgang oder das Dessert kreieren. Dabei empfängt ihr eure Gäste in einer eurer Küchen und wandert von Gang zu Gang zu neuen Kochteams, die euch ebenfalls in ihrer Wohnung begrüßen. Du und dein Kochpartner\*in bleibt immer zusammen, trifft dafür aber bei jedem Gang auf neue Teams. Abschließend trifft sich die ganze Spinfood-Community auf der After-Dinner-Party wieder.

Du kannst dich entweder direkt mit deinem Kochpartner\*in anmelden oder wir losen dir einen passen Teilnehmer zu. Das Los entscheidet ebenfalls über eure Aufgabe. Eure persönliche Dinner-Route und alle relavaten Informationen bekommt ihr einige Tage vor dem Eventstart zugeschickt. Wie immer gilt, egal ob vegan, vegetarisch oder mit Fleisch, es ist für jeden etwas dabei.

<img src="images\Ablaufplan_Skizze.png"  width="330" height="200"> 

## Problemstellung

Wie oben beschrieben haben Teilnehmende die Möglichkeit sich alleine oder direkt mit einem Kochpartner\*in anzumelden. Alle Einzelanmeldungen werden von uns zu Pärchen zusammengefügt und anschließend in einem seperaten Schritt wieder mit den anderen Pärchenanmeldungen zusammengelegt. Bei der Erstellung dieser Pärchen sind einige wichtige Kriterien zu beachten (siehe Abschnitt ``Aufgabenstellung``), welche den Kern dieser Aufgabestellung bilden.

````
Wie können wir die Einzelanmeldungen matchen, damit wir ein Maximum an Zufriedenheit unter den Teilnehmenden erreichen?
```` 

## Datengrundlage 
In dem Jupiter Notebook (Python) findest du Anmeldungen aus einem früheren Event. Die persönlichen Daten der Teilnehmenden haben wir natürlich annonymisiert. Das Dataframe ``df`` besteht aus folgenden 11 Spalten:

````
Data columns (total 11 columns):
 #   Column             Non-Null Count  Dtype  
---  ------             --------------  -----  
 0   ID                 205 non-null    object 
 1   Name               205 non-null    object 
 2   FoodPreference     205 non-null    int64  
 3   Age                205 non-null    int64  
 4   Sex                205 non-null    object 
 5   Kitchen            205 non-null    int64  
 6   Kitchen_Story      205 non-null    float64
 7   Kitchen_Longitude  104 non-null    float64
 8   Kitchen_Latitude   104 non-null    float64
 9   agerange           203 non-null    object 
 10  count_wg           104 non-null    float64
````

- ID: Einzigartiger Hashwert
- Name: Vollständiger Name des Teilnehmenden
- FoodPreference: Essensvorliebe
    - 0: Fleischis
    - 1: Egali (Personen ohne Präferenz)
    - 2: Vegans
    - 3: Veggies
- Age: Alter
- Sex: Geschlecht
    - female
    - male
    - other
- Kitchen: Wird eine Küche als Austragungsort bereitgestellt?
    - 0: Keine Küche vorhanden. Somit keine Gastgeberbereitschaft.
    - 1: Küche vorhanden. 
    - 2: Küche vorhanden, soll aber nur genutzt werden wenn es absolut gar nicht anders geht (Notfall-Küche)
- Kitchen_Story: Stockwerk in dem die Person wohnt. Wert ist auch gegeben wenn keine Küche zum kochen angegeben wurde (``Kitchen = 0``)
- Kitchen_Longitude: Ortskoordinaten des Wohnorts
- Kitchen_Latitude: Ortskoordinaten des Wohnorts
- agerange: Altersstufe
- count_wg: Anzahl weiterer Teilnehmenden mit den selben Ortskoordinaten. Evtl WG-Mitbewohner. 

Der Toy-Datensatz wird in dem Jupyter Notebook geladen. Die dazugehörige csv-Datei findest du in dem Ordner ``data``.

## Aufgabenstellung und Randbedingungen

Das Ziel ist es, möglichst vielen Teilnehmenden einen passenden Kochpartner\*in zuzulosen und ein Maximum an Zufriedenheit unter den Teilnehmenden zu erreichen. 


Alle Teilnehmende werden in drei Gruppen (bzw. Töpfe) eingeordnet.

<img src="images\Gruppen.png"  width="330" height="200"> 

Bei der Zusammenstellung der Gruppen sind einige Dinge zu beachten:

- Es macht natürlich keinen Sinn, einen Fleischliebhaber (wir nennen sie liebevoll _Fleischis_) mit einem _Veganer_ zu matchen. Vorzugsweise erstellen wir Pärchen mit identischen Essensvorlieben. Geht dies nicht ganz auf, ist darauf zu achten, dass sich die Essensvorlieben der Pärchen zumindest nicht ausschließen (wie im Falle von _Veganer_ + _Fleischi_). Besonders hilfreich ist hier die Personengruppe der _egalis_. Sie können als Lückenfüller verwendet werden. Beim Auffüllen gehen wir immer wie folgt vor:
    - Egalis vorzugsweise mit Fleischis zusammen matchen! Ansonsten kommt es zu einer erhöhten Anzahl an Abmeldungen kurz nach der Bekanntgabe der Pärchenzusammenstellung. Beim Auffüllen achten wir darauf, die Essensvorlieben so gut es geht zu matchen. In anderen Worten: Fleischi-Gruppen werden mit Egalis aufgefüllt. Veggie-Gruppen werden mit Fleischis oder Egalis aufgefüllt. Veganer-Gruppen werden mit Vegetariern oder Egalis aufgefüllt. Veganer-Gruppen werden NICHT mit Fleischis aufgefüllt. Veggie-Gruppen werden NICHT mit Veganern aufgefüllt. Zudem werden Egalis bevorzugt zu den Fleischis hinzugefügt, danach zu den Veggies. Wenn möglich nicht zu den Veganern. Vereinzelt aber möglich. 
    - Klingt komplizierter als es eigentlich ist, macht aber eigentlich total Sinn und spiegelt sich klar in der Zufridenheit der Teilnehmenden wieder.
- Es ist darauf zu achten, dass die Geschlechter möglichst durchmischt werden. Das heißt, vorzugsweise werden Pärchen gebildet mit unterschiedlichem Geschlecht.
- Jedes Kochpaar muss über mindestens eine Küche verfügen. Da Küchen immer knapp sind, sollte es vermieden werden, Pärchen zu bilden in denen beide Personen eine Küche stellen könnten. Das wäre eine Verschwendung an Küchen, denn je Paar wird nur eine Küche beansprucht. Pärchen ohne Küchen sind ungültig, da sie nicht kochen und keine Gäste empfangen können.
- Es ist darauf zu achten, die Altersdifferenz bei der Pärchenbildung zu minimieren. So wollen wir z.B. verhindern, dass junge Frauen mit deutlich älteren Männern zuzammengelost werden.
- Natürlich kann es sein, dass es unter den gegebenen Vorrausetzungen, nicht möglich ist jedem Teilnehmenden einen Kochpartner\*in zuzulosen. Die Anzahl dieser Personen soll aber minimiert werden. Je mehr Matches zu stande kommen desto besser. Es ist zu erwarten, dass einige Teilnehmende keinen Kochpartner\*in erhalten werden. Diese Personen speichern wir in einer csv-Datei ab und schieben sie nach, sobald sich andere Teilnehmende vom Event abmelden sollten.

## Bewertung
Um die Qualität der Pärchenbildung zu messen, könnte es sinnvoll sein, eine Bewertungsmatrix zu erstellen, die auf den oben genannten Voraussetzungen basiert. Bislang haben wir jedoch keine gute Bewertungsmethode gefunden. Es zeigt sich, dass die Teilnehmende besonders gerne Pärchen mit gemischten Geschlechter mögen. Hier könnte man vielleicht einen Schwerpunkt setzten.

Im Jupyter Notebook findest Du eine mögliche Pärchenbildung sowie die endgültigen Spalten, die wir für weitere Schritte in unserer Verteilung benötigen:
````
['Einzelanmeldung', 'Person_1', 'Person_1_Age', 'Person_1_AgeRange', 'Person_1_FoodPreference', 
'Person_1_Kitchen', 'Person_1_KitchenLatitude', 'Person_1_KitchenLongitude', 'Person_1_Name', 
'Person_1_Sex', 'Person_1_Story', 'Person_2','Person_2_Age', 'Person_2_AgeRange',
'Person_2_FoodPreference', 'Person_2_Kitchen', 'Person_2_KitchenLatitude',
 'Person_2_KitchenLongitude', 'Person_2_Name', 'Person_2_Sex', 'Person_2_Story']
````


Wir haben unsere Lösung in Python programmiert, aber wir glauben, dass es noch Verbesserungsmöglichkeiten gibt. Für dieses Projekt suchen wir zunächst eine Lösung in Java, aber wir würden uns auch sehr über Lösungsansätze in Python freuen. Mittelfristig brauchen wir eine Lösung in Python.

## Zukünftige Events
Wir planen in Zukunft regelmäßig Events in Marburg durchzuführen. Wir freuen uns über jeden Teilnehmenden. Schau einfach auf unserer [Website](https://www.spinfood.de/) nach oder melde dich in unserer [Spinfood Marburg Telegram Gruppe](https://t.me/spinfoodmarburg) an.

Wir organisieren alle Events in unserer Freizeit. Der Aufwand ist sehr groß. Wir freuen uns über euer Feedback und Unterstützung eurer Fachbereiche.

![myImage](https://media.giphy.com/media/XRB1uf2F9bGOA/giphy.gif)


## Feedback
Fall es Fragen gibt, meldet euch einfach bei uns: hi@spinfood.de


