# Verschiedene Template-Sensor Varianten

Es gibt verschiedene Möglichkeiten, Template-Sensoren in Home Assistant anzulegen. Jede Variante hat ihre Berechtigung sowie Vor- und Nachteile.


In dieser Beschreibung behandle ich folgende Varianten:


1. In `configuration.yaml` mit `platform: template`
2. In `configuration.yaml` als `template:`
3. In `template.yaml` File als `sensor:`
4. In `template` Folder als `Aktive Entitäten Zähler.yaml` File mit `sensor:`
5. Als Template - Sensor, in den Helfern angelegt


Als Beispiel werde ich in jeder der Varianten, zwei Templates zur Zählung von aktiven `light` und `switch` Entitäten anlegen.


<br>


### <p align="center">Wenn du Interesse daran hast, mich, meinen Kanal oder meine kreative Arbeit zu unterstützen,<br>freue ich mich über jeglichen Support:
</p>
<p align="center">
  <a href="https://www.buymeacoffee.com/bastler">
    <img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/buy_me_a_coffee_logo.png" alt="Buy Me A Coffee" width="150">
  </a>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://www.paypal.me/kramlmaxx">
    <img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/paypal_donate_logo.png" alt="PayPal Donate" width="150">
  </a>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://www.amazon.de/hz/wishlist/ls/3FT7MNGRVOTM3?ref_=wl_share">
    <img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/amazon_wishlist_logo.png" alt="Amazon Wishlist" width="150">
  </a>
</p>

### <p align="center">Danke</p>

<br>


## :one: In `configuration.yaml` mit `platform: template`


Eine veraltete Version, die man noch immer wieder sieht, ist das Anlegen eines Sensors in der `configuration.yaml` mit `platform: template`. Diese Methode funktioniert nach wie vor, jedoch ist die Verarbeitungszeit dieses Templates deutlich langsamer als bei den anderen, noch folgenden Varianten.


Der Code zu diesen Template-Sensoren sieht wie folgt aus:


```yaml
#-----------------------------------------------------------
# Sensor - Template, eingetragen in der configuration.yaml
#-----------------------------------------------------------
sensor:
  - platform: template
    sensors:
      zahler_lights_gesamt:
        friendly_name: "Zähler Lights gesamt"
        unique_id: "zahler_lights_gesamt"
        value_template: >
          {{ states.light 
            | rejectattr('attributes.entity_id', 'defined')
            | selectattr('state', 'eq', 'on') 
            | list 
            | count }}
  - platform: template
    sensors:
      zahler_schalter_gesamt:
        friendly_name: "Zähler Schalter gesamt"
        unique_id: "zahler_schalter_gesamt"
        value_template: >
          {{ states.switch 
            | rejectattr('attributes.entity_id', 'defined')
            | selectattr('state', 'eq', 'on') 
            | list 
            | count }}
```


## :two: In `configuration.yaml` als `template:`


Die aktuell gängigste Variante ist der Eintrag in die `configuration.yaml` als `template:` Diese Variante erhöht die Abfragegeschwindigkeit, sieht aber vom Code her etwas anders aus. Wichtig ist, dass alle Template-Sensoren unter dem Eintrag `template:` und `- sensor:` mit ` - ` angeführt werden. Das Zeichen ` - ` definiert den Beginn eines Listeneintrags, womit klar ist, dass hier eine Liste an Template-Sensoren erstellt wird.


Der Code zu diesen Template-Sensoren sieht wie folgt aus:


```yaml
#-----------------------------------------------------------
# Template - Sensor, eingetragen in der configuration.yaml
#-----------------------------------------------------------
template:
  - sensor:
    - name: Zähler Lights gesamt
      unique_id: "zahler_lights_gesamt"
      state: >
        {{ states.light 
          | rejectattr('attributes.entity_id', 'defined')
          | selectattr('state', 'eq', 'on') 
          | list 
          | count }}
    - name: Zähler Schalter gesamt
      unique_id: "zahler_schalter_gesamt"
      state: >
        {{ states.switch
          | rejectattr('attributes.entity_id', 'defined')
          | selectattr('state', 'eq', 'on') 
          | list 
          | count }}
```


## :three: In `template.yaml` File als `sensor:`


Um die Templates aus der `configuration.yaml` auszulagern, gibt es mehrere Möglichkeiten. Eine davon ist, im Verzeichnis `config` eine Datei anzulegen und diese z.B. `template.yaml` zu nennen. Um nun Home Assistant mitzuteilen, wo diese Templates zu finden sind, ist ein Eintrag in der `configuration.yaml` notwendig. Dieser sieht folgendermaßen aus:


```yaml
template: !include template.yaml
```


Nun kann im `template.yaml`-File der Code für die Template-Sensoren eingetragen werden. Dieser unterscheidet sich zum vorherigen lediglich dadurch, dass der Begriff `template:` nicht mehr angegeben werden muss, da dieser bereits in der `configuration.yaml` definiert wurde.


```yaml
#-----------------------------------------------------------
# Template - Sensor, eingetragen in Template File
#-----------------------------------------------------------
- sensor:
  - name: Zähler Lights gesamt
      unique_id: "zahler_lights_gesamt"
      state: >
      {{ states.light 
          | rejectattr('attributes.entity_id', 'defined')
          | selectattr('state', 'eq', 'on') 
          | list 
          | count }}
  - name: Zähler Schalter gesamt
      unique_id: "zahler_schalter_gesamt"
      state: >
      {{ states.switch
          | rejectattr('attributes.entity_id', 'defined')
          | selectattr('state', 'eq', 'on') 
          | list 
          | count }}
```


## :four: In `template` Folder als `Aktive Entitäten Zähler.yaml` File mit `sensor:`


Eine andere Möglichkeit, die Templates auszulagern, ist das Anlegen eines eigenen Folders (Ordners), z.B. `template`, unter dem Verzeichnis `config`. In diesem Ordner können nun eigene Files (Dateien) für die Templates angelegt werden. Der Name dieser Dateien kann individuell sein und sogar Leerzeichen und Umlaute enthalten. Wichtig ist, dass der Name mit der Endung `.yaml` versehen wird. Der Vorteil dieser Variante ist die Übersichtlichkeit, da man im Template-Ordner die angelegten Dateien schnell finden kann und nicht mühsam alle Einträge in einer einzigen Datei durchsuchen muss.


Um Home Assistant mitzuteilen, wo in diesem Fall die Templates zu finden sind, ist folgender Eintrag in die `configuration.yaml` notwendig:


```yaml
template: !include_dir_list template
```


Im Falle der Beispiel-Templates lautet das File im Template-Folder `Aktiv_Entity_Count.yaml` und der eingetragene Code sieht wie folgt aus:


```yaml
#-----------------------------------------------------------
# Template - Sensor, eingetragen in Template Folder
#-----------------------------------------------------------
sensor:
  - name: "Zähler Lights gesamt"
    unique_id: zahler_lights_gesamt
    icon: mdi:lightbulb-question-outline
    state: >
      {{ states.light 
      | rejectattr('attributes.entity_id', 'defined')
      | selectattr('state', 'eq', 'on') 
      | list 
      | count }}
  - name: "Zähler Schalter gesamt"
    unique_id: zahler_schalter_gesamt
    icon: mdi:help-network-outline
    state: >
      {{ states.switch 
      | rejectattr('attributes.entity_id', 'defined') 
      | selectattr('state', 'eq', 'on') 
      | list 
      | count }}
```


## :five: Template - Sensor, in den Helfern angelegt


Eine relativ neue Variante im Jahr 2024 ist es, Templates in den Helfern anzulegen.


Dazu gehe in den <img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/HA ICONS/Einstellungen.jpg" alt="Icon" height="40"/> auf <img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/HA ICONS/Gerate_und_Dienste.jpg" alt="Icon" height="40"/> und dann oben auf <img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/HA ICONS/Helfer.jpg" alt="Icon" height="40"/>.


Dann klicke rechts unten auf <img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/HA ICONS/Helfer_Erstellen.jpg" alt="Icon" height="40"/>.


Nun sollte folgende Fenster angezeigt werden, in welchen die rot umrandeten Bereiche ausgewählt werden.


<img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/HA FENSTER/Helfer_Template.jpg" alt="Icon" width="600"/>


<img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/HA FENSTER/Template_fur_einen_Sensor.jpg" alt="Icon" width="600"/>


<img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/HA FENSTER/Zahler_Lights_gesamt.jpg" alt="Icon" width="600"/>


Dieser Code wird im Feld `Zustandstemplate` eingetragen:


```yaml
{{ states.light 
  | rejectattr('attributes.entity_id', 'defined')
  | selectattr('state', 'eq', 'on') 
  | list 
  | count }}
```


```yaml
{{ states.switch 
  | rejectattr('attributes.entity_id', 'defined')
  | selectattr('state', 'eq', 'on') 
  | list 
  | count }}
```


Nach dem Bestätigen ist es am Besten, den Browser zu aktualisiern und der Template-Sensor ist angelegt und sollte Werte liefern.


## :six: Allgemeine Infos zum Schluss


Wenn bei den mit YAML Code eingetragenen Template-Sensoren eine `uniqu_id` angegeben wird, werden diese Template-Sensoren auch in den Helfern angezeigt und können zur Kategorie-Zuweisung und für Labels bearbeitet werden. Dies mach ein Suche im späteren Verlauf viel einfacher.


<img src="https://raw.githubusercontent.com/MaxxKra/README_images/master/Allgemein/HA FENSTER/Zahler_Lights_gesamt_Labels.jpg" alt="Icon" width="800"/>