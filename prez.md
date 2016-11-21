%title: Monitoring with Prometheus
%author: deathowl
%date: 2016-11-21

-> $ whoami <-
=========

-> Bálint Csergő (deathowl) <-

-> _Social:_ <-

Twitter      *@abagoly*

Blog         *https://www.deathowlsnest.com/*

Linkedin     *www.linkedin.com/in/deathowl*

Github:     * deathowl *

-> _Code:_ <-
* Python
* Go
* Node

-> _Past:_ <-
* Ustream (Backend dev, Infra)
* Endticket (Infra)

-> _Current_ <-
Devops @ Hortonworks
                 .-..\_____
              ____/\`       \'\\.
           .-'  (    (   a \\
          /      (    \\,_   \\
         /|       \'---\` |\\ =|
        \` \\    /__.-/  /  | |
           |  / / \\ \\  \\   \\_\\
           |__|_|  |_|__\\



-------------------------------------------------

-> # Prometheus <-
* Prometheus is:
<br>

    * Monitoring system
<br>

    * Built for microservices
<br>

    * Is composed of microservices
<br>

    * Initial version developed at Soundcloud, later open sourced, moved to CNF(Cloud Native Foundation)
<br>

    * Go
<br>

    * Pull modell
<br>

    * Timeseries DB, Charting solution, Rule engine
<br>

    * Hard to understand at first
<br>

    * Scalable
<br>

* Prometheus isn't:
<br>

    * a silver bullet
<br>

    * Nagios
<br>

    * the best solution for all usecases
<br>

    * too hard to understand

-------------------------------------------------

-> # Architecture <-
Consists of microservices
Pull model: agents running on hosts monitored
Advantages to push:
<br>
* You can run your monitoring on your laptop when developing changes.
<br>
* You can more easily tell if a target is down.
<br>
* You can manually go to a target and inspect its health with a web browser.
<br>
* Target/Service discovery.
<br>
* Better for HA: No floating IP required for monitoring master.
<br>
* Most of the agents are zero-config
<br>
* ALERTING:
 * ALERTMANAGER
 * Alerta
 * Simple, well defined API, easy to integrate
<br>                   
* Visualization of the collected data
  * Grafana
  * Promdash
  * Simple JSON API (Roll your own?)

<br>
CHART INCOMING:
<br>
▛▀▀▀▀▀▀▀▀▀▀▀▀▜           ▛▀▀▀▀▀▀▀▀▀▀▜          ▛▀▀▀▀▀▀▀▀▀▀▜
▌  DASHBOARD ▐  ==GET==> ▌          ▐ ==GET==> ▌  AGENTS  ▐
▙▄▄▄▄▄▄▄▄▄▄▄▄▟           ▌          ▐          ▙▄▄▄▄▄▄▄▄▄▄▟
                         ▌PROMETHEUS▐
▛▀▀▀▀▀▀▀▀▀▀▀▀▜           ▌          ▐          ▛▀▀▀▀▀▀▀▀▀▀▜
▌ALERTMANAGER▐ <=POST=== ▌          ▐ ==GET==> ▌  AGENTS  ▐
▙▄▄▄▄▄▄▄▄▄▄▄▄▟           ▙▄▄▄▄▄▄▄▄▄▄▟          ▙▄▄▄▄▄▄▄▄▄▄▟

-------------------------------------------------
