---
title: Viden
parent: Builder
---

# Hvornår er en use case egnet til en AI-assistent?

En sprogmodel (LLM) er designet til at generere sandsynlig tekst — ikke til at
gengive kildemateriale 100 % ordret. Selv med korrekt opsætning (gode Skills,
en velfungerende videndatabase, en gennemtestet systemprompt) er der altid en
risiko for, at modellen omformulerer, forkorter eller "udfylder" manglende
information på en måde, der lyder korrekt, men ikke er det.

Brug denne vejledning tidligt i arbejdsgangen (trin 1: *Drøft use casen med
forretningen*) til at vurdere, om en use case egner sig til en AI-assistent —
og hvordan den i så fald bør bygges.

## Tommelfingerregel

> Spørg: **"Hvad sker der, hvis svaret er 95 % rigtigt, men de sidste 5 % er
> forkerte?"**

| Konsekvens af en fejl | Anbefaling |
|---|---|
| Lidt irriterende — brugeren tjekker selv videre | En AI-assistent er velegnet |
| Juridisk problem, forkert udbetaling, brud på GDPR | Kræver særlig opsætning (se nedenfor) eller menneskelig kontrol — undgå evt. helt som en ren AI-opgave |

## Det en LLM er god til

- **Forklare og fortolke** — omsætte komplekst indhold (juridisk tekst,
  tekniske specifikationer, lange dokumenter) til forståeligt sprog for en
  almindelig medarbejder. Her er "cirka rigtig, men letforståelig" ofte mere
  værd end "ordret, men uforståelig".
- **Navigere og henvise** — pege brugeren hen mod det rette dokument, den
  rette afdeling eller det rette link, uden selv at skulle levere den
  præcise tekst.
- **Strukturere og opsummere** — tage et rodet eller langt dokument og give
  et overblik, hvor mindre unøjagtigheder i detaljer ikke ændrer den
  overordnede forståelse.
- **Levere et første udkast** — et udkast til tekst eller svar, som et
  menneske derefter kvalitetssikrer, frem for et endeligt,
  afsendelsesklart produkt.
- **Håndtere variation i sprog** — forstå at "ferieansøgning",
  "ansøg om ferie" og "hvordan får jeg fri" betyder det samme, og guide
  brugeren rigtigt uafhængigt af det præcise ordvalg.

## Det en LLM er dårlig til

- **Ordret gengivelse af kildetekst** — juridiske klausuler,
  GDPR-formuleringer, citater, eller tal der skal være 100 % præcise.
- **Beregninger og datalogik** — komplekse udregninger eller andet der
  kræver deterministisk logik bør løses med kode/regneark, ikke en
  sprogmodel.
- **Autoritative, bindende afgørelser** — juridisk rådgivning,
  personalesager, eller andet hvor en fejl har reelle konsekvenser, skal
  altid ende hos et menneske med ansvar og kompetence.
- **Sjældne/kritiske engangsopslag** — hvis noget kun skal slås op én gang,
  men skal være 100 % korrekt (fx et specifikt lovparagraf-nummer), er et
  direkte link til den autoritative kilde ofte bedre end en
  LLM-formulering.

## Hvis use casen kræver ordret præcision

Nogle use cases (fx GDPR-tekst i skrivelser) kræver alligevel præcise,
ordrette formuleringer. Her er risikoen for hallucination markant højere end
ved almindelige spørgsmål. Reducér risikoen sådan:

- **Brug Skill, ikke RAG, til korte ordrette tekster.** Er den præcise
  formulering kort nok (et par afsnit eller mindre), bør den ligge som et
  Skill i stedet for i videndatabasen. Et Skill indlæses i sin helhed uden
  chunking, så teksten forbliver intakt.
- **Brug Full Context frem for Focused Retrieval til længere ordrette
  dokumenter.** Er dokumentet for langt til et Skill, men kræver stadig
  ordret gengivelse, brug Full Context for netop den fil. Full Context
  injicerer hele dokumentet ordret ved hver besked uden chunking eller
  semantisk søgning — på bekostning af at det fylder mere i
  kontekstvinduet.
- **Tilføj en eksplicit anti-hallucination-regel i systemprompten**, fx:

  > Skal du gengive en ordret formulering (fx en GDPR-klausul), og du ikke
  > kan finde den fulde, præcise ordlyd i det modtagne materiale, må du
  > IKKE selv formulere eller rekonstruere teksten. Sig i stedet klart, at
  > den præcise formulering ikke kunne findes, og at brugeren bør
  > verificere teksten manuelt eller kontakte [relevant fagperson/afdeling].

- **Test specifikt for denne risiko.** Stil et spørgsmål, hvor det
  tilhørende Skill/dokument bevidst er utilgængeligt (formuleret som om det
  var dækket), og bekræft at assistenten korrekt afviser i stedet for at
  hallucinere en plausibel-lydende formulering.

## Konklusion

En AI-assistent kan reducere risikoen for fejl gennem god prompt- og
kildeopsætning, men kan **aldrig** give en 100 % garanti for nøjagtighed.
Byg derfor altid menneskelig kontrol ind som en fast del af arbejdsgangen,
når output bruges i formelle eller juridisk bindende sammenhænge.
