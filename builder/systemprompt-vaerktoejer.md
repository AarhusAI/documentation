---
title: Systemprompt
parent: Builder
---

# Systemprompt og værktøjer: hvad skal du huske?

Denne side beskriver, hvad du skal skrive i din systemprompt, når din assistent skal bruge videndatabasen (RAG),
websøgning, skills eller office-værktøjet.

Kort fortalt: I AarhusAI er det modellen selv, der beslutter, om den vil kalde et værktøj. Lokale modeller som Gemma
kalder sjældent et værktøj af sig selv. Derfor skal systemprompten sige præcist, hvilket værktøj der skal kaldes, og
hvornår.

## Før du skriver prompten: tjek indstillingerne

Selv den bedste prompt virker ikke, hvis indstillingerne i model-editoren er forkerte.

- **Function Calling** skal stå på "Standard". Det er den rigtige indstilling i AarhusAI, så du skal ikke ændre den.
- **Builtin Tools** skal være slået til, når assistenten bruger en videndatabase eller skills. Uden Builtin Tools
  forsvinder både skill-kald og søgning i videndatabasen.
- **Slå ubrugte Builtin Tools-kategorier fra.** Hver aktiv kategori fylder i kontekstvinduet og giver modellen flere
  værktøjer at vælge forkert imellem. Har assistenten ingen videndatabase, så slå kategorien Knowledge Base fra.
- **Del skills og videndatabaser** med de samme grupper som assistenten. Deling af assistenten deler ikke de skills og
  videndatabaser, den bruger.

## Værktøjsnavne

Skriv altid værktøjets præcise navn i prompten. Navnene i AarhusAI er:

- **Videndatabasen (RAG):** `query_knowledge_files`
- **Websøgning:** `websearch_search_web`
- **Office-værktøjet:** værktøjer der starter med `office_` (i beta og pt. kun tilgængeligt på stg)
- **Skills:** `view_skill` sammen med skillens id, fx `eksempel-skill`

Eksterne forbindelser får forbindelsens ID som præfiks. Forbindelsen "websearch" giver derfor værktøjet
`websearch_search_web`. Er du i tvivl om et navn, så stil assistenten et spørgsmål og klik på "View Result" eller
"Explored" under svaret. Her kan du se, hvilke værktøjer der blev kaldt.

## Generelle principper for alle værktøjer

1. **Udløs værktøjet ud fra spørgsmålets type.** Skriv ikke "Brug værktøjet, hvis du ikke ved det". Modellen opdager
   sjældent selv, at den mangler viden. Skriv i stedet konkrete emner eller typer af spørgsmål.
2. **Placér værktøjsreglerne øverst** i prompten i en fast rækkefølge, og gentag dem kort i en "Husk:"-linje til sidst.
3. **Brug præcise navne**, både for værktøjer og skills.
4. **Skriv, hvornår værktøjet IKKE skal bruges**, fx ved korte skriveopgaver i chatten.
5. **Beskriv, hvad der skal ske efter kaldet:** Svar kun ud fra resultatet, angiv kilden, og brug én fast sætning, når
   intet findes.
6. **Undgå konkurrerende regler.** Ord som "kun" og "altid" i andre regler kan trumfe et værktøj eller en skill. Brug
   "desuden" og "uanset om" frem for "kun" og "i stedet".
7. **Brug korte eksempler med værktøjskald.** Eksempler styrer modellen stærkere end regler.
8. **Hold prompten kort.** Få regler og korte, direkte sætninger. Hver ekstra regel svækker de andre.

## Skills

Modellen ser navn og beskrivelse af de tilgængelige skills, men henter sjældent en skill af sig selv. Skriv derfor
skillens id og bed eksplicit om kaldet.

```markdown
SKILLS: Ved spørgsmål om [emne], så kald view_skill med [skill-id] og følg skillens instruktioner, før du svarer.
```

Eksempel:

```markdown
SKILLS: Ved spørgsmål om ferie og fridage, så kald view_skill med eksempel-skill og følg skillens instruktioner, før du svarer.
```

Husk også, at skillens beskrivelse skal være kort og sige præcist, hvornår den skal bruges.

## Videndatabasen (RAG)

Videndatabasen bliver ikke automatisk sat ind i samtalen. Modellen skal selv kalde `query_knowledge_files`. Brug ét
begreb for videndatabasen i hele prompten, og nævn gerne det engelske "knowledge base" én gang.

```markdown
VIDENDATABASEN (knowledge base, værktøjet query_knowledge_files): Kald query_knowledge_files, før du svarer på spørgsmål om:
- [emne 1]
- [emne 2]
- [emne 3]
Søg gerne to gange: først med nøgleord, derefter med en alternativ formulering.
Finder du intet relevant, så skriv: "Jeg kunne ikke finde et svar i videndatabasen."
```

Bruger assistenten både skills og videndatabase, så skriv eksplicit, at begge kilder skal bruges sammen:

```markdown
Uanset om du har brugt en skill, så kald desuden query_knowledge_files, når spørgsmålet handler om [emne].
```

Undgå formuleringer som "Hvis ingen skill passer, så søg i stedet i videndatabasen". Modellen læser det som enten/eller
og mister dermed indhold fra den ene kilde.

## Websøgning

Brug websøgning til fakta, der kan ændre sig, eller som modellen ikke kan vide sikkert.

```markdown
WEBSØGNING (websearch_search_web): Kald ALTID websearch_search_web, før du svarer på spørgsmål om:
- personer og hvem der har en post
- navne på organisationer, steder og produkter
- tal, datoer, priser, frister, love og regler
- nyheder og aktuelle forhold
- alt med "nu", "gældende", "i år" eller "seneste"
Søg IKKE ved korte skriveopgaver som mails og omformulering.
Svar kun ud fra søgeresultatet, og skriv kilden.
Finder du intet, så skriv: "Jeg kunne ikke finde et pålideligt svar."
```

Bruger assistenten også en videndatabase, så skriv, hvilken kilde der kommer først. Typisk skal videndatabasen bruges
ved spørgsmål om kommunens egne forhold, og websøgning ved alt andet.

## Office-værktøjet

Office-værktøjet kan lave og læse Word-, Excel- og PowerPoint-filer.

> **Bemærk:** Office-værktøjet er i beta og er pt. kun tilgængeligt på stg. Det kan derfor ikke bruges i assistenter i
> produktion endnu.

```markdown
OFFICE (værktøjer der starter med office_): Kald office-værktøjet, når brugeren:
- beder om en fil, fx et Word-dokument, et Excel-ark eller en PowerPoint
- uploader en Word-, Excel- eller PowerPoint-fil og spørger til indholdet
```

Kender du de præcise navne på office-værktøjerne, så skriv dem direkte, fx `office_create_document`.

## Samlet skabelon

Brug skabelonen som udgangspunkt, og fjern de dele, din assistent ikke bruger.

```markdown
Dato: {{CURRENT_DATE}}

Du er [navn], en assistent for [målgruppe] i Aarhus Kommune. Svar altid på dansk i en professionel og venlig tone.

VIGTIGST: Din egen viden kan være forældet eller forkert. Kald dine værktøjer, før du svarer. Gæt aldrig.

1. SKILLS: Ved spørgsmål om [emne], så kald view_skill med [skill-id] og følg skillens instruktioner.
2. VIDENDATABASEN (knowledge base, query_knowledge_files): Uanset om du har brugt en skill, så kald desuden query_knowledge_files ved spørgsmål om [emner].
3. WEBSØGNING (websearch_search_web): Kald ALTID ved spørgsmål om personer, tal, datoer, love og aktuelle forhold, som videndatabasen ikke svarer på.
4. OFFICE (værktøjer der starter med office_): Kald når brugeren beder om en Word-, Excel- eller PowerPoint-fil.

Brug IKKE værktøjer ved korte skriveopgaver i chatten.

Når du har brugt et værktøj:
- Svar kun ud fra resultatet.
- Skriv kilden.
- Finder du intet, så skriv: "Jeg kunne ikke finde et pålideligt svar."

[Øvrige regler for rolle, tone og format]

Eksempler:
Bruger: [typisk spørgsmål om dit emne]
Du: [kalder query_knowledge_files med "nøgleord"] [svar]. Kilde: [dokumentnavn]

Bruger: Hvem er borgmester i Aarhus?
Du: [kalder websearch_search_web med "borgmester Aarhus Kommune"] Borgmesteren er [navn]. Kilde: aarhus.dk

Husk: Skill først. [Emner] = query_knowledge_files. Faktaspørgsmål = websearch_search_web. Fil = office.
```

## Test og fejlsøgning

- **Lav en testliste** med 15-20 spørgsmål. Tag både spørgsmål med, der skal udløse et værktøj, og spørgsmål, der ikke
  skal.
- **Test i nye chats.** Har assistenten allerede givet et forkert svar tidligere i samtalen, holder den ofte fast i det.
- **Test som almindelig bruger**, ikke som Builder eller administrator. Ellers opdager du ikke manglende deling.
- **Tjek "View Result" eller "Explored"** under svaret for at se, hvilke værktøjer der faktisk blev kaldt.
- **Test en skill med $.** Vælg skillen manuelt med $ i chatten. Virker den med $ men ikke uden, er det indlæsningen
  (prompten eller skillens beskrivelse), der fejler, ikke adgangen.
- **Kør testlisten igen** efter hver ændring af prompten.
