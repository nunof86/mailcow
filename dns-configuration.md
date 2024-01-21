# DNS Configuration

## Zone Configuration

1. Having in mind the DNS Configuration of the [https://documentation-com.gitbook.io/dns-master-and-slave-configuration/](https://documentation-com.gitbook.io/dns-master-and-slave-configuration/) you can add the following lines in the <mark style="color:red;">`/etc/bind/zones/projeto.com.zone`</mark> of the <mark style="color:red;">**DNS Master**</mark> machine.
2. Change the <mark style="color:red;">`your_ip_address`</mark> to your <mark style="color:red;">IP</mark>.

```dns-zone-file
mail    IN      A       your_ip_address

@       IN      MX 10   mail.projeto.com.
@       IN      TXT     "v=spf1 mx -all"
```

## Resolv.conf Configuration

1. Modify the <mark style="color:red;">`/etc/resolv.conf`</mark> configuration file to match the <mark style="color:red;">**IP**</mark> address of the <mark style="color:red;">**DNS Master**</mark> machine.
2. Change the <mark style="color:red;">`your_ip_address`</mark> to your <mark style="color:red;">IP</mark>.

```bash
nano /etc/resolv.conf
nameserver your_ip_address
```

## Restart the BIND9 Service

```bash
systemctl restart bind9
```
