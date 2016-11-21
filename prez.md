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
A possible setup:
<br>
▛▀▀▀▀▀▀▀▀▀▀▀▀▜           ▛▀▀▀▀▀▀▀▀▀▀▜          ▛▀▀▀▀▀▀▀▀▀▀▜
▌  DASHBOARD ▐  ==GET==> ▌          ▐ ==GET==> ▌  AGENTS  ▐
▙▄▄▄▄▄▄▄▄▄▄▄▄▟           ▌          ▐          ▙▄▄▄▄▄▄▄▄▄▄▟
                         ▌PROMETHEUS▐
▛▀▀▀▀▀▀▀▀▀▀▀▀▜           ▌          ▐          ▛▀▀▀▀▀▀▀▀▀▀▜
▌ALERTMANAGER▐ <=POST=== ▌          ▐ ==GET==> ▌  AGENTS  ▐
▙▄▄▄▄▄▄▄▄▄▄▄▄▟           ▙▄▄▄▄▄▄▄▄▄▄▟          ▙▄▄▄▄▄▄▄▄▄▄▟

-------------------------------------------------
-> # Exporters <-

* They're really simple to write.
<br>
* Great support for a lot of programming languages:
  * Go
  * Python
  * Node
  * Ruby
  * etc
<br>
* Simple protocol(Protobuf)
<br>
    from prometheus_client import start_http_server, Summary
    import random
    import time
    # Create a metric to track time spent and requests made.
    REQUEST_TIME = Summary('request_processing_seconds', 'Time spent processing request')
    # Decorate function with metric.
    @REQUEST_TIME.time()
    def process_request(t):
        """A dummy function that takes some time."""
        time.sleep(t)r
    if __name__ == '__main__':
        # Start up the server to expose the metrics.
        start_http_server(8000)
        # Generate some requests.
        while True:
            process_request(random.random())

-------------------------------------------------

-> # Operating your Prometheus setup <-
Simple setup:
    `mkdir tmp
    `curl -L https://github.com/prometheus/prometheus/releases/download/v1.3.1/prometheus-1.3.1.darwin-amd64.tar.gz |tar --strip-components=1 -xz -C tmp

<br>
I heard you are into containers, so why not?
<br>

 ` docker run --restart=always -p 9090:9090 -v /tmp/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
`

# What about the exporters?
* As already mentioned most of them are 0config, so
 `mkdir /opt/node_exporter; curl -Lks https://github.com/prometheus/node_exporter/releases/download/0.12.0/node_exporter-0.12.0.linux-amd64.tar.gz | tar --strip-components=1 -xz -C /opt/node_exporter; /opt/node_exporter/node_exporter&`
