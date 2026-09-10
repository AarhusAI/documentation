---
title: Vejledning: Funktioner i OpenWebUI — hvad gør de?
---

## Vejledning: Funktioner i OpenWebUI — hvad gør de?

Når du opretter eller konfigurerer en assistent i **Workspace → Models**, kan du aktivere en række funktioner.
Denne
vejledning forklarer hvad hver funktion gør, og hvornår det er hensigtsmæssigt at aktivere den.

**Bemærk:** Nogle funktioner er deaktiveret på serverniveau i Aarhus AI (markeret ❌ i tabellerne nedenfor).
Disse
afkrydsningsfelter er stadig **synlige** i model-editoren og kan sagtens afkrydses — men de har ingen effekt,
uanset om
de er slået til eller fra, så længe funktionen er deaktiveret på serverniveau. De bør som udgangspunkt **ikke
slås
til**, selvom det er muligt, da det giver et misvisende indtryk af, at funktionen er aktiv.

---

## Funktioner vs. Standardfunktioner

I model-editoren optræder to rækker, som nemt kan forveksles:

- **Funktioner** styrer, om modellen overhovedet har adgang til en given evne. Er en funktion slået fra her,
  kan
  modellen aldrig bruge den — uanset hvad brugeren gør i selve chatten.
- **Standardfunktioner** styrer kun, om en funktion er **slået til som udgangspunkt**, når en bruger starter
  en ny chat.
  Den findes kun for de tre værktøjer, der har en per-chat-knap i selve chat-vinduet: **Websøgning**,
  **Billedgenerering** og **Kode Interpreter**.

For netop disse tre værktøjer er der reelt tre lag, der alle skal være opfyldt, før modellen kan bruge
værktøjet i en
given samtale:

1. Global admin-indstilling på serveren
2. **Funktioner**-afkrydsningen — er værktøjet overhovedet tilladt for modellen
3. **Standardfunktioner**-afkrydsningen — er værktøjet slået til som standard, eller skal brugeren selv slå
   det til for
   den enkelte samtale via knappen i chat-vinduet

Hvis en **Funktion** er slået fra, er det ligegyldigt om **Standardfunktion** er slået til — værktøjet er
stadig
utilgængeligt. Er **Funktionen** derimod slået til, men **Standardfunktionen** slået fra, er værktøjet
tilgængeligt, men
brugeren skal selv aktivere det manuelt i chat-vinduet, hver gang de vil bruge det.

De øvrige funktioner (Vision, Fil Upload, File Context, Citater m.fl.) har ikke en per-chat-knap og optræder
derfor kun
under Funktioner — de er enten slået til eller fra for modellen som helhed.

**I Aarhus AI er Standardfunktioner-sektionen reelt uden betydning.** Websøgning, Billedgenerering og Kode
Interpreter
er alle deaktiveret på serverniveau (se tabellen nedenfor) — altså blokeret allerede i lag 1. Det er derfor
ligegyldigt,
hvad der står under Standardfunktioner for disse tre; værktøjerne kan aldrig bruges, uanset afkrydsning. I bør
derfor
aldrig udfylde noget i Standardfunktioner-sektionen i vores nuværende opsætning.

---

## Opsummering

Der er tre niveauer at holde styr på: **Værktøjer**, som er eksterne integrationer tilføjet af en
administrator,
**Funktioner**, som er indbyggede indstillinger i model-editoren, og **Builtin Tools-kategorier**, som kun har
effekt,
hvis den overordnede **Builtin Tools**-funktion er slået til (se forklaring i afsnittet om Builtin Tools).
Værktøjer og
Funktioner findes desuden to forskellige steder i brugergrænsefladen — se afsnittet **Værktøjer** nedenfor for
detaljer.

### Værktøjer

Værktøjer er eksterne integrationer, der er tilføjet til Aarhus AI af en administrator, og optræder i deres
egen sektion
i model-editoren. Der kan løbende komme flere til efterhånden som nye værktøjer bliver tilføjet.

| Navn | Anbefaling | Bemærkning |
| --- | --- | --- |
| Websearch | Efter behov | Søger på internettet |

### Funktioner

| Navn | Anbefaling | Bemærkning |
| --- | --- | --- |
| Vision | Efter behov | Analyserer uploadede billeder |
| Fil Upload | Efter behov | Uploader dokumenter til chatten |
| File Context | Efter behov | Bestemmer hvordan uploadede filer bruges af modellen |
| Hukommelse | ❌ Ikke tilgængelig | Deaktiveret på serverniveau |
| Websøgning | ❌ Aldrig | Deaktiveret på serverniveau |
| Billedgenerering | ❌ Aldrig | Deaktiveret på serverniveau |
| Kode Interpreter | ❌ Aldrig | Deaktiveret på serverniveau |
| Terminal | ❌ Aldrig | Ikke installeret — må aldrig aktiveres |
| Forbrug | ❌ Ikke relevant | Kun nyttigt ved betaling per token til ekstern API |
| Citater | ✅ Anbefales | Særligt ved brug af vidensbaser |
| Statusopdateringer | ✅ Anbefales | Viser hvad modellen arbejder på |
| Builtin Tools | ✅ Anbefales, hvis vidensbase anvendes | Kræver kendskab til hvad der injiceres |

### Builtin Tools-kategorier

Disse er underindstillinger, der kun vises og har effekt, når **Builtin Tools** ovenfor er slået til.

| Kategori | Anbefaling | Bemærkning |
| --- | --- | --- |
| Time & Calculation | ✅ Anbefales | Simple tids- og datoberegninger, ingen server-afhængighed |
| Hukommelse | ❌ Ikke tilgængelig | Deaktiveret på serverniveau |
| Chat History | ❌ Skal slås fra | Skal fjernes manuelt på hver model. Se bemærkning nedenfor. |
| Noter | ❌ Ikke tilgængelig | Deaktiveret på serverniveau |
| Vidensbase | ✅ Anbefales | Nødvendig hvis assistenten skal bruge vidensbaser |
| Ask User | Efter behov | Styres kun via kategori-afkrydsningen pr. model |
| Websøgning | ❌ Aldrig | Deaktiveret på serverniveau — brug Websearch-toolet i stedet |
| Billedgenerering | ❌ Aldrig | Deaktiveret på serverniveau |
| Kode Interpreter | ❌ Aldrig | Deaktiveret på serverniveau |
| Kanaler | ❌ Aldrig | Deaktiveret på serverniveau |
| Task Management | Efter behov | Opretter og opdaterer tjeklister for flertrinsopgaver i chatten |
| Automations | ❌ Aldrig | Deaktiveret på serverniveau |
| Kalender | ❌ Aldrig | Deaktiveret på serverniveau |
| Files | Efter behov | Kræver File Context slået fra for at have effekt |
| Notifikationer | ❌ Aldrig | Deaktiveret på serverniveau |
| Sub-agents | ❌ Aldrig | Deaktiveret på serverniveau |

---

## Værktøjer

Værktøjer er eksterne integrationer der er tilføjet til Aarhus AI af en administrator og optræder i sin egen
sektion i
model-editoren, adskilt fra Funktioner. De adskiller sig fra Funktioner ved at de kan kalde eksterne
tjenester.

---

### Websearch

**Hvad gør det?** Giver assistenten mulighed for at søge på internettet og hente aktuelle oplysninger som del
af sit
svar.

**Hvornår aktiveres det?** Når assistenten har behov for adgang til aktuelle oplysninger fra internettet.

**Sådan aktiveres det:**

1. Gå til **Workspace → Models** og åbn den assistent du vil redigere
2. Rul ned til sektionen **Værktøjer**
3. Sæt kryds ved **Websearch**
4. Klik **Save**

Websearch er nu tilgængeligt for assistenten og kan kaldes automatisk af modellen når den vurderer at et
søgeresultat
vil forbedre svaret.

---

## Funktioner

### Vision

**Hvad gør det?** Giver assistenten mulighed for at "se" og analysere billeder som brugeren uploader i
chatten. Modellen
kan beskrive, fortolke og besvare spørgsmål om billeder.

**Hvornår aktiveres det?** Når brugerne har behov for at uploade billeder som del af deres opgave — f.eks.
screenshots,
diagrammer eller scannede dokumenter.

---

### Fil Upload

**Hvad gør det?** Giver brugeren mulighed for at uploade dokumenter (PDF, Word, tekst m.fl.) direkte i
chatten, som
modellen kan læse og besvare spørgsmål om.

**Hvornår aktiveres det?** Når brugerne skal arbejde med dokumenter — f.eks. vejledninger eller rapporter.

Bemærk at Fil Upload kun styrer *om* brugeren kan uploade filer. Det er File Context (se nedenfor), der
bestemmer,
hvordan modellen efterfølgende bruger de uploadede filer.

---

### File Context

**Hvad gør det?** Bestemmer hvordan indholdet af filer, som **slutbrugeren selv uploader i chatten**, stilles
til
rådighed for modellen, når Fil Upload er aktiveret.

**Vigtigt:** File Context handler udelukkende om filer, brugeren uploader undervejs i en samtale — f.eks. et
dokument
brugeren vedhæfter for at spørge ind til det. Det har intet med assistentens **vidensbase** at gøre, og det er
heller
ikke filer, der er uploadet direkte på assistenten selv i Workspace → Models. Vidensbaser håndteres altid
gennem Builtin
Tools-kategorien "Vidensbase" (se ovenfor), uanset hvordan File Context er indstillet.

- **Slået til:** Hele filens tekst indsættes direkte i samtalen ved hver besked. Modellen har med det samme
  adgang til
  hele dokumentets indhold uden selv at skulle søge efter det.
- **Slået fra:** Filens fulde tekst indsættes ikke automatisk. I stedet kan modellen bruge søgeværktøjer under
  Builtin
  Tools-kategorien "Files" til selv at slå op i filen efter behov (liste filer, semantisk søgning, nøjagtig
  tekstsøgning, og læsning af uddrag).

**Hvornår aktiveres det?**

- **Anbefales aktiveret** for korte dokumenter (f.eks. et enkelt referencedokument eller en kort note), hvor
  det er
  vigtigt at modellen har hele indholdet til rådighed med det samme, og hvor omkostningen ved at indsætte hele
teksten
  er lav.
- **Kan med fordel slås fra** ved store eller flere filer, da det sparer tokens og skalerer bedre — men det
  kræver, at
  Builtin Tools-kategorien "Files" er aktiveret på modellen, og at modellen rent faktisk kalder
søgeværktøjerne. Mindre
  eller ældre modeller kan være mindre pålidelige til dette.

**Konkret anbefaling:** Slå File Context **til** som udgangspunkt for de fleste assistenter — det er den sikre
og
pålidelige løsning, uanset hvor god modellen er til selv at kalde værktøjer. Overvej først at slå den fra til
fordel for
Files-kategorien, hvis I oplever et konkret problem med store filer, mange vedhæftede filer, eller højt
tokenforbrug.
Undgå at have begge slået til samtidig — så er Files-kategorien reelt uden effekt, fordi indholdet allerede er
indsat
via File Context.

---

### Hukommelse

**Hvad gør det?** Denne Funktion-afkrydsning er den overordnede kapabilitet for hukommelse — dvs. om modellen
overhovedet må gemme fakta om brugeren på tværs af samtaler. Det er en anden indstilling end Builtin
Tools-kategorien
"Hukommelse" (se nedenfor), som styrer hvilke *værktøjer* modellen får til rent faktisk at gemme og hente
hukommelser.

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Hukommelse er deaktiveret på serverniveau og er derfor
ikke
tilgængelig, uanset om denne Funktion er slået til eller fra.

---

### Websøgning

**Hvad gør det?** Aktiverer den **indbyggede** websøgningsfunktion i OpenWebUI, som sender brugerens prompt
til en
ekstern søgetjeneste for at hente aktuelle informationer fra internettet.

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau og er derfor
ikke
tilgængelig. Brug i stedet Websearch-toolet under Værktøjer.

---

### Billedgenerering

**Hvad gør det?** Giver modellen mulighed for at generere billeder baseret på tekstbeskrivelser. Kræver at en
billedgenereringstjeneste er konfigureret (f.eks. DALL-E eller ComfyUI).

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau, da der ikke
er
installeret en billedgenereringstjeneste.

---

### Kode Interpreter

**Hvad gør det?** Giver modellen mulighed for at skrive og udføre Python-kode direkte i chatten — f.eks. til
dataanalyse, beregninger eller filbehandling.

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau.

---

### Terminal

**Hvad gør det?** Giver modellen direkte adgang til at udføre kommandoer i serverens terminal — svarende til
shell-adgang på selve serveren.

**Hvornår aktiveres det? Må aldrig aktiveres.** Terminal er ikke installeret i Aarhus AI og har ingen effekt
selv hvis
det afkrydses.

---

### Forbrug

**Hvad gør det?** Viser brugeren information om token-forbrug for den aktuelle samtale — dvs. hvor meget af
modellens
kapacitet der er brugt.

**Hvornår aktiveres det?** Ikke relevant i Aarhus AI. Forbrug er primært nyttigt når man betaler per token til
en
ekstern API — da modellen kører lokalt er der ingen direkte omkostning at overvåge for brugeren.

---

### Citater

**Hvad gør det?** Når modellen besvarer spørgsmål baseret på uploadede dokumenter eller vidensbase, vises
kildehenvisninger — dvs. hvilken del af hvilket dokument svaret er baseret på.

**Hvornår aktiveres det?** Anbefales aktiveret når assistenten bruger vidensbaser eller dokumenter, da det
giver
brugeren mulighed for at verificere svarene.

---

### Statusopdateringer

**Hvad gør det?** Viser løbende statusbeskeder mens modellen arbejder — f.eks. "søger i vidensbase",
"genererer svar".
Giver brugeren feedback om hvad der sker i baggrunden.

**Hvornår aktiveres det?** Anbefales aktiveret for en bedre brugeroplevelse, særligt når assistenten bruger
tools eller
vidensbaser.

---

### Builtin Tools

**Hvad gør det?** Aktiverer OpenWebUI's indbyggede systemværktøjer som modellen kan kalde selvstændigt —
herunder adgang
til vidensbaser, kanaler og agentic research.

**Hvornår aktiveres det?** Native Mode er aktiveret i Aarhus AI, så Builtin Tools er funktionelt. **Anbefales
slået til,
hvis assistenten anvender en vidensbase** — uden Builtin Tools kan modellen ikke selv opsøge indholdet i
vidensbasen (se
afsnittet om Vidensbase nedenfor). Ellers anbefales det kun aktiveret hvis du har sat dig ind i hvad det
betyder og
hvilke værktøjer der injiceres — da modellen selvstændigt beslutter hvornår den kalder dem. Hukommelse og
noter er
deaktiveret på serverniveau og er ikke tilgængelige.

Under Builtin Tools kan du finjustere hvilke **kategorier** af værktøjer modellen har adgang til. Kategorierne
gennemgås
enkeltvis nedenfor.

---

### Time & Calculation (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen simple værktøjer til at finde det aktuelle tidspunkt og udregne relative
datoer (f.eks.
"for 3 dage siden").

**Hvornår aktiveres det?** Kan trygt stå slået til — kræver ingen server-konfiguration og har ingen
sikkerheds- eller
omkostningsmæssig betydning.

---

### Chat History (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen mulighed for at søge i og læse brugerens tidligere chats — f.eks. for at
finde op på en
tidligere samtale eller undgå at spørge om noget, der allerede er blevet drøftet.

**Skal slås fra.** Denne kategori **skal altid være slået fra** på alle assistenter i Aarhus AI. Brugerens
tidligere
samtaler må ikke kunne genfindes og læses af modellen i en anden samtale.

Da denne kategori er slået til som standard på enhver ny model, er det nødvendigt at:

1. Gennemgå samtlige eksisterende modeller og fjerne krydset ved Chat History manuelt.
2. Sikre at fjernelse af Chat History indgår som fast tjekpunkt, når nye modeller oprettes fremover.

---

### Vidensbase (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen værktøjer til selv at finde og søge i vidensbaser, der er tilknyttet
assistenten —
f.eks. liste tilgængelige vidensbaser, søge i indholdet, og læse specifikke filer.

**Hvornår aktiveres det?** Anbefales aktiveret, når assistenten har en eller flere vidensbaser tilknyttet.
Uden denne
kategori kan modellen ikke selv opsøge indholdet, selvom vidensbasen er tilknyttet.

---

### Ask User (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen mulighed for at stoppe midt i et svar og selv stille brugeren et opklarende
spørgsmål,
i stedet for at gætte eller antage noget. Modellen kalder værktøjet `ask_user`, samtalen pauser, og brugeren
får et
interaktivt spørgsmål vist i chatten. Modellen fortsætter derefter svaret baseret på brugerens svar.

**Eksempel:** Hvis brugeren skriver "book et møde med Peter" uden at angive et tidspunkt, kan modellen med Ask
User
aktiveret stoppe op og spørge "Hvornår vil du booke det?" i stedet for at gætte eller give et upræcist svar.

**Relateret funktion:** Sammen med Ask User blev der i samme version (v0.11.1) introduceret "tool-call
approvals" — en
indstilling en administrator kan slå til, hvor værktøjskald (f.eks. afsendelse af en mail eller sletning af
noget) skal
godkendes af brugeren, før de udføres. De to funktioner er ikke det samme, men hænger konceptuelt sammen:
begge giver
brugeren kontrol midt i en samtale, i stedet for at modellen handler eller gætter på egen hånd.

**Hvornår aktiveres det?** Efter behov. Denne kategori styres udelukkende via kategori-afkrydsningen pr. model
— ligesom
Chat History.

---

### Kanaler (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen mulighed for at søge i og læse beskeder fra kanaler, som brugeren har adgang
til.

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau og er derfor
ikke
tilgængelig.

---

### Task Management (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen mulighed for at oprette en struktureret tjekliste over trin i en
flertrinsopgave, og
løbende opdatere status på de enkelte trin, mens den arbejder.

**Hvornår aktiveres det?** Efter behov — særligt nyttigt for assistenter, der løser komplekse opgaver i flere
trin, da
det giver brugeren indblik i fremdriften.

---

### Automations (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen mulighed for selv at oprette, ændre og slette planlagte automatiseringer —
f.eks. en
tilbagevendende opgave, der køres på et fast tidspunkt.

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau og er derfor
ikke
tilgængelig. Skal være slået fra.

---

### Kalender (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen mulighed for at søge i, oprette, opdatere og slette kalenderbegivenheder på
brugerens
vegne.

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau og er derfor
ikke
tilgængelig.

---

### Files (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen søgeværktøjer til filer, der er vedhæftet den aktuelle chat af
**slutbrugeren** —
modellen kan liste filerne, søge semantisk i dem, søge efter præcis tekst, og læse uddrag, i stedet for at få
hele
filens indhold indsat direkte i samtalen.

**Vigtigt:** Ligesom File Context handler denne kategori udelukkende om filer, brugeren selv har uploadet i
chatten —
ikke om assistentens vidensbase og ikke om filer uploadet direkte på assistenten.

**Vigtigt:** Denne kategori virker kun, når **File Context er slået fra** for modellen (se afsnittet om File
Context
ovenfor). Er File Context slået til, er indholdet allerede i samtalen, og disse værktøjer bliver ikke brugt.

**Hvornår aktiveres det?** Relevant hvis I ønsker at slå File Context fra for at spare tokens ved store eller
mange
vedhæftede filer. Kræver, at modellen er god til at kalde værktøjer selvstændigt (Native Mode) — mindre eller
ældre
modeller kan finde på slet ikke at kalde søgeværktøjerne, hvilket betyder at filen reelt ikke bliver brugt i
svaret.

**Konkret anbefaling:** Som udgangspunkt anbefales File Context frem for Files (se afsnittet om File Context
ovenfor) —
den er mere pålidelig. Slå kun Files til, og File Context fra, hvis I har et konkret behov for at håndtere
store eller
mange filer effektivt.

---

### Notifikationer (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen mulighed for selv at sende en notifikation til brugerens konfigurerede
webhook-mål
(f.eks. Slack eller Discord), når modellen selv vurderer at noget er relevant at gøre opmærksom på — f.eks.
når en
langvarig opgave er færdig.

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau og er derfor
ikke
tilgængelig.

---

### Sub-agents (Builtin Tools-kategori)

**Hvad gør det?** Giver modellen mulighed for at uddelegere en afgrænset delopgave til en "hjælpeagent", der
kører sin
egen selvstændige samtale med samme model og værktøjer, og derefter rapporterer resultatet tilbage til
hovedsamtalen.

**Hvornår aktiveres det? Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau og er derfor
ikke
tilgængelig. Bemærk desuden, at hver uddelegering er et helt ekstra modelkald, og at ét svar kan sætte flere
sub-agents
i gang parallelt — det ville øge både svartid og forbrug mærkbart, hvis funktionen på et tidspunkt blev slået
til.

---

*Denne vejledning er udarbejdet som led i Aarhus AI's implementering.*