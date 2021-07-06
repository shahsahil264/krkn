# Kraken
Chaos and resiliency testing tool for Kubernetes and OpenShift.
Kraken injects deliberate failures into Kubernetes/OpenShift clusters to check if it is resilient to turbulent conditions.


### Workflow
![Kraken workflow](media/kraken-workflow.png)


### How to Get Started
Instructions on how to setup, configure and run Kraken can be found at [Installation](docs/installation.md).

See the [getting started doc](docs/getting_started.md) on support on how to get started with your own custom scenario or editing current scenarios for your specific usage

After installation, refer back to the below sections for supported scenarios and how to tweak the kraken config to load them on your cluster


### Setting up infrastructure dependencies
Kraken indexes the metrics specified in the profile into Elasticsearch in addition to leveraging Cerberus for understanding the health of the kubernetes/OpenShift cluster under test. More information on the features is documented below. The infrastruture pieces can be easily installed, uninstalled by running:

```
$ cd kraken
$ podman-compose up or $ docker-compose up    # Spins up the containers specified in the docker-compose.yml file present in the run directory
$ podman-compose down or $ docker-compose up  # Delete the containers installed
```
This will manage the Cerberus and Elasticsearch containers on the host on which you are running Kraken.

**NOTE**: Make sure to have enough resources ( memory and disk ) on the machine on top of which the containers are running as Elasticsearch is resource intensive. Cerberus monitors the system components by default, the [config](config/cerberus.yaml) can be tweaked to add applications namespaces, routes and other components to monitor as well. Also the command will keep running until killed as detached mode is not supported as of now.


### Config
Instructions on how to setup the config and the options supported can be found at [Config](docs/config.md).

### Kubernetes/OpenShift chaos scenarios supported
Kraken supports pod, node, time/date and [litmus](https://github.com/litmuschaos/litmus) based scenarios.

- [Pod Scenarios](docs/pod_scenarios.md)

- [Container Scenarios](docs/container_scenarios.md)

- [Node Scenarios](docs/node_scenarios.md)

- [Time Scenarios](docs/time_scenarios.md)

- [Litmus Scenarios](docs/litmus_scenarios.md)

- [Cluster Shut Down Scenarios](docs/cluster_shut_down_scenarios.md)


### Kraken scenario pass/fail criteria and report
It's important to make sure to check if the targeted component recovered from the chaos injection and also if the Kubernetes/OpenShift cluster is healthy as failures in one component can have an adverse impact on other components. Kraken does this by:
- Having built in checks for pod and node based scenarios to ensure the expected number of replicas and nodes are up. It also supports running custom scripts with the checks.
- Leveraging [Cerberus](https://github.com/openshift-scale/cerberus) to monitor the cluster under test and consuming the aggregated go/no-go signal to determine pass/fail. It is highly recommended to turn on the Cerberus health check feature avaliable in Kraken. Instructions on installing and setting up Cerberus can be found [here](https://github.com/openshift-scale/cerberus#installation). Once Cerberus is up and running, set cerberus_enabled to True and cerberus_url to the url where Cerberus publishes go/no-go signal in the Kraken config file.
- Leveraging [kube-burner](docs/alerts.md) alerting feature to fail the runs in case of critical alerts.


### Performance monitoring
Monitoring the Kubernetes/OpenShift cluster to observe the impact of Kraken chaos scenarios on various components is key to find out the bottlenecks as it's important to make sure the cluster is healthy in terms if both recovery as well as performance during/after the failure has been injected. Instructions on enabling it can be found [here](docs/performance_dashboards.md).


### Scraping and storing metrics long term
Kraken supports capturing metrics for the duration of the scenarios defined in the config and indexes then into Elasticsearch to be able to store and evaluate the state of the runs long term. The indexed metrics can be visualized with the help of Grafana. It uses [Kube-burner](https://github.com/cloud-bulldozer/kube-burner) under the hood. The metrics to capture need to be defined in a metrics profile which Kraken consumes to query prometheus ( installed by default in OpenShift ) with the start and end timestamp of the run. Information on enabling and leveraging this feature can be found [here](docs/metrics.md).


### Alerts
In addition to checking the recovery and health of the cluster and components under test, Kraken takes in a profile with the Prometheus expressions to validate and alerts, exits with a non-zero return code depending on the severity set. This feature can be used to determine pass/fail or alert on abnormalities observed in the cluster based on the metrics. Information on enabling and leveraging this feature can be found [here](docs/alerts.md).


### Blogs and other useful resources
- Blog post on introduction to Kraken: https://www.openshift.com/blog/introduction-to-kraken-a-chaos-tool-for-openshift/kubernetes
- Discussion and demo on how Kraken can be leveraged to ensure OpenShift is reliable, performant and scalable: https://www.youtube.com/watch?v=s1PvupI5sD0&ab_channel=OpenShift
- Blog post emphasizing the importance of making Chaos part of Performance and Scale runs to mimic the production environments: https://www.openshift.com/blog/making-chaos-part-of-kubernetes/openshift-performance-and-scalability-tests


### Contributions
We are always looking for more enhancements, fixes to make it better, any contributions are most welcome. Feel free to report or work on the issues filed on github.

[More information on how to Contribute](docs/contribute.md)


### Community
Key Members(slack_usernames): paigerube14, rook, mffiedler, mohit, dry923, rsevilla, ravielluri
* [**#sig-scalability on Kubernetes Slack**](https://kubernetes.slack.com)
* [**#sig-scalability on Kubernetes Slack**](https://kubernetes.slack.com)
* [**#forum-chaos on CoreOS Slack internal to Red Hat**](https://coreos.slack.com)
* [**#forum-perfscale on CoreOS Slack internal to Red Hat**](https://coreos.slack.com)
