# Mailcow Docker Deployment

## Clone the Repository

1. Firts clone the Mailcow repository.

```bash
git clone https://github.com/mailcow/mailcow-dockerized
```

## Mailcow Configuration

1. Enter the <mark style="color:red;">`mailcow-dockerized`</mark> directory and run the <mark style="color:red;">`generate_config.sh`</mark> script to generate the <mark style="color:red;">`mailcow.conf`</mark> configuration file.&#x20;
2. The script will ask you to fill the following information:

* <mark style="color:red;">`FQDN`</mark>
* <mark style="color:red;">`Timezone`</mark>
* <mark style="color:red;">`Mailcow Branch`</mark>

```bash
cd mailcow-dockerized
./generate_config.sh
```

## Docker Compose

1. Start the WordPress deployment using docker-compose:

```bash
docker-compose up -d
```

2. After that navigate to the dashboard with <mark style="color:red;">`https://your_ip_address`</mark> with the default credencials (<mark style="color:red;">admin</mark>/<mark style="color:red;">moohoo</mark>).
