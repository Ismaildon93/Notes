<link rel="stylesheet" type="text/css" media="all" href="D:\Users\idona\Notes\css\avenue.css"/></link>

# SQL*Plus #

<!-- MarkdownTOC autolink="true" autoanchor="false" -->

- [Indice](#indice)
- [SQL*Plus](#sqlplus)
- [Connettersi al database tramite SQL*plus](#connettersi-al-database-tramite-sqlplus)
- [Settare le variabili d'ambiente](#settare-le-variabili-dambiente)

<!-- /MarkdownTOC -->


## Indice ##

<br>
Ci sono 3 modi principali per comunicare con un database oracle sottomettendo statements 
SQL e comandi

- Tramite interfaccia a linea di comando **SQL*Plus**
- Tramite GUI __OEM__(Oralce Enterprise Manager)
- Tramite __SQLdeveloper__


## SQL*Plus ##

<br>
Tramite SQL*Plus possiamo effettuare le seguenti operazioni all'interno di un database
Oracle

- start/shut down del db
- settare i parametri di inizializzazione del db
- creare e amministrare gli utenti di database
- creare e modificare database object (come tabelle e indici)
- Operazioni CRUD sui dati
- eseguire query SQL

Prima di effettuare le operazioni bisogna connettersi al database Oracle e lo si può fare in
due modi

- **Localmente** ovvero dalla stessa macchina in cui risiede il db
- **Remoto** tramite network ad un database Oracle che si trova su un host remoto

## Connettersi al database tramite SQL*plus

Il database Oracle è composto dai seguenti componenti:

- **The Oracle Database Instance** un insieme di processi e aree di memoria
- **Files sul disco** che contiene i dati degli utenti e di sistema  

Ogni Database possiende un instance ID chiamato **SID**. All'interno di un Host
possono risiedere multiple istanze ognuna con il suo set di data files, è quindi
necessario poter identificare a quale instanza connettersi.

- **Local connection** set delle variabili d'ambiente identifica l'istanza
- **Remote connection** network address e un database service name

Sia per le connessioni remote che locali è comunque necessario indicare il percorso
dell'eseguibile **SQL*Plus**

## Settare le variabili d'ambiente ##












