# Labbmiljö, Git, CLI och AI
Namn Efternamn  
Datum xxxx-xx-xx  
Kursnamn xxx  
Beskrivning av labbmiljö  

## Del 1 - Skapa och initiera projektet med Git
### Github
Skapande av ny repository på github.com (lehtela/labb001), anges som Privat.  
Skapande av **C:\GIT\Labb001** och en **testfile.txt** som ska kunna verifiera att att repository fungerar online.  
Terminal öppnas i **C:\GIT\Labb001**.  
![Bild på terminal för init repo](./000_setupgitrepo.png)
>"git init"  
>"git remote add origin git@github.com:lehtela/Labb001.git"  
>"git add ."  
>"git commit -m "First comment on update testfile"  
>"git push origin main"  


Git initierar och pushar filer till Github, filer veriferas genom webläsare på sidan https://github.com/lehtela/labb001  

### VS Code
I VS Code, öppna **C:\GIT\Labb001** och skapa **Labbdokumentation.md**. Filen uppdateras med rubrikinnehåll och rubiker/text. 
Ny commit för att spara arbete online:
>"git add ."  
>"git commit -m "Rubriker, spaltindelningar och Del 1"  
>"git push origin main"  











## Del 2 - Virtuell Labbmiljö och Nätverk
### VirtualBox
**Konfiguration av två virtuella maskiner i Virtualbox.**  
Det finns två val av konfigurationer mellan de virtuella enheterna, både val isolerar internetåtkomst från de virtuella enheterna.

→ **Internal Network**  
Isolerat nätverk där virtuella enheter kan kommunicera med varandra.  
→ **Host-Only**  
Isolerat nätverk där virtuella enheter kan kommunicera med varandra samt dator som motsvarar host/värd. 
- - - 
![VirtualBox Host-only inställningar](./2_001_vboxadapter.png)
I VirtualBox, **File → Tools → Network** (Ctrl+H) för att skapa adapter.

Skapa ny **Host-only adapter** **"VirtualBox Host-Only Ethernet Adapter #2"**  
Välj **Adapter → Configure Adapter Manually**:  
IPv4 Adress: **192.168.1.1**  
IPv4 Network Mask: **255.255.255.0**
- - - 
![Virtuella maskinernas inställningar](./2_002_vmsadapter.png)
Nästa steg, **välj** adapter för **alla virtuella** enheter:  

Virtuella enhetens inställningar→**Settings→Network→Adapter 1**.  
**Enable Network Adapter 1**  
Attached to: **Host-only Adapter**  
Name: **VirtualBox Host-Only Ethernet Adapter #2**.  
- - -




### Linux Ubuntu Desktop med Host-Only
Starta Ubuntu Desktop VM, hitta **Settings→Network→Wired→IPv4**
![Ubuntu Desktop inställningsvy för statisk IP](./2_004_ubuntuip.png)
Enable IPv4 Method: **Manual**  
Address: **192.168.1.50**  
Netmask: **255.255.255.0**  
→**Apply**


### När Ubuntu Desktop's IP-inställningar inte fungerar att spara:
Addressera problem i **Terminal** för att ändra nätverksinställningarna manuellt.  
`sudo nano /etc/netplan/00-installer-config.yaml`

Ange värden för fil:
```network:
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 192.168.1.50/24
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
      match:
        macaddress: 08:00:27:4c:f4:d4
      set-name: enp0s3
  version: 2
  ```

Spara fil och applicera inställning:  
`sudo netplan apply`

 - - - 






### Windows 11 med Host-Only
Starta Windows 11 VM, hitta till **Control Panel→Network and Internet→Network Connections** (eller sök efter Network).
![Windows 11 inställningsvy för statisk IP](./2_003_winip.png)
Öppna **"Ethernet Intel(R) PRO/100 MT"→Properties→Networking→Internet Protocol Version 4 (TCP/IPv4)**:  
Ange nya inställningar:  
IP address: **192.158.1.51**  
Subnet mask: **255.255.255.0**  
→ **OK** 

### Alternativ PowerShell konfiguration
Starta PowerShell med "Run as administrator" och ange
>New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress "192.168.1.51" -PrefixLength 24


- - -



### Verifiera anslutning med både virtuella enheter 
Öppna vardera PowerShell/Terminal på var sin enhet och använd ping för att säkerställa att enheter når varandra.
![Windows 11 PowerShell ping](./2_005_ping2fw.png)  
Windows 11 använder PowerShell/kommandotolken:   
>ping 192.168.1.50
- - - 
![Ubuntu Desktop Terminal ping](./2_005_ping1.png)  
Ubuntu använder Terminal: 
>ping 192.168.1.51  
- - - 
### När Ubuntu inte når Windows 11 med ping:
**Alternativ 1**   
Öppna PowerShell med "Run as administrator".  
**Stäng ned Windows 11 VM brandvägg:** 
>Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False  

Stänger ner Windows Defender Firewall som är operativsystemets brandvägg.  
Detta är inte ett rekommenderat val om enheter är anslutna till öppet nät eller Internet, men som fungerar i sluten testmiljö.
- - - 
**Alternativ 2**  
Öppna PowerShell med "Run as administrator".  
**Tillåt inkommande "ICMP Echo Request"-anrop:**

>New-NetFirewallRule -DisplayName "Tillåt ICMP Inkommande (PING)" -Direction Inbound -Protocol ICMPv4 -IcmpType 8 -Action Allow  

En ny regel under namn "Tillåt ICMP Inkommande (PING)" läggs till i brandväggsinställningar som tillåter ping.
- - - 
**Alternativ 3**  
<span style="color: red">Obeprövad lösning, använd på egen risk.</span>   
Öppna Kommandotolken med "Run as administrator". 
>netsh advfirewall firewall add rule name="Tillåt ICMP Inkommande" protocol=icmpv4:8,any dir=in action=allow  

En ny regel under namn "Tillåt ICMP Inkommande" läggs till i brandväggsinställningar som tillåter ping.  

→ **Verifiera med  ping.**  
- - - 
### Tabell över konfiguration

| Hostname | Operativsystem | IP-adress | Subnätmask | Standard Gateway |
| :--- | :--- | :--- | :--- | :--- |
| Ubuntun-VirtualBox | Ubuntu Desktop | 192.168.1.50 | 255.255.255.0 | Ingen (Host-Only) |
| Windowsen | Windows 11 | 192.168.1.51 | 255.255.255.0 | Ingen (Host-Only) |







## Del 3 - Kommandoradsarbete och Felsökning (Kursmål 9)  
### * Linux (Bash)  
Skapa kataloger och filer och ändra behörigheter:

>sudo mkdir -p /var/systementor/konsultdata  
-p gör att undermappar i mappen kan skapas.
sudo" ger tillfälliga administrationsrättigheter för att skriva i mappar såsom /var/

>cd /var/systementor/konsultdata
förflytta dig till konsultdata-katalogen

>sudo touch anteckningar.txt  
skapar txtfil i mapp vi står i

>ls -la
listar alla filer och mappar i kataloger samt behörighetsinformation och egenskaper.  
![Skapa grupper och ändra behörigheter](./3_002_rights.png)  
skapar gruppnamn konsulter
>sudo groupadd konsulter  

chgrp ändrar ägandegrupp för olika filer eller kataloger.
-R gör att hela katalogstrukturen och underliggande filer tilldelas samma värde.
>sudo chgrp -R konsulter /var/systementor/konsultdata  

applicerar "least privilege"-principen till kataloger och filen anteckningar.txt
>sudo chmod 750 /var/systementor/konsultdata  
>sudo chmod 640 /var/systementor/konsultdata/anteckningar.txt  

Hjälper oss verifiera att rättigheter är efter önskade värden.
katalog "drwxr-x--- (750)" tillhör ägare root och grupper konsulter.
fil anteckningar.txt "-rw-r---- (640) och tillhör ägare root och grupper konsulter.
>sudo ls- la  

![Verifera nätverkanslutning till Windows-Vm samt nätverkskortets detaljer för Ubuntu-VM](./3_005_verifyping_addrshow.png)
Verifiera nätverksanslutning till Windows-VM samt nätverkskortets detaljer för Ubuntu-VM.
>ping 192.168.1.51  
>ip addr show  


### * Windows (PowerShell)  
![Windows PowerShell med administratörsrättigheter](./3_006_wfolderacl.png)
Starta PowerShell med administratörsrättigheter.

Skapa katalog KonsultData i Systementor.  
New-Item skapar nytt objekt, -ItemType Directory bestämmer vilken typ av föremål och -Path bestämmer destination.
>New-Item -ItemType Directory -Path "C:\Systementor\KonsultData"  

Läs behörigheter över katalog med hjälp av Access Control List: 
>Get-Acl "C:\Systementor\KonsultData"

![Windows PowerShell visar Ping och Nätverkansinställningar](./3_007_wpingIP.png)
Verifiera nätverkanslutningen till Ubuntu-VM genom ping samt visa nätverksinställningar:
>ping 192.168.1.50  
>ipconfig /all  

## Del 4 - AI-stöd och Kritisk Utvärdering (Kursmål 11)  


-----------
Jag är studerande på YH-skola mot IT och jag vill att du agerar lärare till mig. Jag behöver hjälp att förstå och lära mig, jag har tidigare inte gjort detta och jag vill att du förklarar på ett enkelt sätt. Bryt gärna ned svaret i steg med förklarande texter.
Här är min uppgift:
Hur skapar jag en mapp i PowerShell win11?
-----------
Hej! Kul att du pluggar IT. Att skapa en mapp via PowerShell är enkelt när man lär sig grundkommandona.

Här är en snabb genomgång:
Steg 1: Öppna PowerShell

Tryck på Windows-tangenten, sök efter PowerShell och tryck Enter. Det öppnar gränssnittet där du kan skriva dina kommandon.
Steg 2: Gå till rätt plats (Valfritt)

Använd kommandot cd följt av sökvägen (t.ex. cd C:\Users\DittNamn\Desktop) för att välja var mappen ska ligga. Om du inte gör detta skapas mappen i din standardmapp.
Steg 3: Skapa mappen

Skriv kommandot New-Item -ItemType Directory -Name "MinMapp" och tryck Enter. Alternativt kan du använda det kortare kommandot mkdir "MinMapp".

Vill du testa att skapa en fil i mappen också?
-----------
//Svaret som AI gav mig var tillräckligt för att lösa problemet samt exempel hur man använder kommando. Däremot svarade inte AI med vad delar av svaret betyder eller gör. Vad exempelvis är New-Item? Vad innebär -ItemType? Flera terminalkoder är ofta förkortningar och genvägar, ett exempel på detta är "ls --format=long --all" som kan förkortas "ls -la". En del kommandorader går att lista ut med logik och andra inte, ett annat exempel som kan förvirra betydelsen är kommandot "Get-ChildItem" i PowerShell.

-----------
Vad betyder "New-Item -ItemType Directory -Name "MinMapp""? 
-----------
* New-Item: Själva kommandot som talar om för PowerShell att du vill skapa något nytt (t.ex. en fil eller en mapp).
* -ItemType Directory: En parameter som bestämmer vad du vill skapa. Directory betyder att det ska vara en mapp (katalog) istället för en vanlig fil.
* -Name "MinMapp": Anger namnet på det du skapar, här blir mappen hetande "MinMapp".
-----------

//Jag testade kommando i PowerShell och det gav mig rätt svar på min uppgift. Jag säkerställer att det jag skriver och använder mig av i terminal är saker jag förstår. Är jag exprimentell använder jag virtuella maskiner för att sandboxa och testa saker.
Detta ökar min säkerhetsbild virtuella maskiner är låsta från min värd-dator och internet.




## AI-logg och Utvärdering:  

Del 4. AI-stöd och Kritisk Utvärdering (Kursmål 11)
AI-logg och Utvärdering

Jag har valt att använda Google Gemini som är ett generativt AI-verktyg som ska hjälpa mig med att utförligt stödja och hjälpa mig med
kommandorader för att skapa en ny katalog i PowerShell (Windows).

--------------------------------------------------------------------------------
Jag är studerande på YH-skola mot IT och jag vill att du agerar lärare till mig. Jag behöver hjälp att förstå och lära mig, jag har tidigare inte gjort detta och jag vill att du förklarar på ett enkelt sätt. Bryt gärna ned svaret i steg med förklarande texter.
Här är min uppgift:
Hur skapar jag en mapp i PowerShell win11?
--------------------------------------------------------------------------------
Hej! Kul att du pluggar IT. Att skapa en mapp via PowerShell är enkelt när man lär sig grundkommandona.

Här är en snabb genomgång:
Steg 1: Öppna PowerShell

Tryck på Windows-tangenten, sök efter PowerShell och tryck Enter. Det öppnar gränssnittet där du kan skriva dina kommandon.
Steg 2: Gå till rätt plats (Valfritt)

Använd kommandot cd följt av sökvägen (t.ex. cd C:\Users\DittNamn\Desktop) för att välja var mappen ska ligga. Om du inte gör detta skapas mappen i din standardmapp.
Steg 3: Skapa mappen

Skriv kommandot New-Item -ItemType Directory -Name "MinMapp" och tryck Enter. Alternativt kan du använda det kortare kommandot mkdir "MinMapp".

Vill du testa att skapa en fil i mappen också?
--------------------------------------------------------------------------------
Vad betyder "New-Item -ItemType Directory -Name "MinMapp""? 
--------------------------------------------------------------------------------
New-Item: Själva kommandot som talar om för PowerShell att du vill skapa något nytt (t.ex. en fil eller en mapp).

-ItemType Directory: En parameter som bestämmer vad du vill skapa. Directory betyder att det ska vara en mapp (katalog) istället för en vanlig fil.

-Name "MinMapp": Anger namnet på det du skapar, här blir mappen hetande "MinMapp".
---------------------------------------------------------------------------------
vilka fler liknande kommandon i PowerShell kan vara viktigt för mig att känna till, exempelvis när jag skapar mappar? 
---------------------------------------------------------------------------------
mkdir: En genväg för att snabbt skapa nya mappar direkt utan att skriva hela New-Item.

Remove-Item: Används för att radera mappar eller filer (motsvarar rm i Linux).

Set-Location (eller cd): Används för att byta till en annan mapp.
---------------------------------------------------------------------------------
 Vad är cd förkortning av? 
---------------------------------------------------------------------------------
cd är en förkortning av Change Directory. Det används för att byta till en annan mapp i terminalen.
---------------------------------------------------------------------------------
varför finns det flera kommandon för samma typ av händelser? borde inte Set-Location ha en förkortning mer lik SL än CD?
---------------------------------------------------------------------------------
PowerShell har flera kommandon för samma sak (så kallade alias) för att underlätta för användare vana vid antingen Windows CMD eller Linux. Roligt att du tänker på det – Set-Location har faktiskt sl som ett inbyggt alias, precis som cd!
---------------------------------------------------------------------------------
Varför nämnde du inte SL som första alternativ? Varför nämna CD senare? och inte alls SL förrän jag frågar efter detta? 
---------------------------------------------------------------------------------
cd nämndes först eftersom det är den absolut vanligaste standarden i alla terminaler. sl utelämnades eftersom det i praktiken nästan aldrig används av utvecklare, även om det finns som ett inbyggt alias. Jag ville fokusera på de kommandon du har störst nytta av direkt.
---------------------------------------------------------------------------------
vad är historien bakom CD vs Set-Location/SL? Varför finns flera kommandon för samma ärende? 
---------------------------------------------------------------------------------
PowerShell använder en egen standard med tydliga namn som Set-Location, men lade till inbyggda genvägar (alias) för att underlätta övergången från äldre system. Genvägen cd lades till för att MS-DOS- och Linux-användare direkt skulle känna igen sig, medan sl är den logiska förkortningen av systemets eget namn. Syftet med flera kommandon är alltså enbart att göra terminalen användarvänlig för alla, oavsett vilken IT-bakgrund du har.
---------------------------------------------------------------------------------



Svar som Gemini ger mig är tillräckligt för att lösa min uppgift, att skapa ny mapp. Den talar om vad olika kommandon betyder och vad deras funktioner innebär, särskilt när man ställer direkta följdfrågor. Kommandon som ofta används har även fått förkortningar till sig, att istället skriva "Set-Location" kan förkortningen "cd" användas som är en förkortning av 'Change Directory'. Jag säkerställer att det AI har lärt mig är rätt genom att själv prova och skapa mapper i PowerShell i testmiljö som en virtuell maskin för Windows 11.

Efter fler följdfrågor kommer fler svar fram, man kan tycka att Gemini skulle ha berättat för mig om kommandot SL tidigare men Gemini nämner om att SL inte är ett standardiserat kommando. De är enklare att minnas förkortningar samt enklare att skriva än "Set-Location", som nybörjare eller studerande kan detta skapa förvirring i vad det är som gäller, ska man lita på att AI-verktyg bedömmer rätt åt en? 
Detta skulle kunna innebära säkerhetsrisker i aktiva IT-miljöer då AI-verktyg kan filtrera bort kritisk information som användare värderar.

Slutsats att dra är att Gemini inte verkade hallucinera någonting vid denna frågeställning, kanske att verktyget valde bort information som inte tycktes vara viktigt för mig, egna antaganden, som ledde till följdfrågor från användaren. Annarsvis var all kod korrekt.