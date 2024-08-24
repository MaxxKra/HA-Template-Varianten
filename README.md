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


