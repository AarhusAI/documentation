---
title: Funktioner i OpenWebUI — hvad gør de?
parent: Builder
---

## Vejledning: Funktioner i OpenWebUI — hvad gør de?

Når du opretter eller konfigurerer en assistent i **Workspace → Models**, kan du
aktivere en række funktioner. Denne vejledning forklarer hvad hver funktion gør,
og hvornår det er hensigtsmæssigt at aktivere den.

### Opsummering

|Type|Navn|Anbefaling|Bemærkning|
|-|-|-|-|
|Værktøj|Websearch|Efter behov|Søger på internettet|
|Funktion|Vision|Efter behov|Analyserer uploadede billeder|
|Funktion|Fil Upload|Efter behov|Uploader dokumenter til chatten|
|Funktion|Websøgning|❌ Aldrig|Deaktiveret på serverniveau|
|Funktion|Billedgenerering|❌ Aldrig|Deaktiveret på serverniveau|
|Funktion|Kode Interpreter|❌ Aldrig|Deaktiveret på serverniveau|
|Funktion|Terminal|❌ Aldrig|Ikke installeret — må aldrig aktiveres|
|Funktion|Forbrug|❌ Ikke relevant|Kun nyttigt ved betaling per token til ekstern API|
|Funktion|Citater|✅ Anbefales|Særligt ved brug af vidensbaser|
|Funktion|Statusopdateringer|✅ Anbefales|Viser hvad modellen arbejder på|
|Funktion|Builtin Tools| ✅ Anbefales|Hvis assistentens skal søge i viden skal dette være slået til|

\---

### Værktøjer

Værktøjer er eksterne integrationer der er tilføjet til Aarhus AI af en
administrator. De adskiller sig fra Funktioner ved at de kan kalde eksterne
tjenester.

### Websearch

**Hvad gør det?**
Giver assistenten mulighed for at søge på internettet og hente aktuelle
oplysninger som del af sit svar.

**Hvornår aktiveres det?**
Når assistenten har behov for adgang til aktuelle oplysninger fra internettet.

**Sådan aktiveres det:**

1. Gå til **Workspace → Models** og åbn den assistent du vil redigere
2. Rul ned til sektionen **Værktøjer**
3. Sæt kryds ved **Websearch**
4. Klik **Save**

Websearch er nu tilgængeligt for assistenten og kan kaldes automatisk af
modellen når den vurderer at et søgeresultat vil forbedre svaret.

\---

### Vision

**Hvad gør det?**
Giver assistenten mulighed for at "se" og analysere billeder som brugeren
uploader i chatten. Modellen kan beskrive, fortolke og besvare spørgsmål
om billeder.

**Hvornår aktiveres det?**
Når brugerne har behov for at uploade billeder som del af deres opgave —
f.eks. screenshots, diagrammer eller scannede dokumenter.

\---

### Fil Upload

**Hvad gør det?**
Giver brugeren mulighed for at uploade dokumenter (PDF, Word, tekst m.fl.)
direkte i chatten, som modellen kan læse og besvare spørgsmål om.

**Hvornår aktiveres det?**
Når brugerne skal arbejde med dokumenter — f.eks. vejledninger eller rapporter.

\---

### Websøgning

**Hvad gør det?**
Aktiverer den **indbyggede** websøgningsfunktion i OpenWebUI, som sender
brugerens prompt til en ekstern søgetjeneste for at hente aktuelle
informationer fra internettet.

**Hvornår aktiveres det?
Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau
via `ENABLE\_WEB\_SEARCH=False` og er derfor ikke tilgængelig. Brug i stedet
Websearch-toolet under Værktøjer.

\---

### Billedgenerering

**Hvad gør det?**
Giver modellen mulighed for at generere billeder baseret på tekstbeskrivelser.
Kræver at en billedgenereringstjeneste er konfigureret (f.eks. DALL-E eller
ComfyUI).

**Hvornår aktiveres det?
Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau
via `ENABLE\_IMAGE\_GENERATION=False`, da der ikke er installeret en
billedgenereringstjeneste.

\---

### Kode Interpreter

**Hvad gør det?**
Giver modellen mulighed for at skrive og udføre Python-kode direkte i
chatten — f.eks. til dataanalyse, beregninger eller filbehandling.

**Hvornår aktiveres det?
Anvendes ikke i Aarhus AI.** Funktionen er deaktiveret på serverniveau
via `ENABLE\_CODE\_INTERPRETER=False`.

\---

### Terminal

**Hvad gør det?**
Giver modellen direkte adgang til at udføre kommandoer i serverens terminal
— svarende til shell-adgang på selve serveren.

**Hvornår aktiveres det?
Må aldrig aktiveres.** Terminal er ikke installeret i Aarhus AI og har
ingen effekt selv hvis det afkrydses.

\---

### Forbrug

**Hvad gør det?**
Viser brugeren information om token-forbrug for den aktuelle samtale —
dvs. hvor meget af modellens kapacitet der er brugt.

**Hvornår aktiveres det?**
Ikke relevant i Aarhus AI. Forbrug er primært nyttigt når man betaler per
token til en ekstern API — da modellen kører lokalt er der ingen direkte
omkostning at overvåge for brugeren.

\---

### Citater

**Hvad gør det?**
Når modellen besvarer spørgsmål baseret på uploadede dokumenter eller
vidensbase, vises kildehenvisninger — dvs. hvilken del af hvilket dokument
svaret er baseret på.

**Hvornår aktiveres det?**
Anbefales aktiveret når assistenten bruger vidensbaser eller dokumenter,
da det giver brugeren mulighed for at verificere svarene.

\---

### Statusopdateringer

**Hvad gør det?**
Viser løbende statusbeskeder mens modellen arbejder — f.eks. "søger i
vidensbase", "genererer svar". Giver brugeren feedback om hvad der sker
i baggrunden.

**Hvornår aktiveres det?**
Anbefales aktiveret for en bedre brugeroplevelse, særligt når assistenten
bruger tools eller vidensbaser.

\---

### Builtin Tools

**Hvad gør det?**
Aktiverer OpenWebUI's indbyggede systemværktøjer som modellen kan kalde
selvstændigt — herunder adgang til vidensbaser, kanaler og agentic research.

**Hvornår aktiveres det?**
Native Mode er aktiveret i Aarhus AI, så Builtin Tools er funktionelt.
Det anbefales kun aktiveret hvis du har sat dig ind i hvad det betyder og
hvilke værktøjer der injiceres — da modellen selvstændigt beslutter hvornår
den kalder dem. Hukommelse og noter er deaktiveret på serverniveau via
`ENABLE\_MEMORIES=False` og `ENABLE\_NOTES=False` og er ikke tilgængelige.

\---

*Denne vejledning er udarbejdet som led i Aarhus AI's implementering.*
