# APEX and ORDS Docker on Linux OS installation 
### Disclaimer
This repository's content is primarily written in tagalog/taglis to better suit my workflow and communication preferences. If you encounter any difficulties understanding the material, feel free to reach out or use translation tools. Thank you for your understanding!
# 

Panimula siguradihin ang docker ay nagagamit sa inyong mga "server" or "machine" sunod ay Kunin ang "database images" gamit ito `docker pull container-registry.oracle.com/database/free:latest` at kung meron laktwan ang prosesong ito

### Gumawa: Volume at Network 
docker network create apex-network
docker volume create ords_config
# 

### kuhanin 

curl -o apex.zip https://download.oracle.com/otn_software/apex/apex-latest.zip
unzip -q apex.zip
chmod -R 755 apex

# Login as sys
sqlplus sys/bookkeep123@localhost:1521 as sysdba

ALTER SESSION SET WORKAREA_SIZE_POLICY = AUTO;

CREATE TABLESPACE APEX
DATAFILE 'APEX.dbf'
SIZE 500M
AUTOEXTEND ON NEXT 100M
MAXSIZE UNLIMITED;

ALTER SESSION SET CONTAINER = Book ;
