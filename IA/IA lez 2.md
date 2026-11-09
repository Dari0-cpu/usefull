# Lezione AI: Risoluzione di Problemi e Ricerca (Problem Solving)

> [!tip] 🎓 Consigli del Professore per gli Esercizi e i Progetti
> 
> Il professore ha dato indicazioni molto precise su come affrontare gli esercizi pratici e la scrittura del codice:
> 
> - **Uso dell'Intelligenza Artificiale:** Usare l'AI (come ChatGPT o Gemini) _non è sbagliato_, ma è fondamentale il **come** la si usa. Invece di chiederle di risolvere direttamente il problema scrivendo il codice al posto tuo, usala come un tutor: chiedile _come_ affronterebbe il problema, scrivi tu una tua bozza di soluzione, e poi chiedi all'AI di correggerla e ottimizzarla.
>     
> - **Importanza della Documentazione:** Non basta saper scrivere un buon codice; la **documentazione è fondamentale**. Il lavoro deve essere riccamente commentato. Oltre al codice nudo e crudo, è fortemente richiesto allegare un documento Word di spiegazione, oppure sviluppare il progetto all'interno di un **Jupyter Notebook** (che unisce testo spiegato e codice), o in alternativa inserire commenti molto corposi e descrittivi direttamente nei file sorgente.
>     

## 1. Introduzione: Il Paradigma del Problem Solving

Nel campo dell'Intelligenza Artificiale, poiché esistono molteplici soluzioni per un dato problema, possiamo vedere la costruzione della soluzione come un **avvicinamento per passi** (step-by-step). Ogni passo rappresenta una possibile evoluzione dello stato del nostro problema.

Questo approccio adotta il paradigma della **risoluzione di problemi come ricerca in uno spazio di stati** (Problem Solving come Ricerca).

- _Esempio classico:_ Risolvere una partita a scacchi, teoricamente, è puro problem solving mediante ricerca in un grafo di mosse possibili.
    
- Cercare nei grafi significa trovare la sequenza ottimale per raggiungere la soluzione.
    

### 1.1 Tipologia di Agenti

Questo paradigma è adottato da agenti specifici:

- **Agenti con modello:** Utilizzano una rappresentazione **atomica** dello stato (uno stato è considerato come una scatola nera indivisibile, non ci interessa la sua struttura interna ai fini della ricerca, ma solo come si transita da uno stato all'altro).
    
- **Agenti basati su obiettivo (Goal-based):** Pianificano l'intera sequenza di mosse _prima_ di agire fisicamente nell'ambiente.
    

## 2. Il Processo di Risoluzione dell'Agente

Quando interroghiamo un'AI, stiamo interrogando un agente che cerca la risposta migliore. Il processo logico che segue l'agente si divide in fasi ben distinte:

1. **Determinazione dell'obiettivo (Goal Formulation):** Identificare una serie di condizioni nel mondo reale per cui il problema si considera risolto. Esistono stati nel programma che fungono da "traguardo".
    
2. **Formulazione del Problema (Problem Formulation):** Definire in modo formale:
    
    - La _rappresentazione degli stati_.
        
    - La _rappresentazione delle azioni_ (come passare da una situazione all'altra).
        
3. **Ricerca della Soluzione (Search):** Determinare la soluzione navigando nello spazio degli stati. L'agente esplora le possibilità cercando un cammino dallo stato iniziale (`start`) allo stato obiettivo (`goal`).
    
4. **Esecuzione del Piano (Execution):** Una volta trovata la soluzione teorica, l'agente esegue le azioni.
    

> [!warning] Concetto Chiave
> 
> È fondamentale **separare la fase di ricerca (soluzione del piano) dalla fase di esecuzione**. L'agente prima "pensa" (ricerca) e solo dopo aver trovato il cammino completo "agisce" (esegue).

## 3. Assunzioni sull'Ambiente

Per poter formulare un problema in questo modo, dobbiamo fare delle assunzioni precise sull'ambiente in cui l'agente si muove. In genere, assumiamo che l'ambiente sia:

- **Statico:** L'ambiente non cambia mentre l'agente sta "pensando" o calcolando la mossa.
    
- **Osservabile:** L'agente ha accesso completo e perfetto allo stato attuale dell'ambiente.
    
- **Discreto:** L'insieme degli stati e delle azioni possibili è numerabile e finito.
    
- **Deterministico:** Ogni azione ha un solo esito certo, senza probabilità o imprevisti.
    

> [!info] La metafora degli "Occhi chiusi"
> 
> Poiché l'ambiente è deterministico, osservabile e statico, si assume che l'agente possa eseguire il suo piano **"ad occhi chiusi"** (approccio _Open-loop_). Avendo pianificato tutto in anticipo e sapendo che niente può andare storto o cambiare a sua insaputa, l'agente sa esattamente in quale stato si troverà dopo ogni azione, senza bisogno di guardare la mappa durante l'esecuzione.

## 4. Formalizzazione Matematica di un Problema

Ogni volta che si implementa un algoritmo di ricerca (es. Dijkstra, A*), il problema **deve** essere formalizzato definendo rigorosamente questi **5 elementi fondamentali**:

1. **Stato Iniziale ($s_0$):** Lo stato di partenza dell'agente.
    
2. **Azioni Possibili:** Dato uno stato $s$, una funzione $Azioni(s)$ restituisce l'insieme di azioni legali eseguibili in quello stato.
    
3. **Modello di Transizione:** Descrive cosa fa ciascuna azione. È definito tramite la funzione:
    
    $$Risultato(s, a) = s'$$
    
    Dove $s$ è lo stato corrente, $a$ l'azione e $s'$ è lo stato successore.
    
4. **Test Obiettivo (Goal-Test):** Un test che verifica se un dato stato è un traguardo. Serve a dimostrare che l'algoritmo ha raggiunto lo scopo. Può essere formalizzato come una funzione booleana:
    
    $$GoalTest(s) \rightarrow \{True, False\}$$
    
5. **Costo del Cammino:** La somma dei costi delle singole azioni. Il costo di un singolo passo è definito da una funzione $c(s, a, s')$.
    
    - _Assunzione fondamentale:_ Il costo di un'azione non è mai negativo. $\rightarrow c(s, a, s') \ge 0$.
        

> [!quote] Definizione in logica: Il Predicato
> 
> _Un predicato è un'espressione logica (o una funzione) che afferma una proprietà o una relazione e restituisce come risultato un valore di verità: **Vero (True)** o **Falso (False)**. Ad esempio, nel Goal-Test, `Essere_a_Bucarest(s)` è un predicato logico che risulta Vero solo se lo stato $s$ corrisponde alla città di Bucarest, altrimenti è Falso._

## 5. Algoritmi di Ricerca e Costi

Gli algoritmi di ricerca sono programmi che prendono **in input un problema formalizzato** (i 5 punti sopra) e restituiscono **in output un cammino soluzione** (una sequenza di azioni dallo stato iniziale al goal).

L'efficienza di un agente si misura valutando il costo totale:

$$Costo\ Totale = Costo\ della\ Ricerca\ (tempo/memoria) + Costo\ del\ Cammino\ (peso\ delle\ azioni)$$

## 6. Esempi di Formulazione di Problemi

### Esempio 1: Itinerario Stradale (La mappa della Romania)

_Classico esempio del libro Russell & Norvig per spiegare i grafi e il pathfinding._

- **Stati:** Le singole città. Es: `In(Pitesti)`
    
- **Stato Iniziale:** `In(Arad)`
    
- **Azioni:** Spostarsi verso una città collegata. Es in Arad: $\{Go(Sibiu), Go(Zerind), Go(Timisoara)\}$
    
- **Modello di transizione:** $Risultato(\ In(Arad),\ Go(Sibiu)\ ) = In(Sibiu)$
    
- **Test Obiettivo:** Essere nella città di destinazione. Es: $s == In(Bucarest)$
    

### Esempio 2: Il Problema delle 8 Regine

_Posizionare 8 regine su una scacchiera senza che si attacchino a vicenda._

- **Stati:** Qualsiasi disposizione da 0 a 8 regine sulla scacchiera.
    
- **Stato Iniziale:** Scacchiera vuota (0 regine).
    
- **Azioni:** Aggiungere una regina in una casella vuota (spesso limitato a caselle non sotto attacco).
    
- **Modello di transizione:** Restituisce una scacchiera con la nuova regina aggiunta.
    
- **Test Obiettivo:** Ci sono 8 regine sulla scacchiera e nessuna è sotto attacco (nessuna condivide la stessa riga, colonna o diagonale).
    

### Esempio 3: Dimostrazione di Teoremi (Logica)

- **Stati:** Insiemi di formule proposizionali.
    
- **Stato Iniziale:** L'insieme delle formule proposizionali di base (gli assiomi o le premesse).
    
- **Azioni:** L'applicazione di regole di inferenza (es. _Modus Ponens_) alle formule attuali per generarne di nuove.
    
- **Modello di transizione:** Aggiunge la nuova formula logica generata all'insieme di formule dello stato precedente.
    
- **Test Obiettivo:** Verificare se l'insieme di formule contiene la formula proposizionale che rappresenta il teorema da dimostrare.

---

