## EX NO:02
## DATE:28.04.2022
# <p align="center">Breadth First Search
## AIM

To develop an algorithm to find the route from the source to the destination point using breadth-first search.

## THEORY
Breadth-first search, also known as BFS, finds shortest paths from a given source vertex to all other vertices, in terms of the number of edges in the paths.Breadth-first search (BFS) is an algorithm for traversing or searching tree or graph data structures. It starts at the tree root, and explores all of the neighbour nodes at the present depth prior to moving on to the nodes at the next depth level. BFS is a graph traversal approach in which you start at a source node and layer by layer through the graph, analyzing the nodes directly related to the source node. Then, in BFS traversal, you must move on to the next-level neighbor nodes.

## DESIGN STEPS

### STEP 1:
Identify a location in the google map:

### STEP 2:
Select a specific number of nodes with distance

### STEP 3:
Import required packages.

### STEP 4:
Initialize a queue. All nodes in which nearby nodes have already been visited; you must remove them from the queue.Because the queue is now empty, the bfs traversal has ended.

### STEP 5:
Include each node and its distance separately in the dictionary data structure.

### STEP 6:
End of program.

## ROUTE MAP
#### Include your own map
![Map1](https://user-images.githubusercontent.com/75235747/166148512-a6d7c6d2-6985-4a70-888a-ccad0c9b8aa8.JPG)
<br>

## PROGRAM
```python
Developed by: R Arunraj
Register  No: 212220230004
```
```python
%matplotlib inline
import matplotlib.pyplot as plt
import random
import math
import sys
from collections import defaultdict, deque, Counter
from itertools import combinations
class Problem(object):
    """The abstract class for a formal problem. A new domain subclasses this,
    overriding `actions` and `results`, and perhaps other methods.
    The default heuristic is 0 and the default action cost is 1 for all states.
    When yiou create an instance of a subclass, specify `initial`, and `goal` states 
    (or give an `is_goal` method) and perhaps other keyword args for the subclass."""

    def __init__(self, initial=None, goal=None, **kwds): 
        self.__dict__.update(initial=initial, goal=goal, **kwds) 
        
    def actions(self, state):        
        raise NotImplementedError
    def result(self, state, action): 
        raise NotImplementedError
    def is_goal(self, state):        
        return state == self.goal
    def action_cost(self, s, a, s1): 
        return 1
    
    def __str__(self):
        return '{0}({1}, {2})'.format(
            type(self).__name__, self.initial, self.goal)
class Node:
    "A Node in a search tree."
    def __init__(self, state, parent=None, action=None, path_cost=0):
        self.__dict__.update(state=state, parent=parent, action=action, path_cost=path_cost)

    def __str__(self): 
        return '<{0}>'.format(self.state)
    def __len__(self): 
        return 0 if self.parent is None else (1 + len(self.parent))
    def __lt__(self, other): 
        return self.path_cost < other.path_cost
failure = Node('failure', path_cost=math.inf) # Indicates an algorithm couldn't find a solution.
cutoff  = Node('cutoff',  path_cost=math.inf) # Indicates iterative deepening search was cut off.
def expand(problem, node):
    "Expand a node, generating the children nodes."
    s = node.state
    for action in problem.actions(s):
        s1 = problem.result(s, action)
        cost = node.path_cost + problem.action_cost(s, action, s1)
        yield Node(s1, node, action, cost)
        

def path_actions(node):
    "The sequence of actions to get to this node."
    if node.parent is None:
        return []  
    return path_actions(node.parent) + [node.action]


def path_states(node):
    "The sequence of states to get to this node."
    if node in (cutoff, failure, None): 
        return []
    return path_states(node.parent) + [node.state]
FIFOQueue = deque
def breadth_first_search(problem):
    "Search shallowest nodes in the search tree first."
    node = Node(problem.initial)
    if problem.is_goal(problem.initial):
        return node
    # Remove the following comments to initialize the data structure
    frontier = FIFOQueue([node])
    reached = {problem.initial}
    while frontier:
        node = frontier.pop()
        for child in expand(problem, node):
            s = child.state
            if problem.is_goal(s):
                return child
            if s not in reached:
                reached.add(s)
                frontier.appendleft(child)
    return failure
class RouteProblem(Problem):
    """A problem to find a route between locations on a `Map`.
    Create a problem with RouteProblem(start, goal, map=Map(...)}).
    States are the vertexes in the Map graph; actions are destination states."""
    
    def actions(self, state): 
        """The places neighboring `state`."""
        return self.map.neighbors[state]
    
    def result(self, state, action):
        """Go to the `action` place, if the map says that is possible."""
        return action if action in self.map.neighbors[state] else state
    
    def action_cost(self, s, action, s1):
        """The distance (cost) to go from s to s1."""
        return self.map.distances[s, s1]
    
    def h(self, node):
        "Straight-line distance between state and the goal."
        locs = self.map.locations
        return straight_line_distance(locs[node.state], locs[self.goal])    
class Map:
    """A map of places in a 2D world: a graph with vertexes and links between them. 
    In `Map(links, locations)`, `links` can be either [(v1, v2)...] pairs, 
    or a {(v1, v2): distance...} dict. Optional `locations` can be {v1: (x, y)} 
    If `directed=False` then for every (v1, v2) link, we add a (v2, v1) link."""

    def __init__(self, links, locations=None, directed=False):
        if not hasattr(links, 'items'): # Distances are 1 by default
            links = {link: 1 for link in links}
        if not directed:
            for (v1, v2) in list(links):
                links[v2, v1] = links[v1, v2]
        self.distances = links
        self.neighbors = multimap(links)
        self.locations = locations or defaultdict(lambda: (0, 0))

        
def multimap(pairs) -> dict:
    "Given (key, val) pairs, make a dict of {key: [val,...]}."
    result = defaultdict(list)
    for key, val in pairs:
        result[key].append(val)
    return result
ngp_nearby_locations = Map(
    {('MyHouse', 'NgpBusStand'):  3, ('MyHouse', 'NgpRoundana'): 6, ('NgpOldBusStand', 'NgpBusStand'): 2, ('NgpOldBusStand', 'NgpRoundana'): 3, ('NgpRoundana', 'Thiruvarur'): 23, 
     ('NgpRoundana', 'VlkBusStand'): 9, ('VlkBusStand', 'Kameshvaram'):  7, ('VlkBusStand', 'Thirupundi'): 6, ('Kameshvaram', 'Vettaikaranirupu'): 8, ('Vettaikaranirupu', 'Sembodai'): 11, 
     ('Sembodai', 'Kariyapattinam'): 11,('Sembodai', 'Thoputhurai'): 8, ('Thoputhurai', 'VDMBusStand'):  3, ('Vaimedu', 'VDMBusStand'): 20, ('Vaimedu', 'Umbalacheri'): 16,('Vaimedu', 'Thiruthuraipoondi'): 19,
     ('Thiruthuraipoondi', 'Uthirangudi'):  12,('Thiruthuraipoondi', 'Ettugudi'):  20,('Umbalacheri', 'Kariyapattinam'):  6,('Uthirangudi', 'Thiruvarur'):  15,('Thirupundi', 'Thalainayar'):  10,('Thirupundi', 'Ettugudi'):  11,
     ('Ettugudi', 'Thalainayar'):  7,('Ettugudi', 'Thirukuvalai'):  6,('Thirukuvalai', 'Uthirangudi'):  8 })


r0 = RouteProblem('NgpRoundana', 'Thiruvarur', map=ngp_nearby_locations)
r1 = RouteProblem('NgpBusStand', 'Kameshvaram', map=ngp_nearby_locations)
r2 = RouteProblem('MyHouse', 'Sembodai', map=ngp_nearby_locations)
r3 = RouteProblem('Thoputhurai', 'Kariyapattinam', map=ngp_nearby_locations)
r4 = RouteProblem('VDMBusStand', 'Thiruthuraipoondi', map=ngp_nearby_locations)
r5 = RouteProblem('NgpBusStand', 'VDMBusStand', map=ngp_nearby_locations)
goal_state_path=breadth_first_search(r1)
print("GoalStateWithPath:{0}".format(goal_state_path))
path_states(goal_state_path) 
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))
```

## OUTPUT:
![Output](https://user-images.githubusercontent.com/75235747/166149276-c260f80b-c691-4915-ab74-2126c81fcb3f.JPG)
<br>
## SOLUTION JUSTIFICATION:
Route follow the minimum distance between locations using breadth-first search. As BFS traverses the tree “shallowest node first”, it would always pick the shallower branch until it reaches the solution.
## RESULT:
Thus an algorithm to find the route from the source to the destination point using breadth-first search is developed and executed successfully.

