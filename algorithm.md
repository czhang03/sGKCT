Algorithm without normalization

```ocaml
let bisim e f: bool = 
    (*if e, f accept different atoms*)
    if ϵ(e) ≠ ϵ(f) then false else
    (*
    main checking algorithm.
    require all the transition to follow the following rules:
    - either the transitions are disjoint in booleans
    - or they execute the same action and the resulting states are also bisimular
    *)
    forall ψ_e ↦ (e', p) ∈ δ(e), ψ_f ↦ (f', q) ∈ δ(f), 
    (
        (* the result has already been marked equal *)
        equiv_{bisim} e' f' ∨
        (* when two boolean disjoint, skip *)
        ψ_e ∧ ψ_f = 0 ∨ 
        (* in order for e, f to be bisimular, they will need to execute the same action and result in bisimular states*)
        (p = q ∧ (union_{bisim} e f; bisim e' f'))
    )
```

Tarjan's algorithm adapt to calculate live states.
There are two sub-procedure for Tarjan's algorithm, 
the first to explore all the predecessor's (derivatives in our case),
the second is to collect the scc of a certain expression,
when the entire scc is explored.
For efficiency, we will use a union-find structure for SCCs.

The Tarjan's algorithm keeps track of three value of a expression,
its index, low_link value, and whether it is on stack
- index is assigned incrementally, this grantees that the later element on the stack
    always have higher index on the stack
- the low_link value represents the **lowest index that the current node can reach**.
    Thus when a node has the low link value as its index during backtracking,
    all the child of this node has been explored, 
    and the node cannot reach any previous element of the stack.
    Hence, its entire scc is explored and is on the top of the stack.
- Finally, the on stack boolean simply keeps track of whether the expression is on the stack.
    Keeping track of this value avoids the linear time check of whether 
    a expression is on the stack.

When exploring the predecessor's,
our program is eased by the following invariant
- If a scc is formed by `construct_scc_of`, then the liveness of the scc is known
- If an expression is explored but not on the stack, then its scc is already formed

This means that when we are exploring a predecessor (derivative) of an expression `e`
there are the following cases
- the derivative is unvisited, then we visit them;  
- if the derivative `f` is on stack (which implies they have been visited),
    then we will compute the low_link of `e` by the lowest reachable idx of `f` 
    (AKA the low link of `f`).
- if the derivative `f` is not on stack but explored,
    then its scc is already constructed with a known liveness:
    if `f` is in a live SCC, then `e` should be in `to_live_scc`.

Finally, notice that a SCC is live if and only if there exists either 
a expression that transition to a live SCC, or an accepting expression.
Thus when constructing the scc, we will compute whether the scc is live 
by cumulating the result of `f ∈ to_live_scc` and `ϵ(f) ≠ 0` for each expression `f` in the scc.

```ocaml
let cur_idx = 0 
(* vertices stack, can also store the hash of the expression to save memory,
   if the the hash function is an isomorphism*)
let stack: exp stack = empty
(* tracks the information of a given expression*)
type expInfo = {
    idx: int
    mutable low_link: int
    mutable on_stack: bool
}

let info_of: exp expInfo hashtbl = empty
(* Whether a expression transitions to a live scc

Notice that if a expression is in the set, then exp is necessarily live,
but if it is not, exp is not necessarily dead.
This only keeps track of the expression on the stack,
expressions not on the stack but explored are already collected as scc, 
hence should use `live_scc` instead.*)
let to_live_scc: exp set = empty
(* all the currently discovered live scc,
   each scc is marked by its "representing expression" in the union-find object.
   for a expression `e`, the representing expression for its scc
   can be computed by `rep_{scc} e`.*)
let live_scc: exp set = empty

(** visit the predecessor of e
    
this will set the info of e to the correct value 
and compute the `detected_liveness` of `e`*)
let visit_predecessor_of e: unit = 
    (* iterate through all the predecessor of e,
    to compute the liveness and low link of e*)
    for (b ↦ f) ∈ δ(e): 
        (* f hasn't been explored, then explore f *)
        if f ∉ info_of.keys then
            visit(f);
            info_of[e].low_link := min(info_of[e].low_link, info_of[f].low_link)
        (* if f has been explored, and on stack, then it is in the scc of e *)
        else if info_of[f].on_stack then 
            (*if f is live, then e is live*)
            info_of[e].low_link := min(info_of[e].low_link, info_of[f].low_link)
        (* if f explored but not on stack This means that f is part of an explored scc. 
           We will take the liveness of that scc *)
        else
            e_to_live_scc := e_to_live_scc ∨ is_live_scc[rep_{scc} f];
    
    (* collect whether `e` has transition to live scc*)
    if e_to_live_scc then 
        to_live_scc := to_live_scc ∪ {e}

(** This will collect all the element in the scc of e, and remove them from the stack
    
This procedure is called when the entire scc of e is detected
*)
let construct_scc_of e : unit = 
    scc_is_live = false
    do 
        (* remove all the vertex on stack until e *)
        let f = stack.pop ();
        infoTbl[f].on_stack := false;
        scc_is_live := scc_is_live ∨ f ∈ to_live_scc ∨ ϵ(f) ≠ 0
        delete f to_live_scc;
        (* add f to the scc of e *)
        union_{scc} e f
    until f = e 
    (* add `e` to live scc if it is live *)
    if scc_is_live then live_scc = live_scc ∪ {rep_(scc) e}

(** Construct union find object that classifies the scc

This function is based on Tarjan's SCC algorithm;
it will iterate through all the reachable expressions from `e`,
and should only be called when e has not been explored.
*)
let visit e : unit =
    (* initialize the computation *)
    stack.push e;  
    let info_of[e] = {  
        idx = cur_idx;  
        low_link = cur_idx;
        on_stack = true
    }
    cur_idx := cur_idx + 1;
   
    visit_predecessor_of e
    
    (* if e is the root of the scc, then the entire scc of e is detected.*)
    if e.low_link = e.index then
        construct_scc_of e
```

Then we can modify our bisim algorithm to take into account 
of the liveness of each expression
```ocaml
let equiv e f: bool =
    (* get the liveness of all the reachable expression from `e` and `f`*)
    visit e; 
    visit f;
    (* run the modified bisim algorithm based on liveness *)
    bisim_with_liveness e f 
where 
    (* e is live when its scc is live *)
    let is_live e = (rep_{scc} e) ∈ live_scc
    (* bisim algorithm taking into account liveness*)
    let bisim_with_liveness e f: bool = 
        (* liveness checks *)
        if ¬ (is_live e) ∧ ¬ (is_live f) then true   (* both dead *)
        else if (is_live e) ∧ ¬ (is_live f) then false   (*one dead, one live*)
        else if ¬ (is_live e) ∧ (is_live f) then false   (*one dead, one live*)
        (*both e and f are live, if e, f accept different atoms,
           then they are not bisimilar*)
        else if ϵ(e) ≠ ϵ(f) then false 
        (*
        main checking algorithm, both `e` and `f` are live,
        require all the transition to follow the following rules:
        - either the transitions are disjoint in booleans
        - or they execute the same action and the resulting states are also bisimular
        *)
        else forall ψ_e ↦ (e', p) ∈ δ(e), ψ_f ↦ (f', q) ∈ δ(f), 
        (
            (* the result has already been marked equal *)
            equiv_{bisim} e' f' ∨
            (* when two boolean disjoint, skip *)
            ψ_e ∧ ψ_f = 0 ∨ 
            (* in order for e, f to be bisimular, they will need to execute the same action and result in bisimular states*)
            (p = q ∧ (union_{bisim} e f; bisim_with_liveness e' f'))
        )
```