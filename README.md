## DNS-Ubuntu-Redhat

# DNS Ubuntu Config 🟦

**Install DNS**

`apt install bind9`

**Go to nano /etc/bind/named.conf.options, uncomment these 3 lines and add google DNS below forwarders**

```
forwarders {
     8.8.8.8;
};
```
**Go to `/etc/bind` and copy these files then move it to `/var/lib/bind`**

```
cp db.local db.inova.pt
cp db.local db.31.172
mv db.inova.pt /var/lib/bind/
mv db.31.172 /var/lib/bind/
```
**Go to `nano /var/lib/bind/db.31.172` and config your reverse zone at the bottom (here are some examples)**

```
;
@       IN      NS      inova.pt.
X.0     IN      PTR     control.inova.pt.
X.1     IN      PTR     central.inova.pt.
```
**Go to `nano /var/lib/bind/db.inova.pt` and config your forward zone at the bottom (here are some examples)**

```
;
@         IN      NS      inova.pt.
          IN      A       172.31.0.X
control   IN      A       172.31.0.X
central   IN      A       172.31.1.X
```

**Go to `nano /etc/bind/named.conf.local`, copy this zones and config your zones if needed**

```
zone "inova.pt" {
        type master;
        file "/var/lib/bind/db.inova.pt";
};

zone "31.172.in-addr.arpa" {
        type master;
        file "/var/lib/bind/db.31.172";
};
```
**Restart you DNS**

`systemctl restart bind9`

**Test your DNS**

`nslookup control.inova.pt`

# DNS Redhat Config 🟥

**Install DNS**

`yum install bind`

**Go to `nano /etc/named.conf`, remove and replace those lines**

```
line 13 listen-on port 53 { any; };
line 21 allow-query { any; };
line 35 dnssec-enable no;
line 36 dnssec-validation no;
```
**Go to last line and add above both includes**

```
zone "enta.pt" IN {
        type master;
        file "/var/named/db.enta.pt";

};

zone "31.172.in-addr.arpa" IN {
        type master;
        file "/var/named/db.31.172";

};
```
**Go to the folder `cd /var/named` and copy these**

```
cp named.localhost db.enta.pt
cp named.localhost db.31.172
```
**Change their permissions**

```
chmod named db.enta.pt
chgrp named db.31.172
```

**Restart your DNS server**

`systemctl restart named`

