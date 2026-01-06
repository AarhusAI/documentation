---
title: Vi bygger AarhusAI: Teknik, roller og governance
parent: Baggrund
---

# Vi bygger AarhusAI: Teknik, roller og governance

## Infrastruktur og sprogmodeller
En vigtig forudsætning er at sikre kontrol over data. En af drivkrafterne i arbejdet var at øge eget indblik i,
præcis hvor data befinder sig, og hvordan de bliver behandlet – helt ned i infrastrukturen, datacentre m.v.

Vi har derfor undersøgt en række open source-modeller, som kan køres lokalt uden at sende data til 3.-part. Der er
arbejdet på at finde det rigtige kompromis mellem serveromkostninger og modellernes ydeevne, mens man gradvist opbygger
erfaring med systemet, herunder hvor mange brugere systemet kan understøtte, og hvor god svarkvaliteten er.

Til en start er Mistral 3.2 27b valgt som standard-model i Aarhus Kommune på tværs af forskellige use cases,
men der arbejdes ultimo 2025 på at etablere et hybrid-setup, hvor forskellige modeller med forskellige styrker
kan kombineres og tilgodese forskellige behov – fx store, offentlige modeller (GPT 5 via Azure til de
“ufarlige use cases” og vores interne Mistral-model til de use cases, hvor der i højere grad skal være styr på
dataflow og -behandling.

Vi fokuserede tidligt på at afdække muligheder i open source og hos europæiske leverandører – ikke kun til
frontend/backend og øvrige tekniske komponenter, men også GPU-baseret kapacitet til afvikling af sprogmodeller
(inferens).

Computerome – tilknyttet DTU og ejet af staten – blev valgt som partner, fordi det er et af nordens mest sikre
datacentre; derfor giver det en tryg ramme om stabilitet og sikker drift. Det er et veletableret datacenter med
dokumenteret erfaring i håndteringen af sundhedsdata i forskningsregi og dermed et stærkt udgangspunkt for
datasikkerhed, compliance og certificering.

Computerome leverer den fysiske infrastruktur (hardware) mens en fast partnervirksomhed hos Computerome bidrager
med opsætningen af softwareinfrastrukturen (Kubernetes m.v.). Løsningen er bevidst konstrueret, så den kan flyttes
til en anden fysisk infrastruktur, hvis der opstår behov for det. Denne flytbarhed er afgørende for, at vi reducerer
afhængighed af én løsning eller leverandør.

### Svarkvalitet

Forud for AarhusAI har vi i et andet projekt gjort os erfaringer og opnået værdifuld viden omkring svarkvalitet
og evaluering. Det handler om at kunne vurdere, hvornår/i hvilket omfang, vi kan stole på svarene og sikre
dokumentation for, at output er baseret på kildemateriale vi selv har bragt ind i systemet. I det projekt udviklede
vi metoder til at bearbejde og segmentere dokumenter og justere på de mange tekniske komponenter – de “tandhjul”
der driver en RAG-løsning.

## Governance og roller

Det er i Aarhus nødvendigt at arbejde med en decentraliseret struktur, så de enkelte magistratsafdelinger kan
være med til at forme værktøjet til at understøtte netop deres behov. Derfor har vi etableret en ny rolle – de
såkaldte Builders – der er placeret decentralt i magistratsafdelingen og som i samarbejdet med Product Owner
identificerer use cases, best practices og forbedringsforslag til udvidelser af platformen, der er behov for at
sætte i gang centralt. 

Buildernetværket har også fungeret som en måde at validere features. De var de første der blev lukket ind i
prototypen og på den måde fik vi en masse værdifuld feedback fra nogen, der efterfølgende kunne være med til at
udvikle systemet til det bedre.

Herunder følger en overordnet beskrivelse af de roller, der er vurderet centrale for drift,
vedligehold, videreudvikling og udbredelse af AarhusAI. Der er ud over nedenstående
roller også andre kompetencer og ressourcer i spil, eksempelvis decentral GDPR-
rådgivning, slutbrugere, fagpersoner med særlig viden om kildemateriale til en
specialist osv.

### Teknisk Systemejer / Product Owner

Der udpeges en Teknisk Systemejer, der har det formelle ansvar og en Product Owner, der har det operationelle
ansvar. Tilsammen har de to roller ansvaret for vedligehold, support, videreudvikling og støtte til den videre
udbredelse af AarhusAI på tværs af kommunen i samarbejde med Faglige Systemejere. PO’en koordinerer således alle
tiltag rundt om AarhusAI fra teknikken til de organisatoriske aspekter og sikrere, at der arbejdes tilstrækkeligt
tværgående.

PO’en er envidere Aarhus Kommunes snitflade mod OS2ai-koordinationsgruppen og
skal være proaktiv i relation til platformens videreudvikling, dvs. sørge for at opsamle
ønsker eller krav til forbedringer eller nye features fra organisationen og varetage Aarhus
Kommunes praksisnære interesser i relation til OS2ai. De primære snitflader indadtil er
Faglige Systemejere, Builder-communitiet og relevante juridiske ressourcer i organisationen.

PO’en understøtter Builder-communitiet ved at opbygge og vedligeholde best practice
for brugen af AarhusAI og særligt opbygningen af nye specialister og assistenter. PO’en
vedligeholder et overblik over specialister og assistenter, så der skabes bedre grobund
for deling og genbrug på tværs af organisationen. Der er oprettet et dokument der
definerer en række opgaver til Builders i relation til platformen og det er PO’ens opgave,
at vedligeholde denne opgaveliste mens det forventes, at Faglig Systemejer for hver
magistratsafdeling sikrer efterlevelsen hos egne Builders.

PO’en ejer og vedligeholder tværgående processer og snitfladerne til de juridiske
kompetencer, der støtter juridiske afklaringer ifm. screening og risiko- og
konsekvensarbejdet for nye use cases (jfr. kortlægningen af processer ovenfor).

PO’en er endvidere ansvarlig for at udvikle og vedligeholde de testcases, der danner
grundlag for test af nye releases samt at koordinere tests med relevante Builders og
slutbrugere.

PO’en er ansvarlig for at producere krav til brug i udbud af nye systemer, hvor der er
forventning om at skabe en integration til AarhusAI. Det kan f.eks. være i forbindelse
med udbud af et nyt intranetsystem, hvor der bør stilles specifikke krav til hvordan
information lagres og kan tilgås fra AarhusAI, altså hvordan systemet og
informationerne i et nyt Aarhus Intra er ”AI ready” ”by design”.

### Data Scientist

Data Scientist-rollen får kun tiltagende relevans, hvis AarhusAI skal blive en endnu
bedre platform.

Der er bl.a. opgaver omkring tilpasning af GenAI-specifikke komponenter (chunking,
tokenization, embedding, prompting, retrieval m.v.) som der proaktivt og løbende skal
arbejdes med, for at løsningen hele tiden optimeres op imod forretningens behov og dermed
forbliver et velfungerende og relevant alternativ til ChatGPT og Copilot.

Data Scientist-rollen skal desuden arbejde proaktivt med nogle helt centrale områder
for brugen af generativ AI. Det er f.eks. systematisk test og evaluering af svarkvalitet,
præcision og robusthed ift. f.eks. hallucination og RAG performance. Det er hele sikkerhedsområdet
(red teaming, guard railing, AI firewalling m.v.) som skal til for at skabe et trygt og sikkert system. Det er
at definere best practice for klargøring af kildemateriale i RAG-sammenhæng, så Builders og dataleverandører kender
omfang og
opgaver i relation hertil. Og så er det proaktivt at se ind i helt nye muligheder, som
forretningen før eller siden vil efterspørge – eksempelvis agenter.

### Udvikler

Udvikler-rollen dækker primært over backend-relaterede opgaver, herunder løbende
optimering af platformen, evt. Aarhus Kommune-specifik tilpasning og udvikling af
OS2ai samt at afdække krav til og bygge integrationer til fagsystemer gennem f.eks. nye
standarder som MCP og A2A.

### DevOps

Sammen med systemejerskabet følger en række driftsopgaver, som varetages af
DevOps-rollen i samarbejde med driftsleverandøren. Det er release management,
fejlsøgning, driftssupport.

### Faglig Systemejere

Der udpeges en faglig systemejer pr. magistratsafdeling som har ansvaret for den
magistratsafdelings Builders og som sikrer det løbende engagement og prioritering af
arbejdet.

De Faglige Systemejere kobler lokale onboarding-aktiviteter såvel som bredere AI
literacy-aktiviteter til det fælles arbejde, f.eks. ved at sikre, at lokale initiativer,
materialer m.v. deles til inspiration – og indarbejder værdiskabende tiltag fra andre
magistratsafdelinger i egne indsatser.

### Builders

Det eksisterende korps af Builders holdes decentralt og som udgangspunkt til
digitaliseringskyndige personer, der besidder de tekniske og processuelle kompetencer,
der er nødvendig for at skabe brugbare og praksisnære AI specialister – naturligvis i tæt
samarbejde med relevante fagpersoner.

Builders driver alle processer relateret til tilblivelsen af specialister på platformen inkl.
brug af Screeningsværktøjet, risiko- og konsekvensanalyse, tilvejebringelse og
klargøring af relevant kildemateriale, opbygning af specialister i platformen (prompts,
settings), brugertests, formidling samt kontinuerlig kontrol og vedligehold af
magistratsafdelingens specialister.

Builders indgår i det tværgående community, hvor der videndeles og arbejdes sammen
for at konsolidere processer og udviklingsbehov på tværs af organisationen.