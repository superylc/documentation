---
id: grafana-dashboard
title: Monitoring and alerting with Grafana
sidebar_label: Monitoring with Grafana
---

Now that you have your validator and node process running, you will probably need a nice dashboard and alert system to ensure at maximum the profitability of your staking ETH. Here is a simple guide that explain how to get one, without any developer skill.

Here is the result of what you will get if you follow this guide!
![Grafana dashboard for prysm node and validator](/img/dashboard_overview.png "Grafana dashboard for prysm node and validator")


## Metrics from validators and node process
First thing you need to do, is to add this flag to the end of the validator command
```
--enable-account-metrics
```

Now you have to make sure that you have access to both metrics:
- on the machine running the node, you will find the node metrics on http://localhost:8080/metrics
- on the machine running the validator, you will find the validator metrics on http://localhost:8081/metrics

## Prometheus
### Installation
[Download Prometheus](https://prometheus.io/download/), then go to the directory where the **prometheus.yml** file is, and edit it to replace the content of the file by this:
```# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).
# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093
# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"
# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  - job_name: 'validator'
    static_configs:
      - targets: ['localhost:8081']
  - job_name: 'beacon node'
    static_configs:
      - targets: ['localhost:8080']
```
> **NOTICE:** If you run your node and validator on different machine you still can regroup your metrics on a single machine.
> For this you will need to get the correct IP depending if you are running both machine in a local network or in a different network.
> 
> [Get your **private IP**](https://docs.prylabs.network/docs/prysm-usage/p2p-host-ip/#private-ip-addresses)
>
> [Get your **public IP**](https://docs.prylabs.network/docs/prysm-usage/p2p-host-ip/#public-ip-addresses)
>
> You can now replace `localhost` in the yml file by the IP address you got for the process that you want to send the metrics to prometheus.
>
> Example: validator running under IP 12.34.56.78, and I want to display the dashboard on the machine where run the node, I will replace `- targets: ['localhost:8081']` by `- targets: ['12.34.56.78:8081']`
>
> In case of public IP you might need to configure your [port forwarding](https://github.com/wgknowles/documentation/blob/15da3fb1ea477f260ef287497fe047b0a78879b3/docs/prysm-usage/p2p-host-ip.md#port-forwarding).


### Verification
Once the **prometheus.yml** file has been updated, you can then run prometheus by double clicking on the **prometheus** file (with extension **.exe** for windows OS),
or with a terminal, using `cd` command to go to the prometheus directory, then issuing this command line
```
prometheus
```
> **NOTICE**: For Windows OS the command will be `prometheus.exe`

This should open a terminal that will display the prometheus log. You should now be able to open this web page http://localhost:9090/graph.

If everything is working, you should see a page similar to this
![Prometheus page](/img/prometheus_page.png "Prometheus page")

Now you have to make sure that you can find both metrics in the previous image: `validator_balance` and `total_voted_target_balances`. You need to find both metrics to have access to all of them.

### Setting the storage time to 31 days (recommended)
  > **NOTICE:** If you don't plan to have panels that will require metrics older than 15 days then you can skip this part
>
You'll probably need to add this flag while running **prometheus** to upgrade to 31 days the storage time of the metrics. By default, this value is set to 15 days
```
--storage.tsdb.retention.time=31d
```

### Windows: Prometheus running in background (facultative)
For that you need to open a **Windows Powershell** prompt, then go to the directory where is located the **prometheus.exe** file (using command `cd`) and to run this:
```
Start-Process "prometheus.exe" "--storage.tsdb.retention.time=31d" -WindowStyle Hidden
```
If you ever want to stop the prometheus process, you can find it under the name **prometheus.exe** in the windows task manager (shortcut ctrl+alt+del), then you can end the task.


## Grafana
### Installation
It’s now time to [download Grafana](https://grafana.com/grafana/download) and install it.
After the installation, you can now open the web page http://localhost:3000. By default, the username and the password are both ‘admin’

Create a data source and choose Prometheus, and enter in the URL field http://localhost:9090 then click on **Save & Test**. You should have a green notification on the corner upper right “Datasource updated”

### Enable alerts

On the left menu of Grafana, select **Notification channels** under the bell icon. Click on **New channel**.
You can now chose the channel you want to create to be alerted. You can find some of them explained below.

#### Telegram
 Select now the type **Telegram** and configure it how you would like the alerts to be done.

To complete the **Telegram API settings** you will have to create a bot and a telegram channel. Follow this [guide](https://gist.github.com/ilap/cb6d512694c3e4f2427f85e4caec8ad7) to create it, **BUT** there is an error in it. At this step
```
Invite @BotFather to that channel as admin
``` 
you should invite your own bot that you just created instead.
Use the **Send test** button to make sure that your bot is sending alerts to your telegram channel. Once it’s working, save it.

You can now create your own alert rules if needed, the guide above explain how.

#### Discord
  Select **Discord** in the type drop down selection. To complete the set up we will need a Webhook URL from discord. 
  
  Head over to discord and create your own server and make a text channel for these alerts. Go to **Server Settings->Webhooks** and select **Create Webhook**. Add a name to your webhook and select the text channel you created for these alerts in the channel drop down selection. Copy the **Webhook URL** and click Save. Your new webhook should appear in the webhook settings page. Make sure you do not share this webhook url with anyone else!
  
  Now head back to the Grafana site where you selected discord as your notification type and paste the Webhook URL in the settings. Click on **Send Test** and you will recieve a test alert in your text channel on your discord server! Click **Save** to finish the set up.

### Creating/importing dashboards
You can now create your own dashboard how you feel like to. Or you can also just import the Prysm dashboards:
- [dashboard designed for small amount of validator keys](../docs/prysm-usage/grafana-dashboards/small_amount_validators.json)
- [dashboard designed for more than 10 validator keys](../docs/prysm-usage/grafana-dashboards/big_amount_validators.json)

This are the json for a node/validator grafana dashboard made by the Prysm community. To import this json into your Grafana dashboard, you click on the **+** icon on the left menu and select Import, you then just have to paste the json and click **Load** button.
