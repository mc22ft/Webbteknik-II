#Rapporten

Mathias Claesson mc22ft


#Förord

Denna rapport baserar sig på de brister och fel som jag har hittat i applikationen Labby message. Detta är Laboration 2 i kursen Webbteknik II på Línneuniversitetet. Kursansvarig är John Häggerud. 

I Kapitlet “Säkerhetsproblem” hittar läsaren underrubriker som grundar sig på OWASP Top 10 lista gällande säkerhetshål från år 2013[1]. I kapitlet “Prestandaproblem” har jag utgått från Steve Sounders, *High Performance Web Site*[9].

Några verktyg som jag har använt är självstudiematerial från kurs hemsidan, Google för att söka på enstaka problem lösningar, Postman för att inspektera olika anrop, Chrome och Mozilla för att inspektera koden på applikationen. För mer ingående information så finns referenser till varje del av rapporten.

#Säkerhetsproblem

##Injection
Injektion är den vanligaste typen av angrepp enligt tio i topp listan från OWASP[1]. Här är det databasen på applikationen som blir angripen. Angriparen försöker manipulera databasen genom att skicka SQL-frågor i indata fälten[1]. På detta sätt luras databasen att skicka tillbaka den data eller det svar som angriparen är ute efter. 
Alla indatafält som finns i applikationen kan bli utsatt för ett angrepp.

######Eventuella följder problemet kan skapa?
Största och allvarligaste hotet skulle jag påstå är på inloggningen. På en applikation där injektion är möjlig kan man skicka in SQL-frågor i inloggningsfältet och därefter bli inloggad utan några större problem. Detta innebär att t.e.x. jag skulle kunna ta över hela kontot. Allvarligare följder skulle kunna vara att delar eller hela databaser kan blir dumpade/raderade av dessa angrepp. Det skulle alltså vara möjligt att skicka in vilka SQL-frågor som helst mot databasen. 
Följderna för en användare av applikationen, som blir utsatt av injektion, skulle kunna vara att köpa eller skriva saker i användares namn. Om det skulle varit ett Administrationskonto som injicerats kan denne göra vad hen vill på hela applikationen. Allt från att ändra priser, namn, radera saker i databasen och få ut känslig information gällande medlemmar av applikationen. Med känslig information menas användarnamn och lösenord, om de inte är krypterade, och i värsta fall kreditkortsuppgifter.

######Hur man åtgärdar problemet?
Hur förhindras Injektion?
* Det bästa alternativet är att använda variabel bindning av data som kommer in till applikationen. Det går till så att när datan kommer fram till databasen ska det vara en parameter och inte en fråga. T.e.x. att fråga om ett lösenord där utgången av ‘1’=’1 = true. Det skulle resultera i att en blev inloggad om det var möjligt med injektioin. Att skicka datan med variabel bindning så att databasen skulle försöka hitta lösenordet “‘1’=’1” som är en sträng[3].
* Lagrade procedurer är ett annat sätt att skydda sig. Här ligger skyddet i själva databasen. Då utvecklaren har färdiga SQL satser som styrs av parametrar från applikationen[3]. Detta fungerar lite som beskrivet ovan fast i databasen.
* Validering på indatan i applikationen är också ett sätt, dock inte 100 procentigt  säkert. Det kan vara lätt att glömma bort något som är viktigt. Här skulle man kunna stoppa vissa specialtecken som databasen kräver i sina SQL frågor eller hela SQL-frågor[3]. 

##Broken Authentication and Session Management
Detta rör applikationens hanteringen av identifieringens funktioner. Detta beror på att hanteringen av session inte har gjorts korrekt. På så sätt kan lösenord, nycklar eller cookie session synas öppet på något sätt i applikationen[1]. Eller en session som inte blir raderad när man loggar ut.

######Eventuella följder problemt kan skapa?
Konton i sig är målet på denna attack. Angriparen vill åt hela konton helst de konton som är högt betrodda[1]. På detta sätt kunna manipulera omgivningen till kontohavaren samt utföra bedrägerier av olika slag. 

######Hur man åtgärdar problemet?
Använda sig av en enda uppsättning av identifiering och se till att sessionhanteringen går korrekt till. Nedan anges de kontroller som man kan sträva efter att följa[1]. 
* Uppfylla alla identifierings- och sessionhantering som anges i OWASP´s *Security Verification Standard*[2]
* Utvecklaren använder ett enkelt interface. Värt att undersöka skulle vara *ESAPI Authenticator and User APIs*[7].
* Det är också viktig att lägga mycket arbeta på att skydda sig mot XSS attacker vilket innebär att användarens session blir stulen av angriparen[1].

##XSS Cross site scripting
Det innebär att angriparen på något sätt planterat ut skadlig kod på en dåligt validerad applikation. Om användaren klickar på något script från angriparen finns risken för att användarsession kan kapas. Man blir då skickad till en manipulerad applikation som utger sig för att vara något de inte är, eller rent av skadliga sidor för användaren[1][5].

######Eventuella följder problemt kan skapa?
När någon får tag i din session cookie kan följderna bli förödande. Angriparen kommer att kunna vara inloggad på den sidan som cookien kommer ifrån. Det vanligaste skulle vara ett forum där det går att skriva in javascript för att få ut sessioninformation om användaren[5]. Angriparen kan nu låtsas var kontoinnehavaren och manipulera anhöriga på olika sätt för att t.e.x. få tag i  pengar. Om angriparen lyckats få cookie:n från ett forum där det även går att köpa saker skulle angriparen kunna beställa saker i användarens namn.

######Hur man åtgärdar problemet?
Utvecklaren ska i den mån det går undvika möjligheten att kunna skicka in skadlig kod på applikationen som sedan kan kommer ut till användarna[1]. Utvecklaren kan skapa speciella session-id för användaren baserad på tex browser och ip nummer. Ett annat sätt är att byta ut session-id som användaren har ofta. På detta sätt så förbättras chanserna att inte råka ut för session-kapning[5]. 

##Sensitive Data Exposure
Problemet med att skydda data som bör vara skyddat är svårt då många applikationer ligger helt öppna för vem som helst att se. Angriparen behöver inte anstränga sig så mycket när datan ligger helt öppen på detta sättet.. Här är det utvecklaren som måste avgöra vilken data som ska vara synlig eller inte[1]. 

######Eventuella följder problemt kan skapa?
Följderna med öppen data kan vara förödande för alla i samhället. Det kan vara lösenord, kredituppgifter, känslig data för privatpersoner, företagshemligheter osv. som ligger öppna. I och med detta så kan angriparen göra vad hen vill med uppgifterna.  Detta kan resultera i kreditkortsbedrägeri, identitets stöld eller andra brott.. 

######Hur man åtgärdar problemet?
Det är viktigt att inga länkar ligger öppna, ett bra verktyg kan vara ett som hittar alla öppna länkar på applikationen[6] och därefter analysera om det är något som inte ska vara där. Andra saker som att hascha lösenord så att de inte sparas i klartext. Se över säkerheten för känslig data mycket noga. Kryptera den data som är känslig om den mot all förmodan skulle kommas åt inte ska gå att få fram som klartext. Spara inte data i onödan utan radera den så fort den inte behövs[1].

##CSRF Cross-Site Request Forgery
I detta fall så tvingar angriparen att skicka en HTTP anrop i offrets webbläsare. Offret blir utsatt med skadlig kod. Det kan t.e.x. vara en manipulerad image tagg, XSS attack eller något annat sätt som gör att någon blir offer för en hackerattack.
Detta anrop sker i bakgrunden och med angriparens uppgifter i anropet som sker. Offret måste då vara inloggad på den plats som angriparen gör sin attack på om det ska lyckats. 

######Eventuella följder problemt kan skapa?
Attacken är ganska enkel. En CSRF sårbarhet tillåter en angripare att tvinga en inloggad användare att utföra en viktig åtgärd utan deras medgivande eller vetskap. Det skulle resultera i att du blir av med pengar, varor kan bli beställda eller så kan du bli mål för att leverera ut spam. Det kan vara så att angriparen gör så att annonser blir riktade mot dig utan att du vet om det. Denna attack i sig lämnar inga spår efter sig.

######Hur man åtgärdar problemet?
Synchronizer Token Pattern är det sätt som är vanligast att skydda sig med. Det går ut på att serversidan ska upptäcka manipulering av HTTP anropet och ett unikt token skickas med i varje förfrågan. På detta sätt skyddas användaren av attacken men det fungerar bara på POST anrop, inte på ett GET anrop[8].


#Prestandaproblem 

##Make fewer HTTP requests
Det första man kan göra är att titta på antal HTTP anrop som sker. Det kan vara bilder som ska laddas upp för användaren. Detta kan lösas med att kombinera ihop bilder för att göra ett anrop istället för kanske fem. Sedan placera ut de med hjälp av CSS på sidan[9, s. 10] och när det gäller CSS så minskar tiden för att ladda upp sidan om man bara har en CSS fil. Kombinera ihop CSS till en fil förbättrar prestandan[9, s. 15].
Det är också möjligt att utnyttja URL schema som finns inbyggt. Där man kan “förvara” data som laddas fram omgående utan ett HTTP anrop[9, s. 13].

##Add an expires header
Ett annat sätt är att spara data i browserns cache(minne). På detta sätt kan man undvika att göra så många HTTP anrop mot servern. Cacha data kan göras med det mesta utom själva HTML dokument. Det vanligaste är att man sparar bilder. Man ska inte vara rädd att spara scripts och CSS eller andra liknade filer[9, s. 29-30]. 

##Put stylesheets at the top
För att applikationen ska renderas upp på bästa sätt ska CSS länkas in i headern i en link tagg[9, s. 41]. Om detta inte görs och lägger den sist i HTML dokumentet så riskerar man att applikationen får en vit laddnings sida. Som i sin tur kan göra att man förlorar användare till en konkurrerande sida[9, s. 38].

##Put scripts at the bottom
För att inte riskera att få en fördröjning på renderingen av applikationen ska script (javascript) filer placeras i botten på applikationen[9, s. 49]. Hämtningen av komponenter görs med  parallella nedladdningar. Om det skulle uppstå störningar med att ladda script mellan dessa nedladdningar kan detta innebära att det tar mer tid att rendera upp applikationens[9, s. 46].


##Make JavaScript and CSS External
Att använda sig av externa filer för javascript och CSS rekommenderas. Även om applikationen skulle renderas upp fortare av att ha CSS internt. Detta endast första gången HTML dokumentet laddas hem. Man slipper då i detta fallet extra HTTP anrop av filer vilket är optimalt för en applikation som bara har en sida med endast unika återkommande användare[9, s. 55]. 
Observera, då möjligheten att spara filerna i browserns cashe minne finns bör man utnyttja detta om applikationen ska renderas upp på snabbt.. Att casha datan görs bara första gången applikationen hämtas hem därefter så hämtas det utan att göra ett HTTP anrop från cashen. Det finns även sätt att jobba runt för att slippa göra stora HTTP anrop för första gången av filer, då en applikation ska renderas upp. I längden så vinner man på att ha externa filer[9, s. 57-62]!

##Minify JavaScript
Att komprimera javascript koden är att föredra. Det innebär att man tar bort onödig kod som som finns i dokumentet. Alla kommentarer och all “white space” (mellan slag, ny rad och tabbningar) tas bort. Förkortade namn på variabler och övrig kod görs för att minska den så mycket det går[9, s. 69]. Att göra detta kan medföra både buggar och problem med underhåll, debugging kan bli svår att tolka. Det finns bra verktyg för att göra detta som bör användas. JSMin är ett exempel som ikonen Douglas Crockford utvecklat. Sedan finns det en uppsjö av andra verktyg[9, s. 70].

##Egna reflektioner
Några saker jag skulle vilja lyfta gällande prestandan på applikationen är att jag kunde se lite onödig HTML kod på applikationen. Detta skulle kunna innebära att HTTP anropet blir större än det borde vara.
Funktioner som inte fungerade skulle i värsta fall kunna innebära att data inte kan tas bort från applikationen. T.e.x. om det skulle varit en “remove message” knapp som inte fungerade. Det skulle innebära att det skulle ta längre och längre tid för servern att ladda upp datan på applikationen. 

#Egna övergripande reflektioner

Tanken som slår mig är att alla säkerhetshål, oavsett om det är en attack av en injektion eller någon annan hacker attack, resulterar i att datan som kommer fram är nästan densamma. Det är ett stort hot mot användarna och till och med samhället om känslig data hamnar i fel händer.

Det ligger ett stort ansvar på utvecklarna att “täppa till” alla säkerhetshål som är öppna samt hålla applikationens alla delar uppdaterade med senaste tekniken. Det börjar sjunka in att det är mer jobb med underhåll och uppdateringar än jag tidigare trott. 
Att jobba med säkerheten på ett företag, anser jag,  kräver stort ansvar och ett stort kunnande om vilka säkerhetshål som finns och lika snabbt som det kommer nya tekniker kommer det också nya problem med säkerhet och attacker. 

När det gäller prestandaproblem så känns det likadant, det tycks finnas mer att jobba på än jag visste förut. Det är inte bara att “kasta ut” hemsidor på webben och tro att de ska fungera hur bra som helst. Det krävs mycket tid och jobb att utveckla en bra fungerande applikation. Jag kan bara referera till mig själv då jag ofta väljer bort många sidor som tar lång tid på sig att ladda fram. Denna sidan kunde fått en användare till om utvecklaren jobbat mer på prestandan.

En rolig sak som fick mig att skratta och tänka till var det som nämndes i kurslitteraturen gällande om man har en progressbar för att få användaren att fokusera på något annat samtidigt som sidan renderas upp. På det sättet kunde man “köpa” sig lite HTTP tid. Det var lite mitt i prick hur det skulle kunna fungerar att ha kvar en användare eller inte.
Jag undrade om boken[9] fortfarande är lika aktuell som när den gavs ut (2007). Denna tanke gjorde att jag blev lite tveksam till vissa delar och ville veta om det forfarande fungerade så idag. Dock måste jag säga att boken var bra och mycket lättläst. 



#Refernslista

[1] OWASP, "OWASP Top Ten Project," *www.owasp.org*, 12 June 2013, [Online] Tillgänglig pdf: https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013. [Hämtad: 26 November, 2015].

[2] SoapUI, "OWASP Application Security Verification Standard Project," *www.owasp.org*, 4 November 2015 [Online] Tillgänglig: https://www.owasp.org/index.php/ASVS. [Hämtad: 01 December, 2015].

[3] OWASP, "SQL Injection Prevention Cheat Sheet," *www.owasp.org*, 11 Maj 2015 [Online] Tillgänglig: https://www.owasp.org/index.php/SQL_Injection_Prevention_Cheat_Sheet
. [Hämtad: 30 November 2015].

[4] Wikipedia, "Hypertext Transfer Protocol Secure," *https://sv.wikipedia.org*, 9 November 2015, [Online] Tillgänglig: https://sv.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure. [Hämtad: 26 November, 2015].

[5] YouTube, "Demo XSS," *https://www.youtube.com*, 19 Mars 2012, [Online] Tillgänglig pdf: https://www.youtube.com/watch?v=KiWJ8D-kHiI. [Hämtad: 26 november, 2015].

[6] XML-Sitemaps, "Build your Site Map online," *https://www.xml-sitemaps.com*, [Online] Tillgänglig: https://www.xml-sitemaps.com. [Hämtad: 30 november, 2015].

[7] owasp-esapi-java, "Interface Authenticator," *http://owasp-esapi-java.googlecode.com*, 1 June 2007, [Online] Tillgänglig: http://owasp-esapi-java.googlecode.com/svn/trunk_doc/latest/org/owasp/esapi/Authenticator.html. [Hämtad: 01 December, 2015].

[8] OWASP, "Cross-Site Request Forgery (CSRF) Prevention Cheat Sheet
," *www.owasp.org*, 11 Maj 2015, [Online] Tillgänglig: https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet. [Hämtad: 1 December, 2015].

[9] O,REILLY, Steve Sounders, *High Performance Web Sites*. Sebastopol: O,REILLY Media Inc, September 2007.
