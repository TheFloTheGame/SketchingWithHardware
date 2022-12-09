---
title: "Microcontroller"
description: "Rumspielen mit Microcontrollern"
date: 2022-12-07T10:22:21+01:00
type: "post"
draft: false
---

In den Labs habe ich verschiedene Sensoren ausprobiert und teilweise Libraries dazu eingebunden.
Am Anfang mussten wir alle eine LED zum Leuchten bringen, wo man eigentlich nur auf den Widerstand und die Fließrichtung des Stroms achten muss. Je niedriger der Widerstand
umso heller leuchtet die LED, allerdings sinkt dann auch die Lebenszeit.
<img src="led.JPEG" alt="Ein Multimeter auf einem Tisch" width="300"/>
Danach habe ich mithilfe des Ultraschallsensors die Entfernungen zu verschiedenen Dingen gemessen
<img src="ultrasonic_top.JPEG" alt="Ein Multimeter auf einem Tisch" width="300"/>
Als nächstes habe ich mit dem DHT11 die Temperatur und Luftfeuchtigkeit im Raum gemessen. Dafür habe ich eine Library verwendet, was auch ganz gut funktioniert hat.
<img src="temperature.JPEG" alt="Ein Multimeter auf einem Tisch" width="300"/>
Mithilfe eines Mikrofons habe ich einen Piezo zum Piepsen gebracht. Sobald ein bestimmter Lautstärkepegel erreicht wurde hat der Piezo gepiepst. Das hat nicht ganz so gut funktioniert wie ich dachte, weil das Mikro manchmal gar keine Geräusche wahrgenommen hat. Womöglich war ein Kabel nicht richtig angeschlossen, aber fixen konnte ich das leider nicht.
<img src="micro_and_piezo.JPEG" alt="Ein Multimeter auf einem Tisch" width="300"/>
Hier noch eine Seitenansicht davon:
<img src="micro_and_piezo_side.JPEG" alt="Ein Multimeter auf einem Tisch" width="300"/>
Später habe ich etwas probiert, was sich als Gassensor rausgestellt hat. Diese Version davon konnte den CO2-Gehalt des Raums messen, was ich auch mithilfe einer Library gemacht habe. 
<img src="gas_sensor.JPEG" alt="Ein Multimeter auf einem Tisch" width="300"/>
Hinterher habe ich auf einem Raspberry Pi einmal das RetroPie-OS aufgespielt. Eigentlich habe ich das sogar zweimal gemacht, weil die Zuweisung der Tasten etwas irritierend war und es auch nicht so einfach ändern konnte. Beim zweiten mal hat das Ganze aber ganz gut funktioniert, allerdings hat sich das Betriebssystem ziemlich langsam angefühlt, was wohl am recht schwachen Prozessor des Raspberry Pi der 1. Generation liegt.
<img src="raspberry_pi.JPEG" alt="Ein Multimeter auf einem Tisch" width="300"/>
Anschließend habe ich probiert eine LED-Matrix zum Laufen zu bekommen, was ich aber leider nicht hingekriegt habe. Auch mit Libraries hat das aus irgendeinem Grund nicht funktioniert.
<img src="led_matrix.JPEG" alt="Ein Multimeter auf einem Tisch" width="300"/>
In dem folgenden Beispiel wird ein Interrupt verwendet, die eine LED zum Leuchten bringt, sobald ein Button gedrückt wird. Normalerweise blinkt die LED aufgrund der ``loop()``, aber sobald sich die Spannung des Buttons verändert, springt der Arduino zur ``buttonPush()``-Methode. Das wird durch den ``CHANGE`` Interrupt Modus realisiert. Weite Modi wären ``RISING`` (Spannung steigt), ``FALLING`` (Spannung fällt) und ``LOW`` (Spannung ist niedrig).

```cpp
    #define buttonPin = 2;
    #define ledPin = 4;

    int buttonState = 0;

    void setup() {
        pinMode(ledPin, OUTPUT);
        pinMode(buttonPin, INPUT);
        attachInterrupt(0, buttonPush, CHANGE);
    }

    void loop() {
        digitalWrite(ledPin, HIGH);
        delay(1000);
        digitalWrite(ledPin, LOW);
    }

    void buttonPush() {
        buttonState = digitalRead(buttonPin);
        digitalWrite(ledPin, buttonState);
    }
```

Um Energie zu sparen bietet der Arduino verschiedene Sleep Modes an. Im ``Idle`` wird nur die CPU-Clock und die Flash-Clock ausgeschaltet, der ``ADC Noise Reduction`` wird verwendet um bei Analog-Digital-Umwandlungen Rauschen zu reduzieren, der ``Power-down`` ist der stromsparendste Sleep Mode. Hier funktionieren nur externe Interrupts, TWI oder Watchdog Interrupts. ``Power-save`` ist so ähnlich, aber der Timer2 ist noch aktiv und kann über externe Taktgeber betrieben werden. Im ``Stand-by`` läuft noch der Systemoszillator und wird gewählt, wenn man schnelle Wake Ups benötigt und anschließend gibt es noch den ``Extended Standby``, der zusätzlich noch den Timer2 aktiv hat.
Mithilfe von ``set_sleep_mode(SLEEP_MODE_STANDBY)`` kann man beispielsweise den Sleep Mode auf Stand-by setzen und mit ``sleep_mode()`` wird dann der Sleep Mode aktiviert.