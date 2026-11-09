# Il labirinto del cavaliere

> [!abstract] In una riga Il codice del gioco è già scritto. Il nostro compito è implementare **l'algoritmo di ricerca del percorso** che guida il cavaliere dal punto di partenza al tesoro, evitando il drago. Per i livelli 1 e 2 l'algoritmo base è la **BFS**.

---

## 1. Le regole del gioco

|Elemento|Cosa fa|
|---|---|
|**Cavaliere**|È l'agente che dobbiamo programmare. **Vince** se arriva al tesoro.|
|**Drago**|**Vince** se raggiunge il cavaliere.|
|**Tesoro**|È l'obiettivo del cavaliere.|
|**Limite di passi**|Il cavaliere **perde** anche se arriva a **200 passi** senza aver raggiunto il tesoro.|

Quindi le condizioni di sconfitta sono due: essere raggiunti dal drago, oppure finire i passi. Un percorso corretto ma troppo lungo, o che passa vicino al drago, non basta.

![[Pasted image 20261009114532.png]]

---

## 2. I livelli

Dobbiamo **vincere almeno i livelli 1 e 2**.

| Livello | Comportamento del drago                                                                                   | Cosa serve al cavaliere                                                                          |
| ------- | --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **1**   | Fermo. Per il cavaliere è come un **muro**.                                                               | [[#3. BFS                                                                                        |
| **2**   | Si muove solo se il cavaliere è entro **3 caselle** da lui.                                               | BFS che tiene conto della zona di rischio e che ricalcola il percorso mentre il drago si muove.  |
| **2.5** | Va **dritto verso il cavaliere** (appunto originale: «va dritto verso il drago», soggetto non esplicito). | Come sopra, ma il drago è prevedibile: ignora i muri e riduce la distanza in linea retta.        |
| **3**   | È **più lento**, ma insegue il cavaliere con il **percorso migliore** (cioè usa anch'esso una ricerca).   | Serve ragionare sulla "gara": il cavaliere deve arrivare al tesoro prima che il drago lo prenda. |

> [!warning] Da verificare con il codice della consegna Il soggetto della frase sul livello 2.5 non è chiaro negli appunti. L'interpretazione più sensata è che sia il **drago** ad andare dritto verso il cavaliere (inseguimento "ingenuo", senza pianificare). Conviene controllare nel codice del livello.

---

## 3. BFS

### 3.1 Idea

La **Breadth-First Search** (ricerca in ampiezza) visita la griglia **a strati**: prima la cella di partenza, poi tutte le celle a distanza 1, poi quelle a distanza 2, e così via. Usa una **coda FIFO** (il primo inserito è il primo estratto).

Proprietà fondamentale: in un grafo **non pesato** (ogni mossa costa 1), la **prima volta** che la BFS raggiunge una cella lo fa attraverso un percorso con il **minor numero di mosse**. Per questo la BFS trova il **percorso più breve**.

### 3.2 Come si applica a un labirinto

- Ogni **cella libera** è un **nodo**.
- Due celle adiacenti (su, giù, sinistra, destra) sono collegate da un **arco**.
- I **muri** sono nodi che non esistono.
- Per riottenere il percorso si memorizza per ogni cella da quale cella ci siamo arrivati (`came_from`) e alla fine si risale dal tesoro all'indietro.

### 3.3 Pseudocodice

```text
frontier = coda vuota
inserisci start in frontier
came_from[start] = None

finché frontier non è vuota:
    cur = estrai il primo da frontier
    se cur è il goal: fermati
    per ogni vicino libero di cur:
        se il vicino non è in came_from:
            came_from[vicino] = cur
            inserisci vicino in frontier

ricostruisci il percorso risalendo da goal con came_from
```

### 3.4 Complessità e confronti

- **Tempo e memoria:** $O(V + E)$, dove $V$ sono le celle libere ed $E$ le adiacenze. Su una griglia è proporzionale al numero di celle.
- **BFS e Dijkstra:** se tutti gli archi costano 1, Dijkstra si riduce alla BFS. La coda FIFO è più semplice ed efficiente di una coda con priorità.
- **BFS e A*:** la BFS è una ricerca **non informata** (si espande in tutte le direzioni). A* è **informata**: usa un'euristica (es. distanza di Manhattan) per dirigere la ricerca verso l'obiettivo e di solito visita molte meno celle. Resta ottimo se l'euristica è **ammissibile**, cioè non sovrastima mai la distanza reale. Su una griglia a 4 direzioni la distanza di Manhattan lo è.

> [!tip] Se la BFS non basta Con un drago che si muove, la mappa cambia a ogni turno. Il cavaliere deve quindi **ricalcolare** il percorso a ogni passo (o quando il drago si sposta), non pianificare una volta sola all'inizio.

## 4. Ripasso di Python

### 4.1 Classi astratte

Una **classe astratta** è una classe pensata per **essere ereditata**, non istanziata. Definisce quali metodi le sottoclassi devono avere, senza implementarli (nel corpo c'è spesso solo `pass`: da qui l'appunto «classi con pass»).

In Python si usa il modulo `abc`:

```python
from abc import ABC, abstractmethod

class Agent(ABC):
    @abstractmethod
    def act(self, percept):
        pass                         # nessuna implementazione: la scrivono le sottoclassi

Agent()                              # TypeError: non si può istanziare una classe astratta

class Knight(Agent):
    def act(self, percept):
        return "su"                  # ora la classe è concreta

Knight().act(None)                   # 'su'
```

Dettaglio importante: una sottoclasse **non può essere istanziata** finché non sovrascrive **tutti** i metodi astratti. Solo con `pass` e senza `ABC`, invece, Python non segnala nulla.

### 4.2 Dataclass

Una **dataclass** è una classe pensata per **contenere dati**. Il decoratore `@dataclass` genera automaticamente `__init__`, `__repr__` e `__eq__` a partire dai campi annotati, quindi **non serve scrivere l'`__init__`** a mano. Dalla versione 3.7 di Python.

```python
from dataclasses import dataclass

@dataclass
class Posizione:
    riga: int
    colonna: int

p = Posizione(2, 3)
print(p)                             # Posizione(riga=2, colonna=3)
print(p == Posizione(2, 3))          # True
```

Con `@dataclass(frozen=True)` gli oggetti sono **immutabili e hashable**, utile per usarli come chiavi di dizionario o elementi di un set (come nella BFS qui sopra).

### 4.3 Decoratori

Un **decoratore** è una funzione che **riceve una funzione, la avvolge (wrapper) e ne restituisce una nuova** con un comportamento esteso. L'esempio dell'appunto: un decoratore che esegue la funzione e raddoppia il risultato.

```python
from functools import wraps

def doppio(f):
    @wraps(f)                        # conserva nome e docstring dell'originale
    def wrapper(*args, **kwargs):
        return f(*args, **kwargs) * 2
    return wrapper

@doppio                              # equivale a: somma = doppio(somma)
def somma(a, b):
    "somma due numeri"
    return a + b

somma(2, 3)                          # 10
```

`@wraps(f)` evita che il wrapper "sostituisca l'identità" della funzione originale (altrimenti `somma.__name__` risulterebbe `wrapper`). Anche `@dataclass` e `@abstractmethod` sono decoratori.

### 4.4 Annotazioni di tipo (type hints)

Si possono **annotare i tipi** di variabili, parametri e valori restituiti. Python 3.12 ha introdotto l'istruzione `type` per creare **alias di tipo**:

```python
type Vector = list[float]            # nuovo nome per "lista di float"

def norma(v: Vector) -> float:
    return sum(x * x for x in v) ** 0.5
```

> [!warning] Correzione importante agli appunti Negli appunti c'era scritto che passando un tipo sbagliato Python "segnala un errore". **A runtime Python non controlla le annotazioni**: il codice gira comunque. A segnalare l'errore sono i **type checker** (es. `mypy`, `pyright`) o l'editor. Il vantaggio principale è la leggibilità e l'aiuto dell'IDE.
> 
> Inoltre `type` come parola chiave richiede **Python ≥ 3.12**: su versioni precedenti dà `SyntaxError` e si usa `Vector = list[float]` (o `typing.TypeAlias`).
> 
> Non è una "libreria base type": è la sintassi del linguaggio, mentre le annotazioni più complesse stanno nel modulo `typing`.

---

## 4. Ambiente e agente

Nell'IA moderna (impostazione di Russell e Norvig) un **agente** è qualunque cosa che **percepisce** l'ambiente tramite **sensori** e **agisce** su di esso tramite **attuatori**. Ciò che l'agente percepisce in un istante è il **percept**.

- La **funzione agente** associa a ogni sequenza di percezioni un'azione. Il **programma agente** è la sua implementazione.
- L'**ambiente** è il mondo in cui l'agente opera. Per progettare un agente si descrive il **task environment** con la sigla **PEAS**: **P**erformance (misura di prestazione), **E**nvironment, **A**ctuators, **S**ensors.

PEAS applicato a questo esercizio:

|**Componente**|**Descrizione**|
|---|---|
|**Performance**|Raggiungere il tesoro, non farsi prendere dal drago, restare entro 200 passi|
|**Environment**|Labirinto a griglia con muri, tesoro e drago|
|**Actuators**|Muoversi su, giù, sinistra, destra|
|**Sensors**|Posizioni di cavaliere, drago, tesoro e mappa|

> [!warning] Da chiarire Negli appunti: «ambiente è una classe, agente una sottoclasse di ambiente». Dal punto di vista **concettuale** agente e ambiente sono due cose distinte che interagiscono, non una la sottoclasse dell'altra. Nel codice della consegna potrebbe esserci una struttura diversa (per esempio una classe base comune): conviene guardare come sono dichiarate le classi.

---

## 7. Da fare / da chiarire

- [ ] Rileggere il codice della consegna e individuare dove implementare la funzione di scelta della mossa.
- [ ] Implementare la BFS e superare il **livello 1**.
- [ ] Adattare la strategia per il **livello 2** (zona di rischio a 3 caselle, ricalcolo a ogni turno).
- [ ] Chiarire il comportamento esatto del drago nel livello 2.5.
- [ ] Verificare quante mosse al massimo consente il livello (limite 200 passi).

---

## Fonti

- [Breadth-first search in a maze, Gettysburg College (esercizio con BFS e classe Agent)](https://modelai.gettysburg.edu/2016/pyconsole/ex1/index.html)
- [Unweighted Shortest Paths, Kansas State University (BFS come Dijkstra con costi unitari)](https://textbooks.cs.ksu.edu/cis300/graphs/breadth-first/)
- [Introduction to BFS, Codeforces blog](https://codeforces.com/blog/entry/135547)
- [A* Algorithm, labuladong (BFS, Dijkstra e A* su griglie)](https://labuladong.online/en/algo/data-structure/a-star/)
- [Pathfinding visualizer: A* vs BFS (DEV Community)](https://dev.to/dev48v/i-built-a-pathfinding-visualizer-watch-a-visit-a-fraction-of-the-cells-bfs-does-for-the-same-2mi1)
- [Agents and environments, Russell & Norvig (slide, University of Maryland)](https://www.cs.umd.edu/~nau/cmsc421/chapter02.pdf)
- [Intelligent agents e PEAS (University of Iowa)](https://homepage.divms.uiowa.edu/~tinelli/classes/4420/Spring18/notes/2-agents.pdf)
- [Documentazione Python: dataclasses](https://docs.python.org/3/library/dataclasses.html)
- [Documentazione Python: abc](https://docs.python.org/3/library/abc.html)
- [Abstract Base Classes in Python (Dan Bader)](https://dbader.org/blog/abstract-base-classes-in-python)
- [What is PEP 695? (pydevtools)](https://pydevtools.com/handbook/explanation/what-is-pep-695/)
- [functools.wraps (Lerner Python)](https://lernerpython.com/2019/05/05/making-your-python-decorators-even-better-with-functool-wraps/)