# Configure the DB for Application Connectivity

## Check listen_addresses
```
grep "^listen_addresses" /etc/postgresql/*/main/postgresql.conf
If you see:

listen_addresses = 'localhost'

change it to:

listen_addresses = '*'

or

listen_addresses = '172.18.0.17'

$sudo vi /etc/postgresql/18/main/postgresql.conf

listen_addresses = '*'          # what IP address(es) to listen on;
```

Restart PostgreSQL
```
sudo systemctl restart postgresql
```
## Edit pg_hba.conf

Add this line above any restrictive rules:
```
host    mydb    postgres    172.19.0.0/16    scram-sha-256
```
If you want to allow all Docker bridge networks:
```
host    all     all         172.16.0.0/12    scram-sha-256
```
The range 172.16.0.0/12 includes Docker networks like:

172.17.x.x
172.18.x.x
172.19.x.x

Reload PostgreSQL
```
sudo systemctl reload postgresql
```

