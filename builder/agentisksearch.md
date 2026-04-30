---
title: Agentisk søgeværktøj
layout: default
nav_order: 1
---

## Hvad betyder "agentisk"?

Når vi siger, at et søgeværktøj er **agentisk**, betyder det, at AI-modellen
selv beslutter, hvornår og hvordan den skal søge – uden at du behøver at bede
den om det eksplicit hver gang.

En almindelig (ikke-agentisk) søgefunktion virker sådan: du beder modellen om
at søge, og den søger. Punktum.

Et **agentisk søgeværktøj** virker anderledes:

- Modellen vurderer selv, om den har brug for opdateret information for at
  besvare dit spørgsmål
- Den formulerer selv en søgeforespørgsel og udfører søgningen
- Den vurderer resultaterne og beslutter, om den skal søge igen med en ny
  forespørgsel
- Til sidst sammenfatter den svarene og svarer dig

Kort sagt: modellen **handler selvstændigt** som en agent – deraf ordet
"agentisk". Du stiller et spørgsmål, og modellen tager selv de nødvendige
skridt for at finde et godt svar.

### Eksempel

> **Du skriver:** "Hvad er de seneste nyheder om AI-regulering i EU?"
>
> En agentisk model vil automatisk:
>
> 1. Erkende at den ikke har opdateret viden om dette
> 2. Formulere en søgeforespørgsel (f.eks. *"EU AI Act 2025 nyheder"*)
> 3. Gennemse resultaterne
> 4. Evt. søge igen med et mere præcist udtryk
> 5. Give dig et samlet, opdateret svar med kildehenvisninger

---

## Sådan tilføjer du søgeværktøjet til en specialist/assistent

Følg disse trin for at aktivere det agentiske søgeværktøj på en model i
Open WebUI.

### Trin 1: Åbn modellisten

Gå til **Workspace** i venstre menu og vælg **Models** (Modeller). Her ser du
en oversigt over dine tilgængelige specialister/assistenter.

---

### Trin 2: Vælg eller opret en specialist

- Klik på den specialist/assistent, du vil redigere, for at åbne dens
  indstillinger.
- Eller opret en ny ved at klikke på **+ New Model** / **+ Ny model**.

---

### Trin 3: Åbn fanen "Tools" (Værktøjer)

Inde i modelindstillingerne finder du en række faner øverst. Klik på fanen
**Tools** eller **Værktøjer**.

---

### Trin 4: Aktivér søgeværktøjet

Du vil se en liste over tilgængelige værktøjer. Find søgeværktøjet – det
hedder typisk noget i stil med:

- `web_search`
- `Search`
- eller det navn, I har givet jeres eget søgeværktøj

Sæt flueben ud for søgeværktøjet for at aktivere det på specialisten.

---

### Trin 5: Gem ændringerne

Klik på **Save** / **Gem** nederst på siden. Specialisten er nu konfigureret
til at bruge søgeværktøjet agentisk.

---

### Trin 6: Test det

Gå tilbage til chatten og vælg din specialist. Stil et spørgsmål, der kræver
opdateret viden – fx om aktuelle begivenheder. Du bør nu se, at modellen
automatisk udfører en søgning og inkluderer resultater i sit svar.

> **Tip:** Mange modeller viser en lille indikator eller tekst som
> *"Searching…"* / *"Søger…"* mens de bruger søgeværktøjet, så du kan følge
> med i processen.

---

## Hvornår bruger modellen søgeværktøjet?

Modellen beslutter selv, hvornår søgning er relevant. Den vil typisk søge,
når:

- Spørgsmålet handler om aktuelle begivenheder eller nyheder
- Den har brug for faktuelle oplysninger, der kan have ændret sig siden dens
  træning
- Du eksplicit beder den om at søge eller finde frem til noget

Den vil **ikke** søge unødvendigt, f.eks. ved simple matematikspørgsmål eller
generelle forklaringer, som den allerede kender svaret på.

---

## Fejlfinding

| Problem | Mulig løsning |
| --- | --- |
| Modellen søger ikke automatisk | Tjek at søgeværktøjet er aktiveret under Tools for den pågældende specialist |
| Søgningen giver ingen resultater | Kontrollér at søgeværktøjets API-nøgle eller forbindelse er korrekt konfigureret i Open WebUI |
| Modellen ignorerer søgeresultater | Prøv at præcisere i systemprompten, at modellen aktivt skal bruge søgeværktøjet |

---

Guide udarbejdet til internt brug – Open WebUI med agentisk søgeværktøj