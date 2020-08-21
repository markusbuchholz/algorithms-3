
Introduction
-------------

1. **Object-oriented programming** [[link]](./oo-programming)

2. **Python tips: a crash course** [[link]](./python-tips)


Algorithms Part I
-----------------

Collection of algorithms and data structures implemented in Python and C++. For an optimal learning experience it is recommended to study the material in the following order:

1. **Disjoint-set**: (data structure) [[link]](./disjoint-set)

2. **Stack**: (abstract data structure) [[link]](./stack)

3. **Queue**: (abstract data structure) [[link]](./queue)

4. **Elementary sort**: (sorting algorithm) TBD [selection sort, insertion sort, shellsort]

5. **Mergesort**: (sorting algorithm) [[link]](./mergesort)

6. **Quicksort**: (sorting algorithm) [[link]](./quicksort)

7. **Priority-Queue**: (data structure) similarly to a Queue it stores an array of values, but when `pop()` is called the largest value (higher priority) is returned [[link]](./priority-queue)

8. **Symbol Tables**: (data structure) the primary purpose is to associate a key to a value. The best standard implementation is based on ordered arrays and has O(log N) time complexity for `get()` and O(N) time complexity for `put()` [[link]](./symbol-tables)

9. **Binary Search Trees (BSTs)**: (data structure) it is a smart implementation of symbol tables. It solves the problem given by the standard symbol table implementations, giving O(log N) time complexity for both `get()` and `put()`. [[link]](./binary-search-trees)

10. **Balanced Search Trees**: (data structure) it solves the problem of unbalanced trees, given by standard BSTs. It is also known as [self-balancing](https://en.wikipedia.org/wiki/Self-balancing_binary_search_tree) binary search tree. The problem with this data structure is the overhead in keeping track of different types of node and links [[link]](./balanced-search-trees)

11. **Red-Black Balanced Search Trees**: (data structure) it solves the overhead problem of standard balanced trees. The implementation guarantees O(log N) time complexity in all the operations. The resulting tree is not perfectly balanced in some particular cases [[link]](./red-black-balanced-search-trees)

12. **Geometric applications of BST**: (applications) variations of BSTs can be used in a variety of geometric applications, such as evaluating segment and rectangle intersections. [[link]](./geometric-bst)

13. **Hash tables**: (data structure) using an hash function that maps any integer into a subset it is possible to store a large number of values into a limited number of bins [[link]](./hash-functions)

14. **Graphs**: (data structure) the graph data structure is very important and it can be implemented in three ways. The first way is using a matrix of size V*V where each value identifies a connection between two vertices. The third way is using an adjacency-list, meaning a list of list where for each vertex there is associated a list of neighbours. [[link]](./graph)

15. **Depth-First Search**: (search algorithm) using a depth search it is possible to look for path and vertex in a graph. It can be used to verify in constant time if two nodes are connected. [[link]](./depth-first-search)

16. **Breadth-First Search**: (search algorithm) using a breadth search it is possible to look for path and vertex in a graph. It is used to find the shortest path between two nodes. [[link]](./breadth-first-search)

Algorithms Part II
------------------

17. **Direct Grapsh (Digraphs)**: (data structure) a directed graph or digraph has directed edges. The depth-first and breadth-first algorithms still work. However finding strong connected components requires a slightly more complex algorithm. [[link]](./digraph)

18. **Minimum Spanning Trees**: (data structure and search algorithm) sometimes it is necessary to find the minimum set of edges connecting all the nodes, where to each edge it is associated a weigh. The minimum spanning tree correspond to the tree having the edges with lowest weight connecting all the nodes. Here are discussed three implementations: greedy, Kruskal, and Prim. [[link]](./minimum-spanning-trees)

19. **Shortest path**: (search algorithm) finding the shortest path between two nodes in efficient way is not easy. Here are discussed different solutions and the Dijkstra algorithm [[link]](./shortest-path)

Useful templates and strategies
---------------------------------

- **Boyer–Moore majority vote**: finding the majority of a sequence of elements using linear time and constant space [[link]](./useful/boyer-moore-majority/README.md)

- **Find loops (an topological sorting) in directed graphs via Depth-First Search**: the use of DFS is the common choice for finding loops and topologically sort a digraph (other choice could be Breadth-First Search) [[link]](./useful/loop-finder-dfs/README.md)

- **Binary search universal template**: a flexible and universal template for solving binary search problems (with examples) [[link]](./useful/template-binary-search/README.md)

Interviews
-----------

Official requirements (of different companies) for passing coding interview:

- Google [[readme]](./interview/google.md)
- Facebook [[readme]](./interview/facebook.md)

Machine Learning interviews
---------------------------

Various guides for passing Machine Learning interviews:

- Tips and Tricks [[readme]](./machine-learning/khangich/README.md)


