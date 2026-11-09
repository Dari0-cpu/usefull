## Agenti Intelligenti e Razionalità

L'Intelligenza Artificiale si occupa di osservare e comprendere le attività legate al comportamento intelligente per poterne riprodurre le logiche e le funzionalità in modo accurato.

- **Interazione Agente-Ambiente:** Un agente situato riceve informazioni dall'ambiente tramite i propri sensori (generando una sequenza percettiva) e agisce su di esso tramite gli attuatori.
    
- **Agente Razionale:** Un agente si definisce razionale quando, per ogni sequenza di percezioni, compie l'azione che massimizza il valore atteso della misura delle prestazioni, tenendo conto delle percezioni passate e della sua conoscenza pregressa.
    
- **Limiti della Razionalità:** La razionalità non richiede onniscienza (capacità predittive perfette) né onnipotenza (le capacità operative dell'agente possono essere limitate).
    
- **LLM e Simulazione:** Modelli come ChatGPT applicano pattern statistici per generare output testuali coerenti in risposta a un prompt. La valutazione della loro prestazione si basa sulla naturalezza e sull'efficacia della risposta, simulando l'empatia senza provare una reale emozione.
    
> [!question] Emozione vera o simulata?
> Al momento non possiamo affermare che l'agente provi una *reale* emozione. L'IA sta semplicemente applicando pattern statistici per mappare un input emotivo a un output che noi umani codifichiamo come emotivamente coerente.


## Il Framework PEAS e Classificazione degli Ambienti

Definire operativamente il problema per un agente significa caratterizzare l'ambiente in cui opera attraverso la codifica PEAS, dove l'agente razionale rappresenta la soluzione progettuale.

- **P (Performance):** La misura oggettiva del successo desiderato. Nel caso di un taxi autonomo: arrivare in modo sicuro e veloce massimizzando i profitti.
    
- **E (Environment):** L'ambiente in cui l'agente si muove. Per il taxi: strade, pedoni, veicoli.
    
- **A (Actuators):** I meccanismi utilizzati per agire. Per il taxi: sterzo, acceleratore, freni.
    
- **S (Sensors):** I meccanismi per acquisire percezioni. Per il taxi: telecamere, GPS, tachimetro.
    

L'ambiente determina direttamente la complessità algoritmica e architetturale dell'agente attraverso diverse dicotomie:

> [!info] Indipendenza Architetturale
> L'ambiente e gli agenti sono **entità indipendenti**, funzionano in modo analogo ai *Processi* e ai *Threads* all'interno di un Sistema Operativo.


- **Completamente vs Parzialmente Osservabile:** L'apparato percettivo fornisce una conoscenza totale dello stato necessario (es. Scacchi) oppure parziale a causa di limiti sensoriali (es. Guida Autonoma).
    
- **Singolo vs Multi-Agente:** Può includere entità singole o altri agenti con comportamenti competitivi o cooperativi.
    
- **Deterministico vs Stocastico vs Non-deterministico:** Deterministico se lo stato successivo è dettato unicamente dallo stato corrente e dall'azione; stocastico se c'è incertezza modellabile tramite probabilità; non-deterministico se le evoluzioni sono del tutto equiprobabili o imprevedibili.
    
- **Episodico vs Sequenziale:** Episodico se l'esperienza è divisa in task atomici e indipendenti; sequenziale se ogni decisione presente influenza profondamente gli stati futuri.
    
- **Statico vs Dinamico:** Statico se non cambia mentre l'agente delibera; dinamico se i cambiamenti avvengono durante i tempi di calcolo; semi-dinamico se l'ambiente non cambia ma la misura di prestazione (es. scacchi con timer) sì.
    
- **Discreto vs Continuo:** Riferito alla natura di stati, percezioni, azioni o del tempo (es. scacchiera a stati finiti contro la guida continua di un veicolo).
    
- **Noto vs Ignoto:** Stabilisce se l'agente conosce a priori le "leggi fisiche" del dominio o se deve esplorarle per apprenderle.
    

## Struttura e Tipologie di Agenti

Un agente è sempre formato dall'unione di un'**Architettura** fisica/software e di un **Programma** logico che implementa la funzione di mappatura dalle percezioni alle azioni. L'ambiente e l'agente operano come entità indipendenti, in modo analogo alla distinzione tra kernel, processi e thread all'interno di un Sistema Operativo.

Le tipologie fondamentali per lo sviluppo di agenti:

- **Agente basato su Tabella:** Associa rigidamente un'azione a ogni possibile sequenza di percezioni, ma risulta del tutto impraticabile per limiti di memoria e assenza di generalizzazione.
    
- **Agente Reattivo Semplice:** Ignora l'intero storico percettivo e agisce solo sull'input corrente sfruttando regole condizione-azione; funziona solo in ambienti completamente osservabili.
    
- **Agente Basato su Modello:** Mantiene una memoria o descrizione dello stato interno basata sulle regole di evoluzione del mondo, risultando vitale negli ambienti parzialmente osservabili.
    
- **Agente con Obiettivo (Goal-based):** Proietta e pianifica sequenze di azioni valutando scenari futuri per raggiungere uno scopo specifico definito a monte.
    
- **Agente con Valutazione di Utilità:** Mappa gli stati su numeri reali tramite una funzione di utilità per dirimere i trade-off tra obiettivi alternativi, mirando a massimizzare l'utilità attesa (tenendo conto del fattore probabilità).
    
- **Agente che Apprende:** Adatta il programma nel tempo tramite un elemento critico che fornisce feedback, un elemento di apprendimento e un generatore di problemi che stimola l'esplorazione di nuovi dati.
    
- **Agenti Antagonisti (Ambienti Ostili):** Negli scenari multi-agente competitivi, l'incertezza deriva dalle scelte di un avversario che punta a minimizzare la nostra metrica P. Si modellano con alberi decisionali della Teoria dei Giochi (es. algoritmica Minimax) impiegando ottimizzazioni come la potatura Alpha-Beta per tagliare i rami ininfluenti.
