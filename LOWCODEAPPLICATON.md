# APEX and ORDS Docker installation 
  
# Create network

docker network create apex-network

#volume 

docker volume create ords_config

# download and extract apex instance

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
