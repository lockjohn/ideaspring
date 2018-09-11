# ideaspring
daily notes, ideas to follow up


# Graphing Topological Sort 
## Graph Data Structure
- vertices (dots)
- edges (lines) 
    - can add direction (directed graph) - all or none
    - can leave edges unweighted, or u can weight them - all or none

### Facebook graph
- vertices: facebook users |V| = # of verticies
- edges: connect user A to user B if and only if A & B are 'friends'
    - the edges are undirected and unweighted
    - |E| = # edges
- sparse graph has low density
- density = |E|/|V|(|V|-1), density has a maximum of 1 (in an undirected graph), min is 0
- max of density changes when using a directed graph due to in and out edges at a vertices  so is going to be 2

## Twitter graph
- vertices: Users
- Edges: directed A -> B if A is_following?(B)
- low density in total, but might have a subgraph that actually might be dense

sparse is proportional to speed of some algorithms. 

## Trees
- ex: BinarySearchTree
- n vertices and n-1 edges, connected graphs, never have 'cycle' in the graph (not more than one way to get to same node)
- ex: DocumentOjbectModel
    - V: DOMElements
    - E: parents, children connected
- density of a tree is always 1 over n = (n-1)/n(n-1)

## Dependency Set
- vertices: tasks
- edges: Task A -> Task B
- directed, which do I have to do first in order to do another
- no cycles
- topological sort - is the way to get the dependencies in order


## Graph Class

`class Graph`
    `def initialize`
    `@verticies = []`
   ` @edges = []`
    `end`
   ` end`

Edge and Vertex class also need to be created or can use a matrix to represent the edges

# Topological Sort
## Kahn's Algorithm
1. Queue up all vertices with no 'in' edges (incoming edges)
2. Pop off vertices from queue
    a. remove vertex + out edges from graph
    b. push vertex into sorted
    c. examine destination vertices, push on to queue if no more indices
 ### Thus 
    - Vertex needs access to edges
    - Graph class also needs access to edges to more easily and directly delete edges


```ruby
def topo_sort(graph)
    sorted = []
    top = new Queue()
    
    graph.vertices.each do |vertex|
        if vertex.in_edges.empty? #excluding current incoming
        top.enqueue(vertex)
        end
    end

    until top.empty?
    current = top.pop
    sorted << current
        current.out-edges.each |edge|
            if edge.destination.in_edges.empty?
                top.enqueue(edge.destination)
            end
            graph.delete_edge(edge) #this is wrong, needs to be moved up
        end
        graph.delete_vertex(current)
    end
    sorted
end
```

### time complexity of Kahn's as above
- first iteration is O(|V|) 
- until loop is O(|E|)
- O (|V| + |E|) - depends on the density of edges

* khan's algo is not 'deterministic'
Modifciation - Coffman-Graham
Modify DFS - to do this

__Use Cases__
- Task/Dependencies
- Webpack creates a list of files and depenencies and create an order by topological sort
- Schedule tasks given scheduling restrictions
- minimal spanning tree

# Dynamic Programming
e.g. using memoization to calculate fib seq
```python
def fibonacciVal(n):
  memo = [0] * (n+1)
  memo[0], memo[1] = 0, 1
  for i in range(2, n+1):
    memo[i] = memo[i-1] + memo[i-2]
  return memo[n]
```

Memoization means no re-computation, which makes for a more efficient algorithm. Thus, memoization ensures that dynamic programming is efficient, but it is choosing the right sub-problem that guarantees that a dynamic program goes through all possibilities in order to find the best one.

## Step 1 - subproblem identification
Identify the subproblem (the problem you want to memoize?) in words
- If you can identify a sub-problem that builds upon previous sub-problems to solve the problem at hand, then you’re on the right track.
## Step 2 - Write out the sub-problem as a recurring mathematical decision.
There are two questions that I ask myself every time I try to find a recurrence:
- What decision do I make at every step?
- If my algorithm is at step i, what information would it need to decide what to do in step i+1? (And sometimes: If my algorithm is at step i, what information did it need to decide what to do in step i-1?)

e.g. Punchcards

__What decision do I make at every step?__ Assume that the punchcards are sorted by start time, as mentioned previously. For each punchcard that is compatible with the schedule so far (its start time is after the finish time of the punchcard that is currently running), the algorithm must choose between two options: to run, or not to run the punchcard.

__If my algorithm is at step i, what information would it need to decide what to do in step i+1?__ To decide between the two options, the algorithm needs to know the next compatible punchcard in the order. The next compatible punchcard for a given punchcard p is the punchcard q such that s_q (the predetermined start time for punchcard q) happens after f_p (the predetermined finish time for punchcard p) and the difference between s_q and f_p is minimized. Abandoning mathematician-speak, the next compatible punchcard is the one with the earliest start time after the current punchcard finishes running.

__If my algorithm is at step i, what information did it need to decide what to do in step i-1?__ The algorithm needs to know about future decisions: the ones made for punchcards i through n in order to decide to run or not to run punchcard i-1.


1. __Recognize a dynamic programming problem.__ This is often the most difficult step and it has to do with recognizing that there exists a recursive relation. __*Can your problem result be expressed as a function of results of the smaller problems that look the same?*__

2. __Determine the number of changing parameters.__ Express your problem in terms of the function parameters and see how many of those parameters are changing. Typically you will have one or two, but technically this could be any number. Typical examples for one parameter problems are fibonacci or a coin change problem, for two parameter is edit distance.

3. __Clearly express the recursive relation.__ Once you figure out that the recursive relation exists and you specify the problems in terms of parameters, this should come as a natural step. How do problems relate to each other. That is, assuming that you have computed the subproblems, how would you compute the main problem.

4. __What are your base cases?__ When you break down sub-problems into smaller sub-problems and you break down those even further, what is the point where you cannot break it anymore? Do all these subproblems end up depending on the same small subproblem or several of them?

5. __Decide if you want to implement it iteratively or recursively and be comfortable with both.__ Know the trade-offs between one or the other, such as recursive stack overflow, sparsity of computation, think if this is a repeated work or something that you always do online, etc.

6. __Add memoization.__ If you’re implementing it recursively, add memoization. If you’re doing it iteratively, this will come for free. Adding memoization should be super mechanical. Just see if you have memoized the state at the beginning of your function and add it to memoization before each return statement. Memoization value should almost always be the result of your function.

7. __Time complexity = (# of possible states * work done per each state)__ This should also be relatively mechanical. Count the number of states that you can have. This will be linear if you have a single parameter, quadratic if you have two and so on. Think about the work done per each state - that is the work that you have to do assuming that you’ve computed all of the subproblems.