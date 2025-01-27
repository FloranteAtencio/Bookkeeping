# Disclaimer
This repository's content is primarily written in tagalog/taglish to better suit my workflow and communication preferences. If you encounter any difficulties understanding the material, feel free to reach out or use translation tools. Thank you for your understanding!


# Bookkeep Database Initial set up for docker and oracle 23c free database version of oracle

--- Introduction ----------

Oracle 23c Structure

Oracle Database 23c:

Instance: The instance is the set of memory structures and background processes that manage the database files.

Container Database (CDB):

CDB: The CDB includes the root container (CDB$ROOT) and one or more Pluggable Databases (PDBs).

Pluggable Database (PDB):

PDB: Acts as an independent database within the CDB. Each PDB has its own users, schemas, and objects. PDBs share the instance’s memory and processes but are otherwise isolated from one another.

Schema:

Schema: A logical grouping of database objects (tables, views, indexes, etc.) under a single user within a PDB. Each user owns their own schema and objects. So, within your Oracle 23c setup:

One instance manages one CDB.

The CDB contains multiple PDBs.

Each PDB is a self-contained database with its own schemas and objects.

Ubuntu Server 24.04.1 LTS for Database and docker for container.

# Coder start here in Linux 
# Update server

sudo apt update


#  Install docker

sudo apt install docker.io


# Create Volume for progress 

docker volume create oracle_volume


 #  Download oracle inside docker this about worth 10 Gb of

sudo docker run -itd --name Bookkeepeer -p 1521:1521 -e ORACLE_PWD='bookkeep123' -v oracle_volume:/opt/oracle/oradata container-registry.oracle.com/database/free:latest

 #  Execute sql

sudo docker exec -it Bookkeepeer bash


#  Login as admin 

sqlplus sys/bookkeep123@localhost:1521 as sysdba


PLUGGABLE DATABASE CREATE PLUGGABLE DATABASE Dev_Cafe admin user keeper IDENTIFIED BY Book create_file_dest='/home/oracle/plugbookkeep';

ALTER PLUGGABLE DATABASE Bookkeepeer OPEN; 

ALTER PLUGGABLE DATABASE Bookkeepeer SAVE STATE; 

EXIT


 #  Log in to the database

sqlplus sys@bookkeep123localhost:1521/Bookkeepeer as sysdba


 # Grant access to Link 

GRANT DBA to Keeper


 #  Production Access after the schema is created

CREATE ROLE prod_ROL;

BEGIN FOR t IN (SELECT table_name FROM all_tables WHERE owner = 'Dev_cafe') LOOP EXECUTE IMMEDIATE 'GRANT SELECT, INSERT, UPDATE, DELETE ON ' || 'Dev_cafe' || t.table_name || ' TO prod_ROLE'; END LOOP; END; /

CREATE USER Product IDENTIFIED BY Prod123!

GRANT prod_ROLE TO Product

EXIT


