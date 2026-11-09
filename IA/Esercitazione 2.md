# The Knight's Maze 2

>[!important] obbligatorio risolvere l'esercizio: 4 - 5, implementare algoritmo di ricerca A*

L'approccio iniziale è basato sull'algoritmo di **Dijkstra** sufficiente per i primi problemi, ma mostra limiti strutturali a partire dall'esercizio 4 a causa dell'esaurimento del `thinking_budget`: esplorando uniformemente lo spazio degli stati senza una guida euristica, il numero di nodi espansi satura le risorse di calcolo a disposizione.

Introducendo ==🔴A*== con un'euristica ammissibile (distanza Manhattan), la ricerca viene orientata verso il target, consentendo di superare sia l'esercizio 4 sia l'esercizio 6 (quest'ultimo gestendo la presenza del drago tramite una semplice politica di attesa, se il drago è troppo vicino aspetto che si allontani). Tuttavia, questa configurazione fallisce nell'esercizio 5, dove la conformazione della mappa o la complessità dei percorsi richiede ancora troppe espansioni per un singolo ramo di ricerca orientato in avanti.
![[AstarKnight.excalidraw|800]]

Per ottenere una soluzione che risolva contemporaneamente anche l'esercizio 5, l'evoluzione naturale è l'adozione del ==🔴Bidirectional A*==. Facendo progredire simultaneamente due ricerche guidate da euristica — una dalla partenza verso l'uscita e una a ritroso dall'uscita verso la partenza — lo spazio esplorato si riduce da una singola espansione di raggio $d$ a due fronti di raggio $d/2$, abbattendo drasticamente i nodi generati ($O(b^{d/2})$ rispetto a $O(b^d)$) e rientrando comodamente nei vincoli di `thinking_budget'.

![[IdeaKnight.excalidraw|800]]
### Pseudocodice di A* 
```python
function A*-SEARCH(problem, h) returns a solution, or failure
    node     ← a node with STATE = problem.INITIAL-STATE, PATH-COST = 0
    frontier ← a priority queue ordered by PATH-COST + h(STATE),   ◄ UC search orders by PATH-COST alone
               with node as the only element
    explored ← an empty set
    loop do
        if EMPTY?(frontier) then return failure
        node ← POP(frontier)                        /* the node with the lowest f */
        if problem.GOAL-TEST(node.STATE) then return SOLUTION(node)
        add node.STATE to explored
        for each action in problem.ACTIONS(node.STATE) do
            child ← CHILD-NODE(problem, node, action)
            if child.STATE is not in explored or frontier then
                frontier ← INSERT(child, frontier)
            else if child.STATE is in frontier with higher PATH-COST then
                replace that frontier node with child              replace that frontier node with child
```

Per passare dall'implementazione di Dijkstra a quella di A* è sufficiente apportare tre modifiche principali alla struttura dell'algoritmo:

- Aggiungere la funzione euristica `h(nodo, goal)` come parametro di valutazione.
- Ordinare la coda a priorità in base al costo stimato totale $f(n) = g(n) + h(n)$ anziché solo in base a $g(n)$.
- Tenere traccia del costo reale $g(n)$ separatamente dalla priorità $f(n)$ utilizzata nella coda.

---
### Pseudocodice di Bidirectional A*

Per implementare la variante bidirezionale, la struttura di A* viene estesa mantenendo due code di priorità e due mappe dei costi distinte, alternando l'espansione tra il fronte _forward_ e quello _backward_ fino al primo punto di contatto tra le due ricerche:


```python
function BIDIRECTIONAL-A*(graph, start, goal, cost_fn, dragon_pos, h) returns a solution, or failure
    if start == goal then return [start]

    /* Inizializzazione Ramo In Avanti (Forward: da start a goal) */
    frontier_F ← priority queue ordered by g + h(node, goal), containing (h(start, goal), start)
    min_cost_F ← map with min_cost_F[start] = 0
    came_from_F ← map with came_from_F[start] = null

    /* Inizializzazione Ramo All'Indietro (Backward: da goal a start) */
    frontier_B ← priority queue ordered by g + h(node, start), containing (h(goal, start), goal)
    min_cost_B ← map with min_cost_B[goal] = 0
    came_from_B ← map with came_from_B[goal] = null

    /* Calcolo delle celle interdette per la presenza del drago */
    forb ← empty set
    if dragon_pos is not null then
        ADD(dragon_pos, forb)
        ADD-ALL(graph.NEIGHBOURS(dragon_pos), forb)

    while not EMPTY?(frontier_F) and not EMPTY?(frontier_B) do

        /* --- PASSO 1: Espansione Ramo Forward (verso goal) --- */
        f_cost_F, curr_F ← POP(frontier_F)

        if curr_F is in min_cost_B then
            return RECONSTRUCT-BI-PATH(came_from_F, came_from_B, curr_F)

        if f_cost_F <= min_cost_F[curr_F] + h(curr_F, goal) then
            for each neighbor in graph.NEIGHBOURS(curr_F) do
                if neighbor is in forb or cost_fn(neighbor) == ∞ then continue

                new_g ← min_cost_F[curr_F] + cost_fn(neighbor)
                if neighbor not in min_cost_F or new_g < min_cost_F[neighbor] then
                    min_cost_F[neighbor] ← new_g
                    came_from_F[neighbor] ← curr_F

                    /* Se il vicino è già stato visitato dal ramo Backward, i fronti si toccano */
                    if neighbor is in min_cost_B then
                        return RECONSTRUCT-BI-PATH(came_from_F, came_from_B, neighbor)

                    priority ← new_g + h(neighbor, goal)
                    INSERT((priority, neighbor), frontier_F)

        /* --- PASSO 2: Espansione Ramo Backward (verso start) --- */
        f_cost_B, curr_B ← POP(frontier_B)

        if curr_B is in min_cost_F then
            return RECONSTRUCT-BI-PATH(came_from_F, came_from_B, curr_B)

        if f_cost_B <= min_cost_B[curr_B] + h(curr_B, start) then
            for each neighbor in graph.NEIGHBOURS(curr_B) do
                if neighbor is in forb or cost_fn(neighbor) == ∞ then continue

                new_g ← min_cost_B[curr_B] + cost_fn(neighbor)
                if neighbor not in min_cost_B or new_g < min_cost_B[neighbor] then
                    min_cost_B[neighbor] ← new_g
                    came_from_B[neighbor] ← curr_B

                    /* Se il vicino è già stato visitato dal ramo Forward, i fronti si toccano */
                    if neighbor is in min_cost_F then
                        return RECONSTRUCT-BI-PATH(came_from_F, came_from_B, neighbor)

                    priority ← new_g + h(neighbor, start)
                    INSERT((priority, neighbor), frontier_B)

    return failure
```


>[!note] Risultati
>Utilizzare in questo modo il Bidirectional A* mi permette di ottenere il 100% nel 4, 5 e 6 esercizio.
>