<link rel="stylesheet" type="text/css" media="all" href="D:\Users\idona\Notes\css\avenue.css"/></link>

# Tool - rsync #

<!- MarkdownTOC autolink="true" autoanchor="false" ->

- [Introduzione](#introduzione)
- [Algoritmo](#algoritmo)
    - [Ricevente](#ricevente)
    - [Mittente](#mittente)
    - [Risultato](#risultato)
- [Installazione](#installazione)
    - [Red Hat Enterprise Linux \(RHEL\)](#red-hat-enterprise-linux-rhel)
- [Sintassi Rsync](#sintassi-rsync)
- [Comon Options](#comon-options)
    - [Example: rsync locale](#example-rsync-locale)

<!- /MarkdownTOC ->

## Introduzione ##

Rsync è uno strumento versatile e veoloce per copiare e sincronizzare file 
e directory in locale o da/verso sistemi remoti.

Rsync tramite il remote-update protocol è in grado di trasferire
solo i delta. 
La prima volta, copia tutto il contenuto di un file
o di una directory dalla sorgente alla destinazione, ma dalla volta successiva
copia soltanto i blocchi di byte modificati.

## Algoritmo ##

### Ricevente ###

- spezza i file in blocchi contigui di dimensione S
- per ciascuno dei blocchi calcola 2 checksum (MD4 e uno simile a adler-32)
- spedisce al mittente i checksum

### Mittente ###

- calcola il checksum per tutti i possibili blocchi di lunghezza S del file
- compara i suoi checksum ciclici con quelli inviati dal ricevente, 
per verificare se ci sono delle corrispondenze
- Se ci sono, viene calcolato e controllato anche il corrispondente hash MD4
- il mittente invia al ricevente solo le parti del file per cui non sono stati trovati
blocchi corrispondenti, insieme a istruzioni su come ricomporre i blocchi già
presenti e le parti nuove in modo da ottenere una copia esatta del file originale

### Risultato ###

- Se le due versioni del file hanno molte sezioni in comune,
com'è spesso il caso quando si tratta di versioni diverse dello stesso file,
rsync può effettuare la sincronizzazione trasmettendo molti pochi dati
rispetto alla dimensione dell'intero file

## Installazione ##

## Red Hat Enterprise Linux (RHEL) ##

    # yum install rsync

## Sintassi Rsync ##

    rsync [options] [source] [destination]

## Comon Options ##

- **-a** l'archive mode permette di copiare ricorsivamente mantenendo
    permessi, utenti, gruppi, ownership e timestamps
- **-r** permette di effettuare la ricorsione all'interno delle directories senza
         mantenere permessi, utenti, gruppi, ownership e timestamps
- **-v** verbose mode
- **-vv** aumenta la verbosità
- **-vvv** per il debuging
- **-z** effettua la compressione durante il trasferimento
- **-h** Human redable

**Example: rsync locale**

```bash
rsync -avzh test_rsync/ test_rsync_2/
sending incremental file list
    ./
    prova2.txt
    test_rsync_2/
    
    sent 224 bytes  received 42 bytes  532.00 bytes/sec
    total size is 26  speedup is 0.10
```

Da notare che quando lanciamo il comando di rsync nel caso in cui 
la directory non fosse presente sarebbe in automatico creata.

Inoltre la presenza dello **/** finale nel path della sorgente ci permette di definire se volgliamo copiare il contenuto
della directory o se vogliamo creare una gerarchia di directory.

Il seguente comando permette di copiare il contenuto della directory test_rsync/ 
nella directory **test_rsync_2**

    rsync -avzh test_rsync/ test_rsync_2/

Se omettiamo il simbolo di **/** quello che otteniamo verrà creata una directory
test_rsync all'interno di test_rsync_2

**Example: rsync remoto**

Consideriamo 2 server 

- srv1 IP: 192.168.40.250
- srv1 IP: 192.168.40.251

Colleghiamoci tramite ssh al server srv1 ed effettuamo un comando di rsync verso il 
srv2.

Per sincronizzare la directory remota con quella locale usare la seguente 
sintassi

    rsync [opzioni] [sorgente] [{user}@{IP_ADDRESS}:{DIRECTORY}]

**Esempio**

```bash
rsync -avzh /home/ismail/test_rsync root@192.168.40.251:/home/ismail/
```

Per sincronizzare la directory locale con quella remota usare la seguente 
sintassi

    rsync [opzioni] [{user}@{IP_ADDRESS}:{DIRECTORY}] [destinazione]

**Esempio**

```bash
rsync -avzh /home/ismail/test_rsync root@192.168.40.251:/home/ismail/
```

## Rsync con ssh ##

Possiamo utilizzare il protocollo ssh per eseguire il data transfer. Usando
il protocollo ssh i nostri dati vengono inviati in maniera sicura, in quanto
viene applicato l'ecryption dei dati.

Per poter effettuare il rsync è necessario fornire username e password abilitando
la connessione tramite ssh è possibile inviare queste informazioni in modo
sicuro

Per poter abilitare ssh è necessario utilizare l'opzione -e

- **-e** abilita ssh per lo scambio dati

```bash
rsync -ahz -e ssh /home/ismail ismail@192.168.40.251:/home/ismail/ 
```

Se definiamo delle chiavi ssh per l'accesso possiamo utilizzarle per la connessione
tramite rsync senza inserire una password

```bash
rsync -avhz -e "ssh -i /home/ismail/.ssh/vmdb01" test.txt ismail@vmdb02:/home/ismail/
```

Se configuriamo il file /home/ismail/.ssh/config possiamo omettere nel comando la chiave ssh. Di seguito
un esempio di configurazione del file config.

```bash
Host vmdb02
        User ismail
        IdentityFIle /home/ismail/.ssh/vmdb02
```

Il file config deve essere in sola lettura da parte dell'utente 

    -rw------- /home/ismail/.ssh/config

## Progress ##

Per mostrare i progressi quando trasferiamo tramite rsync utilizzare il seguente
comando

- **--progress** mostra la percentuale di completamento necessaria per il trasferimento

## Uso delle opzioni –-include e –-exclude ##

Per escludere ed includere file nel comando di rsync utilizzare le due opzioni

- **--exclude** permette di escludere files specifici.
- **--include** permette di includere specifici files

**Esempio**

    rsync -avvhz -e ssh --exclude '.*' /home/ismail/ ismail@vmdb02:/home/ismail/
    opening connection using: ssh -l ismail vmdb02 rsync --server -vvlogDtprze.iLsfxC . /home/ismail/  (9 args)
    
    sending incremental file list
    [sender] hiding file .bash_logout because of pattern .*
    [sender] hiding file .bash_profile because of pattern .*
    [sender] hiding file .bashrc because of pattern .*
    [sender] hiding file .kshrc because of pattern .*
    [sender] hiding directory .cache because of pattern .*
    [sender] hiding directory .config because of pattern .*
    [sender] hiding file .bash_history because of pattern .*
    [sender] hiding directory .ssh because of pattern .*
    [sender] hiding file .viminfo because of pattern .*
    delta-transmission enabled
    test.txt is uptodate
    ./
    script/
    total: matches=0  hash_hits=0  false_alarms=0 data=0
    
    sent 135 bytes  received 82 bytes  434.00 bytes/sec
    total size is 6  speedup is 0.03

Nell'esempio sopra abbiamo escluso la presenza tutti i files 
che avessero con il pattern **'.*'**

Per effettuare esclusioni multiple basta utilizzare invocare più volte il comando
**--exclude**

## Opzione --delete ##

Con l'opzione **--delete** eliminiamo alla destinazione tutti i files che non sono 
nella directory delle source. Come si può notare dall'esempio sottostante se i files 
che devono essere eliminati per via della clausola delete sono comunque protetti dall
pattern definito nell'exclude

    rsync -avvhz -e ssh --delete --exclude '.*' /home/ismail/ ismail@vmdb02:/home/ismail/
    opening connection using: ssh -l ismail vmdb02 rsync --server -vvlogDtprze.iLsfxC --delete . /home/ismail/  (10 args)
    sending incremental file list
    [sender] hiding file .bash_logout because of pattern .*
    [sender] hiding file .bash_profile because of pattern .*
    [sender] hiding file .bashrc because of pattern .*
    [sender] hiding file .kshrc because of pattern .*
    [sender] hiding directory .cache because of pattern .*
    [sender] hiding directory .config because of pattern .*
    [sender] hiding file .bash_history because of pattern .*
    [sender] hiding directory .ssh because of pattern .*
    [sender] hiding file .viminfo because of pattern .*
    delta-transmission enabled
    [generator] protecting file .bash_logout because of pattern .*
    [generator] protecting file .bash_profile because of pattern .*
    [generator] protecting file .bashrc because of pattern .*
    [generator] protecting file .kshrc because of pattern .*
    [generator] protecting directory .cache because of pattern .*
    [generator] protecting directory .config because of pattern .*
    [generator] protecting file .bash_history because of pattern .*
    [generator] protecting directory .ssh because of pattern .*
    [generator] protecting file .viminfo because of pattern .*
    deleting test2.txt
    test.txt is uptodate
    ./
    total: matches=0  hash_hits=0  false_alarms=0 data=0
    
    sent 147 bytes  received 681 bytes  552.00 bytes/sec
    total size is 6  speedup is 0.01

## Testare l'esecuzione con dry run ##

Per testare rsync prima di eseguirlo si può usare il dry run ci mostrerà
l'output senza però eseguire alcuna copia

    rsync --dry-run -avvhz -e ssh --delete --exclude '.*' /home/ismail/ ismail@vmdb02:/home/ismail/   opening connection using: ssh -l ismail vmdb02 rsync --server -vvnlogDtprze.iLsfxC --delete . /home/ismail/  (10 args)
    sending incremental file list
    [sender] hiding file .bash_logout because of pattern .*
    [sender] hiding file .bash_profile because of pattern .*
    [sender] hiding file .bashrc because of pattern .*
    [sender] hiding file .kshrc because of pattern .*
    [sender] hiding directory .cache because of pattern .*
    [sender] hiding directory .config because of pattern .*
    [sender] hiding file .bash_history because of pattern .*
    [sender] hiding directory .ssh because of pattern .*
    [sender] hiding file .viminfo because of pattern .*
    delta-transmission enabled
    [generator] protecting file .bash_logout because of pattern .*
    [generator] protecting file .bash_profile because of pattern .*
    [generator] protecting file .bashrc because of pattern .*
    [generator] protecting file .kshrc because of pattern .*
    [generator] protecting directory .cache because of pattern .*
    [generator] protecting directory .config because of pattern .*
    [generator] protecting file .bash_history because of pattern .*
    [generator] protecting directory .ssh because of pattern .*
    [generator] protecting file .viminfo because of pattern .*
    deleting albero
    test.txt is uptodate
    total: matches=0  hash_hits=0  false_alarms=0 data=0
    
    sent 147 bytes  received 678 bytes  1.65K bytes/sec
    total size is 6  speedup is 0.01 (DRY RUN)





