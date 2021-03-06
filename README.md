# Java Finite Automata Library

This package includes
 - A parser that reads in GraphViz-formatted finite automata
 - general purpose Classes FiniteAutomaton and TransitionFunction/ TransitionMap
 - A stringifier that converts a given FiniteAutomaton-Object to a GraphViz-formatted graph
 
## Usage

```java
 String inputfile = "graph.gv";
 
 // Create a (generally) nondeterministic finite automaton from a String/ File
 FiniteAutomaton<String, String> nfa =  new GraphConverter().stringToFA(new FileInputStream(inputfile));
 
 // Apply the powerset construction to create a deterministic finite automaton from the input
 FiniteAutomaton<Set<String>, String> dfa = new RabinScott<String, String>().constructDNF(nfa);
 
 // Test whether some input is accepted
 List<String> input = Arrays.asList("1", "0", "1");
 if(dfa.accepts(input) == nfa.accepts(input))
    System.out.println("Success!");
 
 // Convert the new automaton to a String in GraphViz-format
 String result = new GraphConverter().NFAtoString(dfa);
 System.out.println(result);
```

## NFA Powerset construction

Creates a DFA based on a given NFA. For more information on the procedure have a look at [Powerset construction](https://en.wikipedia.org/wiki/Powerset_construction).

Reads in Graphs formatted in the [DOT language](https://graphviz.gitlab.io/_pages/doc/info/lang.html) format by [GraphViz](https://graphviz.gitlab.io/).

Following format should be considered:
 - Normal or doublecircled nodes represent states
 - Edges with labels represent state transitions
 - Use `"&epsilon;"` or `""` or no label for &epsilon; transitions
 - Accepting states are formatted `[shape=doublecircle]`
 - Initial states are marked by a state with `[shape=point]` and an edge towards the initial states


Outputs an equivalent DNF in DOT language format.

Example:
```
digraph finite_state_machine {

    S [shape = doublecircle];
    qi [shape = point ]; 
    
    node [shape = circle];
    qi -> S;
    S  -> q1 [ label = "a" ];
    S  -> S  [ label = "a" ];
    q1 -> S  [ label = "a" ];
    q1 -> q2 [ label = "b" ];
    q2 -> q1 [ label = "b" ];
    q2 -> q2 [ label = "b" ];
    q2 -> q3 [ label = "&epsilon;"];
    q3 -> q2 [ label = "a" ];
}

-- Result ->

digraph {

    "__init" [shape = point];
    "{q1, S}" [shape = doublecircle];
    "{q2, q3}" [shape = circle];
    "{q1, q2, q3}" [shape = circle];
    "{S}" [shape = doublecircle];

    __init -> "{S}";

    "{q1, S}" -> "{q1, S}" [label = "a"];
    "{q1, S}" -> "{q2, q3}" [label = "b"];
    "{q2, q3}" -> "{q1, q2, q3}" [label = "b"];
    "{q1, q2, q3}" -> "{S}" [label = "a"];
    "{q1, q2, q3}" -> "{q1, q2, q3}" [label = "b"];
    "{S}" -> "{q1, S}" [label = "a"];
}

```

Visualized with [GraphViz](https://graphviz.gitlab.io):

<img src="examples/example-1-input.png" alt="input automata (NFA)" height="250px"/> -> <img src="examples/example-1-output.png" alt="output automata (DFA)" height="250px"/>

## TODO

 - Make `FiniteAutomaton` Immutable
 - Create a subclass `DeterministicFinitAutomaton` that allows only one state at a time
