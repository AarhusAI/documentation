---
title: Systempromt
parent: Builder
---

# Systempromt

Systemprompten sætter scenen for specialisten/assistenten. Med den fortæller man hvordan specialisten skal opføre sig,
hvilket sprog der skal svares på, tone i svaret og meget meget mere. Det er helt afgørende for en specialists succes
at systempromten er lavet rigtigt. Det er svært, og det ændrer sig også over tid. En systempromt der virkede perfekt
i sidste release kan give en ny opførsel efter næste release fordi at fortolkningen kan forandre sig.

Når man som builder skal bygge en systemprompt har vi en række anbefalinger. Vi anbefaler at der anvendes en anden og
større sprogmodel (Claude, ChatGPT, Co-Pilot mv.) til at opbygge en systempromt. Den større sprogmodel skal promptes med
at den skal være ”prompt engineer”, den skal lave en systemprompt til Mistral Small 24b med et kontekstvindue på 8192
tokens.

En systemprompt skal sammen med spørgsmålet, relevant viden, og svaret kunne være i modellens kontekstvindue. Du kan
læse mere om kontekstvindue her [indsæt link](https://www.dr.dk).
