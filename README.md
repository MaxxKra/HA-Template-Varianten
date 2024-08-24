# Verschiedene Template-Sensor Varianten

Es gibt verschiedene Möglichkeiten Template-Sensoren in Home Assistant anzulegen. Eine jede Variante hat seine Berechtigung und seine Vor- bzw. Nachteile. 


In diesem Beispiel werde ich zwei Templates zur Zählung von aktiven `light` und `switch` Entitäten anlegen.


## :one: In `configuration.yaml` mit `platform: template`


Eine veraltete Version welche man immer wieder noch sieht ist einen Sensor in der `configuration.yaml` mit `paltform: template` anzulegen. Es funktioniert nach wie vor, jedoch ist die Verarbeitungszeit dieses Templates um einiges langsamer als die folgenden Varianten.


Der Code zu diesen Template-Sensoren siehr wie folgt aus:

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


