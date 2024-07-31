# Active-Directory-with-Azure
Questo Homelab consiste nel replicare Active Directory composto da due terminali, un Windows Server2019 e un host Windows10. <br>
Non avendo hardware sufficiente per supportare entrambe le macchine virtuali ho sfruttato i crediti gratuiti che si ricevono con l'iscrizione a Microsoft Azure<br>
per creare le macchine virtuali on cloud.<br><br>

<h2>Un paio di nozioni sull'Active Directory</h2><br>
Active Directory (AD) è un servizio di directory sviluppato da Microsoft per i sistemi operativi Windows. Serve principalmente per la gestione centralizzata delle risorse di una rete aziendale.<br><br>

<h3>Cosa è Active Directory?</h3><br>
Servizio di Directory: È un database che memorizza informazioni su utenti, computer e altre risorse della rete.<br>
Centralizzato: Consente di gestire tutte queste informazioni da un unico punto.<br><br>


<h3>A cosa serve Active Directory?</h3><br>
Gestione Utenti e Computer: Permette di creare, modificare e cancellare account utente e computer.<br>
Autenticazione e Autorizzazione: Verifica l'identità degli utenti e determina quali risorse possono utilizzare.<br>
Gruppi e Politiche di Sicurezza: Facilita la gestione delle autorizzazioni attraverso gruppi di utenti e politiche di sicurezza.<br>
Amministrazione Centralizzata: Consente agli amministratori di rete di gestire e controllare l'intera rete da un unico punto.<br><br>


<h3>Esempi di Utilizzo</h3><br>
Accesso ai File: Gli utenti possono accedere ai file condivisi solo se autorizzati.<br>
Login Unico (Single Sign-On): Gli utenti accedono a diverse applicazioni e servizi con un solo login.<br>
Gestione delle Risorse: Amministratori possono distribuire software e aggiornamenti a tutti i computer della rete in modo centralizzato.<br><br>


In sintesi, Active Directory è uno strumento per gestire utenti e risorse in una rete, migliorando la sicurezza e facilitando l'amministrazione.<br><br>


<h3>Cosa è una Forest? </h3><br>
Una "Forest" (foresta) è la struttura di più alto livello in Active Directory (AD). È un insieme di uno o più "Domain" (domini) che condividono una configurazione comune e una struttura di sicurezza. Tutti i <br>domini all'interno di una forest sono collegati in modo gerarchico e possono condividere informazioni tra loro. Pensala come una grande azienda con diverse filiali (i domini) sparse in tutto il mondo, ma tutte <br>fanno parte della stessa azienda (la foresta).<br><br>


<h3>Cosa fa un Domain Controller?</h3><br>
Un "Domain Controller" (DC) è un server che gestisce la sicurezza e le risorse di rete per un dominio specifico. Esegue i servizi di Active Directory e autentica gli utenti quando accedono alla rete. In pratica,<br> è come una guardia di sicurezza che controlla chi può entrare in un edificio (il dominio) e cosa può fare una volta dentro. Assicura che solo gli utenti autorizzati possano accedere alle risorse di rete e <br>gestisce le politiche di sicurezza.<br><br>

<h1>PARTE 1: CREAZIONE E CONFIGURAZIONE DELLE MACCHINE VIRTUALI</h1><br><br>

Su Azure cliccate Virtual Machines -> Create new Azure Virtual Machine.Eseguiremo questo processo due volte.<br>
Resource Group: Create new -> "Homelab"<br>
Su Azure, creo due VM:<br><br>

- Windows Server 2019, West US 2, Zone 3, <br>
username = homelab-server, password = Homelab2024!<br>
Nella sezione Networking creaiamo una virtual network con nome Homelab. Lasciamo tutto il resto di default e clicchiamo ok.<br><br>

- Windows 10 Pro, version 22H2 - x64 Gen2, West US 2, Zone 3<br>
username = homelab-windows10, password= Homelab2024!<br>
Nella sezione Networking selezioniamo come virtual network Homelab nel caso non sia già quella di default.<br><br>

NOTA: E' essenziale che le due VM si trovino nella stessa regione, zona e condividano la stessa virtual network altrimenti<br>
non potranno comunicare tra loro.<br><br>

Aprendo la sezione Virtual Machines -> Server2019 -> Connect -> Select RDP -> Donwload RDP File<br><br>

Questo file ci serve per accedere alla VM tramite i servizi di accesso remoto.<br>
Se la connessione RDP non vi funzionasse perché vi dice che le credenziali sono errate<br>
verificate se ci sono "\" oppure "-" dentro la password o se contiene proprio l'username.<br>
Altrimenti resettate la password in modo tale che non contenga l'username al suo interno,<br>
anche se scritto con lettere maiuscole o minuscole o numeri.<br><br>

Una volta che il Server si è aperto io ho resettato ora e giorno per preferenza personale.<br>
Infine in automatico si apre la pagina di configurazione del Server, la dashboard del Server Manager da dove più tardi faremo un pò di modifiche.<br><br>

Rinominiamo il PC per riconoscerlo più facilmente tra le due VM. Da File Explorer -> Click destro su This PC -> Properties -> Change Settings -> Change<br><br>

Io lo ho chiamato Server01. (chiede di riavviare la VM per applicare le modifiche, fatelo)<br><br>

TIP: quando rinominate una macchina datele un nome significativo. Io qui ho scritto Server01 ma sarebbe molto più utile se la chiamassi per esempio Server_Windows_2019_Ita, così chi deve risolvere dei problemi <br>di rete sa esattamente che tipo di server sia, quale OS ci giri sopra e in che nazione/città si trova.<br><br>

TIP: per evitare che consumino i crediti selezionate entrambe le VM in Azure e cliccate su STOP. Sotto la voce status dovrebbe cambiare da Running a Stopped(Deallocated). Così evitare che vi consumino crediti <br>utili e le potrete riavviare in un secondo momento per continuare questo laboratorio.<br><br>

<h1>PARTE 2: CREAZIONE E IMPOSTAZIONE ACTIVE DIRECTORY</h1><br><br>

Comunque, nel caso abbiate stoppato le VM, riavviate la VM Server2019 e accedete tramite RDP.<br>
Nel Server Manager cliccate su Manage in alto a destra e poi su Add roles and features.<br>
Da qui cliccate sempre su Next fin quando non arrivate alla parte Select server roles.<br>
Qui dovrete spuntare Active directory Domain Services -> Add features.<br>
Solo questa da spuntare, cliccate di nuovo Next fino a cliccare Install.<br>
Praticamente ora stiamo installando il Domain Service e Domain Controller, ossia stiamo preparando il necessario per Active Directory...<br><br>

Proseguendo, una volta che ha finito l'installazione non cliccate su Close ma cliccate su "Promote this Server to a Domain Controller". (Nel caso abbiate cliccate Close come me, in alto a destra dovrebbe <br>comparirvi una bandierina gialla. Se la cliccate troverete in blu "Promote this Server to a Domain Controller".)<br><br>

Siccome non abbiamo alcun dominio preesistente, ne creiamo uno facendo "Add new forest".<br><br>

Root domain name che io ho scelto:  ad.lab <br><br>

Potete chiamarlo come vi pare, l'importante è che non scegliato un nome già esistente come google.com o microsoft o youtube, deve essere unico.<br>
Una volta cliccato Next selezionate Windows Server 2016 per entrambi i functional level e inventate una password che servirà a recuperare l'account di domain controller nel caso succeda qualcosa e non riusciate <br>più ad accedere.<br><br>

Password Domain controller: domcon2024!<br><br>

Cliccate sempre Next fino ad arrivare alla sezione "Additional Options" e aspettate che trovi da solo in automatico il nome del dominio NetBIOS. Di nuovo Next fin quando non arrivate a Review Options.<br><br>

TIP: Qui se cliccate su View Script vi mostrerà lo script powershell da seguire con Powershell Administrator.<br>
Questo script vi permetterà di creare l'Active Directory da linea di comando replicando esattamente tutte le cose che abbiamo scritto finora. Scelta vostra su come proseguire, io continuo con l'interfaccia <br>grafica.<br><br>

Cliccate di nuovo Next, vi darà una serie di Warning (triangoli gialli) ma i prerequisiti ci sono tutti. Cliccate Install.<br>
Una volta finita l'installazione vi dirà che sarete disconnessi e la VM si spegnerà.<br>
Aspettate un pò e poi proviamo a riaccedere tramite RDP. Se vi dice password sbagliata aspettate ancora perché vuol dire che sta ancora installando l'active directory. Se dopo 30 min ancora non funziona, <br>resettate la password.<br>
Una volta avviata la VM inizierà un caricamento lunghissimo che dice "please wait for the group policy client".<br>
Abbiate pazienza per favore. Questa è la parte più lunga.<br><br>

Una volta partita, nella barra di ricerca di Windows cercate "Active Directory Adminstrative Center".<br>
A sinistra cliccate sul nome del vostro Root Domain Name, nel mio caso sarà activedirectory (local).<br>
A destra cliccate "Enable Recycle Bin" e cliccate ok due volte.<br>
Questo ci serve perché così, se cancelliamo un utente per sbaglio, lo possiamo recuperare dal Recycle Bin e ripristinarlo.<br>
Aggiornate "Active Directory Adminstrative Center" con la due frecce circolari in alto Ayano2024!fin quando la scritta "Enable Recycle Bin" non è più cliccabile (dovrebbe diventare grigio opaco). Chiudetelo pure<br> ora.<br><br>

Nel server manager invece cliccate in alto a destra su Tools -> Active directory Users and Computers.<br>
Cliccate su View -> Advanced Features.<br>
Sulla sinistra vedremo il nostro root domain name. Cliccatelo e cliccate su Users dove vedrete tutti gli utenti.<br><br>

NOTE: Oltre a tutti gli utenti presenti c'è un utente chiamato krbtgt. Questo è l'utente presente di default che si occupa di Kerberos (il servizio di autenticazione account e ticket) ed è la vulnerabilità <br>sfruttata negli attacchi server ticket e golden ticket. Sono attacchi mirati al domain controller per spacciarsi per un amministratore e controllare gli utenti monitorati dal domain controller. Per questo<br> motivo si consiglia di resettarne la password ogni 6 mesi.<br><br>

Aprite powershell come amministratore e scrivete:<br>
import-module activedirectory<br>
get-command new-aduser -syntax<br><br>

Questi due comandi ci permettono di creare nuovi utenti active directory.<br><br>

Qui per esempio creo due nuovi utenti activedirectory chiamati Ayanokoujii e Kyotaka (ovviamente sempre su Powershell amministratore):<br><br>
new-aduser Ayanokouji<br>
new-aduser Kyotaka<br><br>

e potremo verificare che sono comparsi nella cartella Users che stavamo guardando poco fa (se non li vedete provate a cliccare Refresh in alto, la freccia circolare).<br><br>

Per vedere che utente sono su powershell:<br><br>

whoami<br><br>

che nel mio caso restituisce activedirectory/helpdesk.<br><br>

Per vedere da che domain controller vengo gestito:<br><br>

whoami /fqdn           (fqdn = full qualified domain controller)<br><br>

che mi restituisce CN=helpdesk,CN=Users,DC=activedirectory,DC=lab (visto che era activedirectory.lab).<br><br><br>


Ora per i due account creati andiamo a resettarne la password.<br>
Cercateli nella cartella User e fate click destro -> Reset Password. Togliete la spunta "User must change password at next logon".<br><br>

Password Ayanokouji: Ayano2024!<br>
Password Kyotaka: Kyota2024!<br><br>

Fate di nuovo click destro su entrambi e fate Enable Account.<br><br>



<h1>PARTE 3: AGGIUNGIAMO LA VM WINDOWS10 AL NOSTRO DOMAIN</h1><br><br>

Se proviamo a fare su Windows10 tramite cmd: "ping ad.lab" non lo trova.<br>
Cerchiamo e clicchiamo Control Panel nella barra di ricerca windows in Desktop01.<br><br>

Control Panel -> View Network status and tasks -> Change Adapter Settings -> Ethernet -> Properties -> Internet Protocol Version 4 (TCP/IP).<br>
Aprite il cmd di Windows10 e scrivete: ipconfig.<br>
Selezionate "Use the following IP address:"<br>
- IP address: qui mettete l'ip della vostra VM Windows10, copiatelo dal risultato di ipconfig<br>
- Subnet mask:  qui mettete la subnet mask della vostra VM Windows10, copiatelo dal risultato di ipconfig<br>
- Defalut gateway:  qui mettete il default gateway della vostra VM Windows10, copiatelo dal risultato di ipconfig.<br><br>

Aprite il cmd di Server2019 e scrivete: ipconfig.<br>
Selezionate "Use the following DNS server addresses":<br>
- Preferred DNS server: qui mettete l'ip del vostro Server2019, copiatelo dal risultato di ipconfig<br>
- Alternate DNS server: qui scrivete 8.8.8.8 (che è quello di Google.com, affidabile visto che ci spendono miliardi sopra per mantenerlo sempre attivo).<br><br><br>


Infine cliccate ok -> ok<br>
Si impallerà sicuro la Vm e vi farà uscire automaticamente. Tutto normale. Chiudetela e aspettate 5 min, poi provate a riavviarla tramite RDP.<br>
Una volta riavviata chiudete le impostazioni di Windows10.<br>
Quello che stiamo provando a fare è che stiamo mettendo la nostra VM Windows10 sotto il dominio del nostro domain controller activedirectory.lab.<br><br>

Dal cmd di windows10 ora se facciamo "ping ad.lab" funzionerà.<br>
(Se non vi funziona tornate all'inizio e verificate che la vnet, la regione e la zona siano le stesse per entrambe le VM altrimenti non riusciranno a comunicare tra loro e rende inutile tutto questo laboratorio).<br><br>

Ora, in Windows10, File Explorer -> This PC -> Properties -> Rename this PC (advanced) -> Change e selezionate domain e scrivete il nome del vostro root domain (nel mio caso ad.lab) e come computer name ho messo<br> Desktop01 (anche qui in un contesto aziendale è più utile mettere un nome più significativo).<br>
Cliccate ok e inserite username e password della vostra VM Server2019.<br>
Ora dovrebbe iniziare un caricamente medio-lungo. Se vi da un messaggio d'errore relativo al server RPC, ricliccate ok e reinserite le credenziali di Server2019.<br>
Una volta finito vi apparirà un messaggio che dirà "Welcome to the ad.lab domain" (ovviamente al posto di ad.lab ci sarà il vostro root domain name).<br>
Vi chiederà di riavviare e si riavvierà.<br>

Dalla VM Server2019 possiamo verificare che la nostra VM Windows10 è stata aggiunto al nostro dominio aprendo Server Manager -> Tools -> Active directory for users and computers, cliccate sul vostro root domain <br>-> Computers e troverete il vostro Desktop01.<br><br>

<h2>CREAZIONE LOCAL ADMIN PER WM WINDOWS10</h2><br><br>
Ora se proviamo ad accedere tramite RDP noteremo che l'utente con cui accediamo è il local user di questa VM nel domain a cui appartiene.<br><br>

Ora sul browser di Windows10 andate a questo link:<br><br>

https://www.microsoft.com/en-us/download/details.aspx?id=45520<br><br>

per scaricare gli RSAT (Remote Server Administration Tools).<br>
Cliccate su Download e cliccate la versione x64.<br>
Terminato il Download cliccate due volte per installarli e una volta terminata l'installazione riavviate la VM Windows10 per stare tranquilli.<br><br>

Se vogliamo invece accedere come amministratore di questa macchina, quando facciamo l'accesso con RDP dobbiamo mettere come username:<br>
ad.lab\homelab-server<br>
e usiamo la password di Server2019.<br><br>

Qui se clicchiamo su Start dovremmo trovare gli strumenti da Active Directory sotto la voce "Windows Administrative Tools" e potremo usarli liberamente proprio perché siamo gli admin locali della VM Windows10 <br>(come tipo creare da qui un altro utente per l'active directory come abbiamo fatto per Ayanokouji e Kyotaka).<br><br>

L'account in cui troviamo ora è un admin che viene creato di default quando accediamo tramite root domain.<br><br>

Siccome non vogliamo che la gente lo usi, creiamo giusto per sicurezza, un altro account administrator locale.<br><br>

Nella barra di ricerca in basso di Windows10 cercate Computer Management -> Local User and Groups -> User.<br>
Qui troviamo l'account in cui ci troviamo attualmente, nel mio caso homelab-windows10 e se vedete c'è scritto nella descrizione relativa che è "built-in", ossia viene creato di default. Per questo motivo non <br>possiamo cancellarlo.<br><br>

Click destro -> New User<br><br>

Username: Administrator<br>
Password Administrator: Homelab2024!<br><br>

Togliete la spunta "User must change password at next logon" e selezionate le spunte "User cannot change password" e "Password never expires". Cliccate Create.<br><br>


Cliccate due volte su Administrator -> Member of -> Add -> Advanced -> Find now e dalla lista che vi compare selezionate il gruppo Administrators.<br>
Cliccate Ok, poi Apply e poi di nuovo Ok.<br><br>

Infine sull'account in cui ci troviamo per motivazioni di sicurezza, cliccate due volte il vostro homelab-windows10, spuntate l'opzione "account is disabled" e togliete la spunta "password never expires". Infine<br> Apply.<br>
Questo è per evitare che qualche utente possa arrivare ad ottenere permessi da admin.<br><br>

<h2>ACCESSO CON GLI UTENTI CREATI PRIMA TRAMITE RDP</h2><br><br>

Ora se vogliamo loggare tramite RDP con gli account di Kyotaka e Ajanokoujii dobbiamo prima inserirli nel gruppo dei Remote Desktop Users<br><br>


Nella barra di ricerca in basso di Windows10 cercate Computer Management -> Local User and Groups -> Groups -> Remote Desktop Users -> Add -> Scrivete nell'area di testo il nome dei vostri utenti e poi check <br>names. Se li trova dovrebbe resistuirvi il nome utente sottolineato. <br>
Cliccate ok, (io l'ho fatto per entrambi) e infine Apply.<br><br>



<h2>PICCOLA VISTA SULLE GROUP POLICIES</h2><br><br>

Per visualizzare le policy in atto del domain, sulla VM Server2019:<br>
Server Manager -> Tools -> Group Policy Management.<br>
Qui possiamo cambiare le impostazioni di tutto il domain o anche solo per gruppi.<br><br>

Io qui per esempio andando ad.lab -> Default Domain Policy -> Click destro -> Edit -> Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Account policies -> Account Lockout Policy -> <br>Account lockout threshold -> Doppio click.<br>
Io ho impostato che possono essere fatti al massimo 4 tentativi per loggare.<br>
Una volta confermato vi dirà che altre regole verranno modificate in base alla modifica appena fatta. Cliccate ok.<br>
Di norma è meglio avere policy simili se non uguali tra le varie Domain Policies.<br><br>

Inoltre nel CMD se facciamo "gpresult /h GPRreport.html", verrà generato un documento .html che i strova dentro "This PC -> Windows(:C) -> Users -> homelab-server.<br><br>

Questo documento contiene tutte le impostazioni relative alle policy applicate.<br><br>

Per vedere le policy da riga da cmd: <br>
- gpresult /r<br><br>

Per altre info:<br>
net user nome_utente /domain<br><br>

<h2>CREARE CARTELLE CONDIVISE DA SERVER2019</h2><br><br>

Poi Server Manager -> File and Storage Services -> Shares -> Click destro -> New Share (deve essere selezionata SMB Share - Quick) -> Next -> Next.<br><br>

Share name: ad.lab<br><br>

Next fino alla fine e poi Create e infine close.<br><br>

Questa cartella si troverà in: This PC -> Windows(:C) -> Shares<br><br>

Creiamo la cartella Home dentro la cartella condivisa ad.lab per tutti gli utenti che accederanno al domain.<br><br>

Click destro -> New Folder e la chiamiamo Home.<br><br>

Click destro su Home -> Properties -> Sezione Sharing -> Advanced Sharing -> Spuntate "Share this Folder" -> apply -> ok<br><br>

Poi la sezione Security -> Advanced -> Disable Inheritance -> Selezionate "Convert inherited permissions..."<br>
Poi selezionate dalla lista di account "Users(AD\Users)" e cliccate Remove.<br>
Poi cliccate Add -> Select a principal e come prima cercate il vostro Ajanokouji e Kyotaka. Spuntate Modify e poi Ok. Infine Apply e poi Ok.<br><br>

Torniamo nella sezione Sharing -> Share e impostiamo per Ayanokouji e Kyotaka sotto la colonna "Permission level" Read/Write.<br><br>

Copiamo il network path (nel mio caso è \\SERVER01\Home)<br>
perché adesso andiamo in Active Directory Users and Computers dove selezioniamo il nostro Ayanokouji e in Profile selezioniamo Connect, scegliamo la lettera H: e incolliamo \\SERVER01\Home<br>
Resitutuirà un warning dicendo che stiamo impostando questa cartella come la cartella Home dell'utente Ayanokouji e che dobbiamo assicurarci abbia i permessi per accedervi e scrivere/leggere. Lo abbiamo fatto <br>prima quindi cliccate Ok -> Apply.<br><br>

Io ho fatto la stessa cosa anche per Kyotaka ma non è necessario, basta un utente.<br>
Tenendo aperta la VM Server2019 proviamo ad accedere con Ayanokouji tramite RDP alla VM Windows10 con:<br><br>

username: ad.lab\Ayanokouji<br>
password: la password scelta per Ayanokouji, nel mio caso Ayano2024!<br><br><br>


Se andiamo su File Explorer -> This PC troveremo la nostra Home sotto la voce Network locations chiamata ad.lab (\\SERVER01)(H:)<br>
Se proviamo ad aprirlo, andare nella cartella Home e creare un file .txt potremo vederlo comparire anche dentro la cartella Home creata nella VM Server2019.<br><br>

Io avendo fatto lo stesso procedimento anche per Kyotaka adesso avrò una cartella condivisa tra il Server2019 e i due account.<br><br><br>


NOTA: Nel caso doveste ricevere un messaggio d'errore quando provate ad aprire la VM Windows 10 che dice "The remote computer...NLA" o altri problemi di accesso vi consiglio di cercare su youtube "IT: Creating Homelab Using Azure Part4 Kevtech IT Support" per risolvere.<br>
