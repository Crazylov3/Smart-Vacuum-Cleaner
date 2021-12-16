<img src="https://github.com/Crazylov3/Smart-Vacuum-Cleaner/blob/main/Image/cleaner.png" width="300"> <img src="https://github.com/Crazylov3/Smart-Vacuum-Cleaner/blob/main/Image/cleaner_1.png" width="300"> <img src="https://github.com/Crazylov3/Smart-Vacuum-Cleaner/blob/main/Image/Cleaner_2.png" width="300">

**1. Presentation of the subject**

Topic: Implement a goal-based agent: Intelligent vacuum cleaner for rich people. 

Let’s say the family who owns this vacuum cleaner is very rich. On their floor, there is not only dust, but also jewels. The floor is of a size n x m with each tile have the following values
* 0 for a clean tile
* 1 for a tile with dust
* 2 for a tile with a jewel
* 3 for a tile with a jewel and dust
* 4 for a tile with an obstacle
A tile is clean if it has no dust or jewel or obstacle.

If tile status is 3, then the cleaner must pick up the jewelry first, to avoid sucking it. (0,0) is the top-left corner and is initially clean, and it contains a box. When picking up a jewel, the agent must bring it back to the box at (0,0) before continuing sucking the dust.

The agent must avoid the obstacles.

The vacuum cleaner agent can perform the following actions:
* Move up
* Move down
* Move left
* Move right
* Suck dust
* Pick up jewel
* Put jewel in the jewel box

**2. Description of the problem**
PEAS of our agent:

	* Performance Measures:
	    + Current state
	    + Position (of agent)
	    + Next stop
      
	* Environment: (Partially observable, deterministics)
	    + Floor
	    + Tiles
	    + Dust and jewel
      
	* Actuators:
	    + Turn left/right
	    + Move up/down
	    + Suck (dirts)
	    + Pick up (jewels)
	    + Put down (jewel to the box)
      
	* Sensors:
	    + Sensors to recognize environment
      
**3. Selecting the algorithms to be used for solving the problem**

While standing on a tile, the vacuum must decide what action to take. If the tile has dust only or has jewelry, the vacuum cleaner must suck dust and or pick up jewelry, then it has to decide which tile to move to.

For the sake of optimizing, before moving to the next tile, the vacuum should have queried environment status so as to:

* skip tiles that are already clean or tiles that have obstacles.
* find the nearest tile that has dust or jewelry and move to that tile

In order to do so, we represent the board in terms of graph and then use Floyd-Warshall algorithm to find the shortest path between any pair of tiles. By applying Floyd-Warshall algorithm, handling obstacles becomes easy.

Algorithm description:

1. Number tiles on the board consecutively from 1 to N, where N = m×n.
2. View the board in terms of graph, where 2 vertices i, j of the graph are adjacent if tiles i, j in the board are adjacent. 
3. Build a table matrix to save the cost travelling from tile i to tile j. Simultaneously, we maintain a table path where path[i, j] indicates the tile to traverse on the path from i to j in order to achieve the shortest path from i to j.
Initially, matrix[i, i] <- 0 for all tile i, as the cost to move from a tile to itself is 0.
              matrix[i, j] <- 1 for all adjacent tiles i and j.
              matrix[i, j] <- +∞ if i and j are not adjacent or at least one of them has an obstacle.
Initially, path[i, j] = j, that means the shortest path from i to j is to move directly from i to j.
4. Apply Floyd-Warshall algorithm to update path and matrix, i.e to calculate the shortest path between every pair of tiles (i, j) and the cost of that path.
5. Start cleaning: at each step when the vacuum cleaner has to determine the next tile to move to, we lookup in matrix and path to find the nearest tile. In case there are several candidates, we order them by clean level, say 1 -> 2 -> 3. Then we follow the corresponding route in path.

At step 4, we applied the Floyd-Warshall algorithm. It’s a method used to find shortest paths between any pair of vertices (u, v) in a weighted graph. In our circumstance, we use matrix as the cost table.  

The idea of the Floyd-Warshall algorithm is: For each vertex k, we consider every pair of vertices (u, v), we can minimize the path from u to v by the formula:

matrix[u, v] = min(matrix[u, v], matrix[u, k] + matrix[k, v])

That means: if the current path from u to v is longer than the path from u to k plus the path from k to v, then we remove that current path and accept the new path.

Function Floyd:
     *For each tile i in board:
     
        For each tile j in board:
        
            path[i, j] = j
            
     for each tile k as intermediary:
     
        for each tile i as origin:
        
            for each tile j as destination:
            
                if matrix[i, j] < matrix[i, k] + matrix[k, j]:
                
                    matrix[i, j] < matrix[i, k] + matrix[k, j]
                    
                    path[i, j] = path[i, k]     # move to k right after leave i*
                    
The advantage of Floyd-Warshall algorithm lies in the reusability because the shortest path between all pairs of vertices is calculated in advance. Therefore, querying what the next tile to move to and how to move to that tile requires minimal cost.

However, the Floyd-Warshall algorithm requires auxiliary time complexity of O(n^3) and space complexity of O(n^2). We have to maintain 2 tables matrix and path till the end of the program, especially when N is very large. Moreover, under no circumstance we have to use the entire table.

At step 5, by looking for the nearest tile viewed from the current position of the vacuum, we’re applying the greedy method. Though it may not turn out an optimal solution with lowest cost, it produces a high quality solution in a reasonable computation time, especially when the data set is large.
