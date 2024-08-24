# Verschiedene Template-Sensor Varianten

Es gibt verschiedene Möglichkeiten Template-Sensoren in Home Assistant anzulegen. Eine jede Variante hat seine Berechtigung und seine Vor- bzw. Nachteile. 


In diesem Beispiel werde ich zwei Templates zur Zählung von aktiven `light` und `switch` Entitäten anlegen.


## :one: In `configuration.yaml` mit `platform: template`


Eine veraltete Version welche man noch immer wieder sieht ist einen Sensor in der `configuration.yaml` mit `paltform: template` anzulegen. Es funktioniert nach wie vor, jedoch ist die Verarbeitungszeit dieses Templates um einiges langsamer als die anderen, noch folgenden Varianten.


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
            | count 
          }}
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
            | count 
          }}
```


## :two: In `configuration.yaml` als `template:`


Die aktuell gängigste Variante ist der Eintrag in die `configuration.yaml` als `template:` Diese Variante erhöht die Abfragegeschwindigkeit, sieht aber vom Code her etwas anders aus. Wichtig ist, dass alle Template-Sensoren unter dem Eintrag `template:` und `- sensor:` mit ` - ` angeführt werden. Das Zeichen ` - ` definiert den Beginn eines Listen-Eintrags, womit klar ist, das hier eine Liste an Template-Sensoren erstellt wird.


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
          | count 
        }}
    - name: Zähler Schalter gesamt
      unique_id: "zahler_schalter_gesamt"
      state: >
        {{ states.switch
          | rejectattr('attributes.entity_id', 'defined')
          | selectattr('state', 'eq', 'on') 
          | list 
          | count 
        }}
```


## :three: In `template.yaml` File als `sensor:`


Um die Tempaltes aus der `configuration.yaml` auszulagern gibt es mehrere Möglichkeiten. Eine davon ist, in `config` ein File (Datei) anzulegen und diese z.B. `template.yaml` zu nennen. Um nun Home Assistant mitzuteilen, wo diese Templates zu finden sind, ist ein Eintrag in der `configuration.yaml` notwendig.
Dieser sieht folgendermaßen aus:


```yaml
template: !include template.yaml
```


Nun kann im File `template.yaml` der Code für die Template-Sensoren eingetragen werden. Dieser unterscheidet sich zum vorherigen nur, dass der Begriff `template:` nicht mehr angegeben werden muss, da dieser schon in der `configuration.yaml` angegeben wurde.


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
          | count 
      }}
  - name: Zähler Schalter gesamt
      unique_id: "zahler_schalter_gesamt"
      state: >
      {{ states.switch
          | rejectattr('attributes.entity_id', 'defined')
          | selectattr('state', 'eq', 'on') 
          | list 
          | count 
      }}
```


## :four: In `template` Folder als `Aktiv_Entity_Count.yaml` File mit `sensor:`


Eine andere Möglichkeit die Templates auszulagern ist, unter `config` einen eigenen Folder (Ordner) mit dem Namen z.B. `template` anzulegen. In diesen Ordner werden nun eigene Files (Dateien) für die Templates angelegt. Der Name dieser Files kann individuell und sogar mit Leerzeichen und Umlauten angegeben werden. Wichtig ist, dass der Name mit der Endung `.yaml` versehen wird. Der Vorteil dieser Variante ist die Übersichtlichkeit, da man im Template-Folder die angelegten Files schnell finden kann und nicht mühsam alle Einträge in einem File durchscrollen muss.


Um Home Assistant mitzuteilen, wo in diesem Fall die Templates zu finden sind, ist folgender Eintrag in die `configuration.yaml` notwendig:


```yaml
template: !include_dir_list template
```


Im Falle der Beispiel Templates lautet das File im Template-Folder `Aktiv_Entity_Count.yaml` und der eingetragene Code sieht wie folgt aus:


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

