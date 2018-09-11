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