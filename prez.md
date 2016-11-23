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
EXAMPLE:

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


-------------------------------------------------

-> # So much data! <-

# What is the actual format?
 * A multi-dimensional data model, so that data can be sliced and diced at will, along dimensions like instance, service, endpoint, and method.
 * Concept of matrix and vector
<br>
 Multi-dimensional Vector:

     node_cpu{cluster="stanleyhotel",cpu="cpu23",host="XXXXX.eng.hortonworks.com",instance="YYY.YYY.YYY.1:9100",job="node",mode="softirq"}	27293.85
     node_cpu{cluster="stanleyhotel",cpu="cpu30",host="XXXXX-1n01.eng.hortonworks.com",instance="YYY.YYY.YYY.1:9100",job="node",mode="nice"}	1.06
     node_cpu{cluster="stanleyhotel",cpu="cpu11",host="XXXXX-1n01.eng.hortonworks.com",instance="YYY.YYY.YYY.1:9100",job="node",mode="system"}	329414.79
     node_cpu{cluster="stanleyhotel",cpu="cpu5",host="XXXXX-3n01.eng.hortonworks.com",instance="YYY.YYY.YYY.7:9100",job="node",mode="idle"}	19475734.35
     node_cpu{cluster="stanleyhotel",cpu="cpu6",host="XXXXX-1n02.eng.hortonworks.com",instance="YYY.YYY.YYY.2:9100",job="node",mode="idle"}	19010280.62
     node_cpu{cluster="stanleyhotel",cpu="cpu23",host="XXXXX-3n01.eng.hortonworks.com",instance="YYY.YYY.YYY.7:9100",job="node",mode="system"}	387931.59
     node_cpu{cluster="stanleyhotel",cpu="cpu6",host="XXXXX-3n04.eng.hortonworks.com",instance="YYY.YYY.YYY.10:9100",job="node",mode="idle"}	8594391.36

<br>
Not enough dimensions? Don't worry it gets *better!
<br>

Multi-dimensional Matrices anyone?

        node_cpu{cluster="stanleyhotel",cpu="cpu24",host="XXXXX-2n02.eng.hortonworks.com",instance="YYY.YYY.YYY.5:9100",job="node",mode="nice"}	6.68 @1479822304.424
        6.68 @1479822319.424
        6.68 @1479822334.424
        6.68 @1479822349.424
        node_cpu{cluster="stanleyhotel",cpu="cpu14",host="XXXXX-2n03.eng.hortonworks.com",instance="YYY.YYY.YYY.6:9100",job="node",mode="idle"}	8012476.46 @1479822310.742
        8012490.91 @1479822325.742
        8012505.25 @1479822340.742
        8012519.78 @1479822355.742
        node_cpu{cluster="stanleyhotel",cpu="cpu26",host="XXXXX-1n03.eng.hortonworks.com",instance="YYY.YYY.YYY.3:9100",job="node",mode="user"}	4006265.03 @1479822306.135
        4006265.34 @1479822321.135
        4006265.54 @1479822336.135
        4006265.91 @1479822351.135
        node_cpu{cluster="stanleyhotel",cpu="cpu21",host="XXXXX-1n02.eng.hortonworks.com",instance="YYY.YYY.YYY.2:9100",job="node",mode="irq"}	290.99 @1479822299.505
        290.99 @1479822314.505
        291 @1479822329.505
        291 @1479822344.505


-------------------------------------------------
-> # Data collection != Monitoring <-
# Querying data from Prometheus:
You are collecting all your metrics with Prometheus like crazy. But hey, you will want to run queries on it right?
This is no Graphite after all. Say hello to *PromQL*.

`node_load1 / scalar(count(count(node_cpu{job="node"}) BY (cpu)))` == Load for the last minute.

<br>


Of course you still remember the times when you wrote Nagios configs. *Sigh.
No more. We have a powerful query language and shitload of collected data already.
What if those queries could be used to define alerts?

<br>
Actually they *ARE* used to define Alerts.

      ALERT system_load_over_treshold
        IF node_load1 / scalar(count(count(node_cpu{job="node"}) BY (cpu))) > 0.9
        FOR 1m
        LABELS {severity="critical"}
        ANNOTATIONS {description="Load on {{ $labels.host }} is over 90%", summary="Load on {{ $labels.host }} is over 90%"}

<br>
SCENARIO:
<br>
Pagerduty rings your phone at 3 AM. "Node39 Free space on /  is below 1 Gigs.", It's actually 0. You can't SSH. You are fucked. Thank you very much Nagios for telling me.

<br>
Predictive alerts anyone?

<br>
        predict_linear(node_filesystem_free{device=~"/dev/.*",job="node"}[1h], 4 * 3600)
