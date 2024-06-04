# Monitoring Your Node Using Prometheus and Grafana

This repo is inspired by the repo by Rick [here](https://github.com/ricksteruk/aya_devnet_guide/blob/main/prometheus_guide.md). Thank you so much for this awesome work!

I have just converted the entire process into `docker` and `docker compose` so that users can just run and see the grafana dashboard by just executing some lines of commands.

> This guide will walk you through how to set up Prometheus with Grafana to monitor your node with a basic dashboard. Mainly in a docker environment so that the aya services are not affected by Prometheus and Grafana. Everything will be ready, including Prometheus, Grafana, and Grafana dashboards, etc., just by a single command execution (`docker compose up -d`)!

A Substrate-based chain like Aya exposes data such as the height of the chain, the number of connected peers to your node and more. To monitor this data, Prometheus is used to collect metrics and Grafana allows for displaying them on the dashboard.

It is good practice to install Prometheus and Grafana on separate servers to your node(s) - that way if your node goes down the system can still send you alerts. These should ideally be connected by a VPN such as Tailscale, rather than just opening the relevant ports for Prometheus and Grafana on each server.

For testing purposes, it is possible to run Prometheus and Grafana on the same server as your node. The communication between prometheus and grafana happens by the docker compose's service-name. One container can talk to another container by their service name. 

`THE STEPS IN THIS GUIDE ARE FOR TESTING PURPOSES - DO NOT USE IN A PRODUCTION SETTING`

### Preparation: Installing Docker and Docker Compose

To install Docker on your Ubuntu, run the following commands:

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

sudo usermod -aG docker $USER
```

You can also follow the official documentation to install Docker on your machine: [Docker Installation Documentation](https://docs.docker.com/engine/install/ubuntu/)


### Clone git repo
```
cd /home/${USER}/aya-node
mkdir monitoring_tools
git clone https://github.com/shakilsaddam/aya-devnet-prometheus-guide.git .
```

### Start with docker compose command
```
cd /home/${USER}/aya-node/monitoring_tools
docker compose up -d
```

## Firewall Rule Adjustment
We will just allow port `3000` so we can browse Grafana from outside of the host.
```
sudo ufw allow proto tcp from any to any port 3000
sudo ufw reload
```

You can now access Grafana by going to `http://YOUR_SERVER_IP_ADDRESS:3000/login` in your browser. The default user and password is admin/admin. But, we set the admin password to `admin123` in our `docker-compose.yml` file. You should change this password!!



#### `NOTE - If the panels show "no data" you need to follow the steps bellow` 
```
1. Edit each panel
2. See that the Run queries button is greyed out
3. Go to the Metrics browser section and just add a random character to the end of the value and remove it again
eg. substrate_block_height{status="best"} -> substrate_block_height{status="best"}x -> substrate_block_height{status="best"}
4. This should enable the Run queries button, turning it blue. Click it and you should see it work properly.
5. Apply on each panel and Save the dashboard at the end
```

In the future I will try to integrate some alerts!