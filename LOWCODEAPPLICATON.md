# APEX and ORDS Docker on Linux OS installation 
# 

### Disclaimer
This repository's content is primarily written in tagalog/taglish to better suit my workflow and communication preferences. If you encounter any difficulties understanding the material, feel free to reach out or use translation tools. Thank you for your understanding!
# 

### PANIMULA 
siguradihin ang docker ay nagagamit sa inyong mga "server" or "machine" sunod ay Kunin ang "database images" gamit ito `docker pull container-registry.oracle.com/database/free:latest` at kung meron laktwan ang prosesong ito
# 
### Gumawa: Volume at Network 
`docker network create apex-network`

`docker volume create ords_config`

`docker volume create oracle_volume`
# 

### KUHANIN AT "EXTRACT": "Apex" 

`curl -o apex.zip https://download.oracle.com/otn_software/apex/apex-latest.zip`

`unzip -q apex.zip`

`chmod -R 755 apex`
# 

### GUMAWA: container para sa "database"

`docker run -it -d --name Bookkeep --network=apex-network -p 1521:1521 -e ORACLE_PWD='E' -v oracle_volume:/opt/oracle/oradata -v {pwd}/apex:/tmp/apex container-registry.oracle.com/database/free:latest`
# 

### SUNDIN:
i-access ang docker bash gamit ang `docker exec -it Bookkeep bash` sunod ay siguradhin ang "location" ay nasa /tmp/apex **sunod mag "login as SYS"** `sqlplus sys/bookkeep123@localhost:1521 as sysdba` 

**Kopyahim ang sumusunod pa isa isa!** 

`CREATE PLUGGABLE DATABASE Book admin user Keeper IDENTIFIED BY Q FILE_NAME_CONVERT = ('pdbseed','book');`

`ALTER PLUGGABLE DATABASE Book  OPEN;`

`ALTER PLUGGABLE DATABASE Book  SAVE STATE;`

`ALTER SESSION SET WORKAREA_SIZE_POLICY = AUTO;`

`CREATE TABLESPACE APEX
DATAFILE 'APEX.dbf'
SIZE 500M
AUTOEXTEND ON NEXT 100M
MAXSIZE UNLIMITED;`

`ALTER SESSION SET CONTAINER = Book;`
# 

### Pag "install" Apex instance sa iyong "database"
**kopyahin** `@apexins.sql SYSAUX SYSAUX TEMP /i/` pag natapos na ang pag "install" sunod ay ayusin ito at alingsunod sa iyong gusto "Password"

`ALTER USER anonymous ACCOUNT UNLOCK;`

`ALTER USER flows_files ACCOUNT UNLOCK;`

`ALTER USER APEX_PUBLIC_USER IDENTIFIED BY "<password>" ACCOUNT UNLOCK;`

`ALTER USER APEX_PUBLIC_ROUTER  IDENTIFIED BY "<password>" ACCOUNT UNLOCK;`

# 

### ayusin ang "APEX ADMINISTRATOR"
`@apxchpwd.sql`

ADMIN user : ADMIN 

EMAIL : ADMIN 

PASSWORD : E

# 

### Ayusin ang "Proxy user"
`@apex_rest_config.sql` i-set ang password sa "E" ayusin alin sunod sa iyong gusto

`ALTER USER APEX_PUBLIC_USER ACCOUNT UNLOCK; `

`ALTER USER APEX_REST_PUBLIC_USER IDENTIFIED BY "<password>";`

`ALTER USER APEX_LISTENER  ACCOUNT UNLOCK;`

`ALTER USER APEX_LISTENER IDENTIFIED BY "<password>";`

# ORDS sundin ang mga sumusunod
mag open ng bagong terminal, kunin ang "ords latest imgage" gamit ito`docker pull container-registry.oracle.com/database/ords:latest` 

at gawin ito `password.text` i-edit ito at `cat password.text`resulta dapat ay

`<password of admin user or sys as sysdba>` 

`<passowrd of proxy user or apex public user>`

### "CONTAINER": pag gawa at pag install
`cat password.txt | docker run --rm -i --network=apex-network \
    -v ords_config:/etc/ords/config \
    container-registry.oracle.com/database/ords:latest  install --admin-user SYS --proxy-user --db-hostname 172.18.0.2 --db-port 1521 --db-servicename Book --feature-sdw true --password-stdin`
    
**sunod ay** 

`docker run --rm -it -d --name ords_node1 --network=apex-network -p 8080:8080 -p 8443:8443 -v ords_config:/etc/ords/config -v $(pwd)/apex/images/:/etc/ords/config/global/doc_root/i/24.1.0/images container-registry.oracle.com/database/ords:latest`

**i-access ang docker bash** 

**magtungo sa /etc/ords/config/** 

**gumawa ng bagong "folder" at pangalan "ssl"**

kopyahin at may mga ilang dapat sagutan

`openssl genpkey -algorithm RSA -out private.key -pkeyopt rsa_keygen_bits:2048`

`openssl req -new -key private.key -out csr.csr`.

`openssl x509 -req -in csr.csr -signkey private.key -out certificate.crt -days 365`

kopyahin mga nga sumusunod

`ords config set standalone.doc.root /etc/ords/config/global/doc_root/`

`ords config set standalone.static.path /opt/oracle/apex/images`

`ords config set standalone.http.port 8080`

`ords config set standalone.https.port 8443`

`ords config set standalone.https.cert /etc/ords/config/ssl/certificate.crt`

`ords config set standalone.https.cert.key /etc/ords/config/ssl/private.key`
