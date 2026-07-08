# Benchmark scenarios for public transport network design/redesign problems
Examples of networks to be used on research concerning network design and network redesign and extension problems (sometimes also called line planning problems). These networks were used in "Maximal demand coverage transit network redesign with resource reutilization" by David Canca and Francisco Saldanha-da-Gama, published in the Journal of Transport Geography.
--------------------------------------------------------------------------------
[https://www.sciencedirect.com/science/article/pii/S0966692326002024](https://www.sciencedirect.com/science/article/pii/S0966692326002024)

**If this material is used for research purposes, please cite the original contribution:**

*David Canca, Francisco Saldanha-da-Gama, 2026, Maximal demand coverage transit network redesign with resource reutilization. Journal of Transport Geography, Volume 135, 104748.*

--------------------------------------------------------------------------------

The repository contains several networks, with different purposes. The first two networks are called **Canca-Saldanha 40-node DN** and **Canca-Saldanha 65-node DN**. The **"DN"** suffix refers to the way the passengers demand is considered (Demand in Nodes), in these first two scenarios the demand is supposed to be originated at network nodes (it could be considered that the nodes of the network are also acting as demand centroids). For each scenario, two OD matrices are provided, a) The first one corresponds to a daily average matrix (this demand matrix can be used to manage strategic approaches where there is no interest in specifically determine the frequency and fleet size of transport lines). b) The second one can be used to approach a tactical version of the design/redesign problems, correspond to a peak-hour demand matrix,  then it can be used to design transportation lines and determine frequencies and fleet size  of lines. Since the Network Design problem is a NP-Hard problem, with the purpose of running exact optimization MIP design models, a little version of the network with only 25 nodes is also included.

An enhanced version of the two networks are also included in the repository. The networks are called **Canca-Saldanha 40-node DC** and **Canca-Saldanha 65-node DC**, where the suffix **"DC"** refers to networks containing demand centroids different from the nodes of the network, which are connected using dummy links to the near network nodes. This versions are more general, they can be used to address network design/redesign/extension problems as well, but supposing that passengers depart from centroids and walk to the near network nodes in order to board transportation services (obviously only if lines traverse these near nodes).

### **Network 1** -- **Canca-Saldanha 40-node DN**

The network consider the existence of two metro fixed lines. The idea is the redesign of the transit network that initially consists of three bus lines. It is supposed that these bus lines were part of an old public transportation system that became obsolete after constructing the two metro lines and, as a consequence, a redesign of the transit system is required. The network can be used as an empty underlying network to formulate pure design problems from scratch or can be used, as in our publication, for redesigning or extending only a part of the network, while maintaining a set of fixed lines, in our case the metro lines.

The network is described as a set of nodes and a set of edges. The edges can be duplicated to work with a directed graph, generating two arcs from each edge, one per direction.

```python
ScaleNetwork = 400  #One unit in the node coordinates dictionary is equivalent to 400 meters
ScaleODMatrix=4 #The matrix that appears below is multiplied by "ScaleMatrix" to obtain a reasonable number of daily trips in the network.

ListOfNodes = [i for i in range(1, 41)]  #40 Nodos
NodesCoord = {  # Coordinates before being scaled
    1:  [0, 3],    2: [2, 3],   3: [3, 3],   4: [6, 3],   5: [0, 2],   6: [2, 2],
    7:  [3, 2],    8: [6, 2],   9: [0, 0],  10: [2, 0],  11: [3, 0],  12: [6, 0],
    13: [6, -2],  14: [6, -4], 15: [6, -7], 16: [8, -7], 17: [8, -5], 18: [8, -3],   
    19: [8, -2],  20: [9, 0],  21: [8, 2],  22: [10, 2], 23: [11, 0], 24: [11, -2],   
    25: [10, -4], 26: [5,-8],  27:[5,-10],  28:[5,-12],  29:[7,-9],   30:[7,-11],
    31:[9,-9],    32:[9,-11],  33:[11,-7],  34:[13,-7],  35:[12,-9],  36:[12,-5],
    37:[2,-7],    38:[2,-5],   39:[4,-6],   40:[4,-4]
}

# Scaling the network to meters
for nodo in NodesCoord:
    NodesCoord[nodo][0] = NodesCoord[nodo][0] * ScaleNetwork
    NodesCoord[nodo][1] = NodesCoord[nodo][1] * ScaleNetwork


ListOfEdges = [
    (1, 2),     (2, 3),   (3, 4),  (5, 6),   (6, 7),   (7, 8),  (9, 10),  (10, 11), (11, 12),
    (1, 5),     (2, 6),   (3, 7),  (4, 8),   (5, 9),  (6, 10),  (7, 11),   (8, 12), (11, 13),
    (12, 13), (13, 14), (14, 15), (15, 16), (15, 17), (16, 17), (14, 17), (14, 19), (17, 18), 
    (18, 19), (17, 25), (18, 25), (24, 25), (19, 24), (12, 19), (19, 20), (20, 24), (12, 20),
    (20, 23), (23, 24),  (8, 21), (21, 22), (20, 21), (20, 22), (22, 23), (38, 40), (14, 40),
    (39, 40), (37, 38), (37, 39), (15, 39), (26, 39), (26, 37), (38, 39), (15, 26), (26, 27),
    (27, 28), (26, 29), (27, 29), (27, 30), (28, 30), (15, 29), (29, 30), (16, 29), (29, 31),
    (29, 32), (30, 32), (16, 31), (31, 32), (32, 35), (31, 35), (31, 33), (16, 33), (33, 35),
    (33, 34), (34, 35), (33, 36), (34, 36), (24, 36), (25,36)
    ]
```

Generating arcs, real scaled length of edges, real scaled length of arcs.

```python
# List of network arcs
ListOfArcs=[]
for (i,j) in ListOfEdges:
    ListOfArcs.append((i,j))
    ListOfArcs.append((j,i))

import math

# Length of Edges (after scaling coordinates of nodes)
LengthOfdEdges={}
for (i,j) in ListOfEdges:
    LengthOfdEdges[i,j]=math.sqrt((NodosCoord[i][0]-NodosCoord[j][0])**2 +(NodosCoord[i][1]-NodosCoord[j][1])**2)
        # Calculamos solo en un sentido

LengthofArcs={}
for (i,j) in ListOfEdges:
    LengthofArcs[i,j]=LengthOfdEdges[i,j]
    LengthofArcs[j,i]=LengthOfdEdges[i,j]

# Arcs + Length of arcs  into tuples for represntation purposes (using Networkx)
# Lo pasamos a lista para poder usar networkx y representar la red
ListofArcsPlusLong=[(i,j,LengthofArcs[i,j]) for (i,j) in ListOfArcs]
```

Initial set of lines (the transportation system before constructing the metro systems) and fixed metro lines taking part of the network after the redesign. 

```python
# Old transit lines (To be redesigned)
OldBusNetworkLines={
    1:[1,2,3,4,8,12,13,14], 
    2:[37,38,40,14,17,16,33,34], 
    3:[28,30,29,16,17,25,24,23]
}

# Fixed lines (optional)
FixedLinea = { # In our case metro lines that will take part of the final transportation system. They cannot be changed.
    1:[6,7,8,21,20,24,36,33,31], 
    2:[3,7,11,12,19,18,17,15,39,38]
}
```
The daily OD demand matrix.

```python
ListaOD=[
# 1   2  3      4   5  6    7   8   9   10  11   12  13  14  15 16  17  18  19  20  21  22  23  24  25
[0,	15,	14,	7,	8,	0,	14,	16,	1,	19,	19,	8,	10,	16,	8,	16,	10,	9,	18,	10,	17,	4,	7,	15,	15,	5,	15,	14,	10,	10,	16,	15,	16,	2,	2,	12,	11,	12,	17,	6],
[14,	0,	19,	4,	0,	16,	8,	0,	10,	18,	17,	6,	18,	3,	2,	6,	15,	17,	16,	4,	4,	14,	0,	13,	15,	6,	13,	1,	9,	16,	4,	18,	13,	9,	2,	1,	10,	11,	19,	7],
[18,	12,	0,	18,	4,	13,	24,	6,	12,	10,	36,	36,	6,	4,	21,	4,	39,	18,	45,	5,	12,	14,	14,	10,	18,	3,	4,	5,	15,	35,	10,	12,	10,	19,	4,	12,	6,	41,	5,	12],
[11,	3,	17,	0,	4,	1,	3,	19,	1,	8,	5,	2,	16,	1,	14,	9,	4,	0,	14,	11,	2,	18,	18,	13,	1,	10,	19,	13,	6,	12,	11,	2,	15,	1,	0,	3,	17,	1,	5,	4],
[3,	9,	13,	1,	0,	4,	13,	16,	3,	6,	1,	8,	1,	16,	4,	17,	6,	4,	5,	12,	0,	12,	9,	14,	17,	4,	4,	14,	10,	6,	2,	0,	2,	3,	4,	12,	17,	3,	6,	19],
[17,	14,	5,	11,	6,	0,	36,	40,	10,	9,	6,	9,	3,	13,	5,	11,	12,	14,	9,	16,	46,	19,	17,	18,	4,	8,	1,	9,	15,	9,	46,	10,	47,	7,	18,	31,	5,	10,	16,	10],
[10,	19,	38,	9,	9,	29,	0,	33,	3,	11,	42,	44,	3,	14,	29,	14,	16,	7,	17,	34,	39,	15,	0,	3,	2,	18,	15,	19,	7,	29,	7,	13,	17,	18,	13,	4,	3,	44,	5,	3],
[3,	6,	11,	4,	8,	28,	36,	0,	9,	16,	10,	6,	3,	11,	10,	2,	7,	7,	6,	21,	26,	6,	0,	38,	1,	3,	16,	0,	15,	8,	45,	11,	26,	8,	3,	22,	7,	10,	14,	14],
[17,	11,	4,	6,	17,	0,	19,	3,	0,	0,	9,	2,	10,	5,	13,	11,	13,	9,	4,	8,	15,	14,	10,	11,	17,	3,	19,	8,	0,	13,	4,	0,	10,	8,	10,	1,	4,	6,	3,	7],
[12,	1,	17,	5,	11,	6,	14,	9,	17,	0,	4,	11,	4,	17,	10,	17,	13,	16,	8,	12,	13,	7,	15,	13,	19,	5,	4,	7,	7,	5,	4,	17,	5,	10,	6,	10,	12,	18,	18,	15],
[0,	16,	23,	7,	10,	11,	39,	7,	19,	10,	0,	46,	14,	18,	48,	18,	45,	1,	43,	11,	13,	7,	9,	14,	12,	5,	16,	8,	8,	35,	11,	12,	10,	5,	18,	13,	12,	31,	8,	16],
[11,	9,	17,	14,	8,	12,	22,	9,	2,	18,	17,	0,	7,	4,	17,	18,	43,	11,	48,	6,	5,	10,	2,	9,	12,	1,	17,	9,	11,	18,	13,	19,	11,	8,	2,	10,	13,	25,	3,	0],
[13,	1,	7,	18,	1,	6,	17,	4,	10,	12,	11,	7,	0,	4,	6,	16,	6,	14,	8,	8,	11,	9,	4,	12,	1,	11,	4,	11,	13,	18,	0,	13,	10,	2,	15,	19,	1,	6,	14,	17],
[13,	0,	13,	4,	1,	1,	14,	4,	13,	4,	0,	0,	9,	0,	12,	5,	8,	18,	2,	18,	7,	19,	16,	3,	1,	9,	19,	19,	0,	4,	17,	7,	15,	7,	2,	2,	11,	18,	18,	2],
[13,	17,	32,	14,	7,	9,	40,	8,	1,	4,	40,	25,	0,	7,	0,	4,	33,	18,	20,	6,	5,	0,	7,	15,	6,	7,	13,	13,	3,	42,	8,	18,	11,	12,	17,	5,	5,	36,	4,	14],
[6,	13,	16,	11,	11,	15,	7,	9,	7,	3,	5,	2,	2,	8,	3,	0,	6,	5,	18,	12,	9,	1,	9,	1,	2,	0,	5,	5,	9,	0,	12,	8,	19,	13,	8,	18,	9,	15,	3,	9],
[5,	7,	21,	12,	14,	14,	37,	7,	4,	11,	40,	46,	1,	1,	42,	3,	0,	8,	17,	12,	5,	19,	18,	13,	13,	18,	5,	0,	6,	23,	8,	4,	11,	13,	19,	8,	18,	29,	2,	3],
[13,	8,	12,	4,	15,	1,	6,	4,	7,	8,	15,	8,	15,	7,	14,	13,	17,	0,	12,	18,	18,	14,	5,	6,	1,	5,	13,	0,	15,	18,	10,	17,	19,	13,	13,	18,	1,	2,	18,	17],
[5,	4,	48,	13,	18,	11,	32,	7,	12,	0,	32,	31,	17,	1,	48,	18,	24,	12,	0,	5,	13,	3,	9,	10,	10,	16,	4,	3,	18,	36,	8,	6,	6,	10,	14,	6,	19,	31,	8,	16],
[5,	15,	11,	17,	9,	43,	24,	45,	0,	0,	9,	5,	15,	8,	13,	4,	11,	9,	14,	0,	45,	17,	15,	27,	16,	18,	10,	10,	3,	9,	25,	19,	42,	16,	16,	49,	19,	7,	9,	15],
[3,	1,	7,	3,	2,	24,	48,	44,	9,	15,	12,	5,	0,	16,	14,	5,	7,	1,	7,	48,	0,	14,	5,	31,	16,	4,	18,	9,	10,	13,	44,	1,	21,	3,	15,	41,	15,	7,	5,	13],
[11,	13,	6,	5,	19,	7,	1,	5,	18,	9,	4,	9,	1,	18,	4,	1,	16,	13,	2,	19,	13,	0,	15,	8,	19,	13,	13,	19,	1,	1,	2,	2,	15,	4,	6,	10,	9,	0,	1,	4],
[4,	1,	11,	11,	11,	16,	7,	6,	7,	16,	11,	9,	13,	1,	12,	9,	19,	17,	15,	19,	8,	17,	0,	2,	3,	3,	4,	6,	11,	4,	13,	2,	6,	5,	17,	12,	7,	11,	1,	5],
[14,	5,	5,	11,	1,	46,	31,	16,	0,	13,	6,	12,	9,	8,	9,	3,	13,	4,	13,	42,	27,	11,	13,	0,	1,	14,	0,	16,	2,	14,	29,	11,	21,	13,	15,	24,	9,	8,	0,	19],
[0,	6,	7,	6,	12,	8,	9,	0,	19,	12,	5,	6,	7,	7,	4,	0,	8,	17,	3,	13,	15,	2,	6,	12,	0,	19,	4,	13,	6,	16,	7,	18,	15,	18,	16,	16,	9,	4,	16,	15],
[14,	1,	9,	19,	14,	14,	14,	16,	12,	11,	1,	1,	16,	3,	1,	7,	10,	3,	15,	6,	2,	5,	17,	14,	13,	0,	16,	16,	12,	0,	16,	19,	12,	10,	9,	6,	1,	5,	6,	13],
[7,	8,	4,	9,	11,	13,	13,	19,	3,	11,	17,	10,	4,	5,	7,	11,	3,	13,	6,	19,	1,	6,	3,	7,	3,	4,	0,	10,	16,	16,	17,	12,	16,	11,	15,	8,	3,	2,	19,	19],
[14,	14,	4,	12,	17,	10,	11,	13,	3,	1,	6,	18,	2,	18,	4,	9,	12,	0,	12,	5,	1,	13,	4,	17,	10,	8,	18,	0,	17,	19,	9,	19,	5,	18,	4,	11,	11,	17,	3,	3],
[15,	19,	11,	16,	19,	14,	19,	4,	4,	10,	1,	9,	1,	3,	16,	9,	12,	1,	0,	0,	10,	11,	9,	7,	6,	19,	14,	0,	0,	5,	2,	0,	19,	12,	10,	15,	13,	17,	13,	12],
[14,	11,	41,	8,	16,	5,	40,	10,	8,	4,	49,	36,	16,	17,	41,	9,	36,	9,	18,	7,	11,	6,	13,	14,	9,	14,	16,	9,	0,	0,	14,	7,	14,	9,	15,	10,	19,	41,	11,	17],
[7,	18,	7,	0,	0,	47,	49,	34,	2,	15,	6,	7,	5,	10,	7,	6,	12,	4,	5,	17,	17,	12,	15,	23,	0,	14,	1,	14,	9,	11,	0,	6,	25,	0,	3,	47,	17,	7,	14,	18],
[10,	1,	1,	9,	9,	15,	2,	16,	5,	14,	15,	5,	0,	11,	13,	6,	14,	19,	1,	18,	12,	17,	18,	9,	7,	15,	9,	11,	13,	19,	0,	0,	3,	2,	17,	2,	3,	5,	17,	15],
[12,	1,	8,	7,	14,	21,	43,	16,	18,	1,	14,	13,	15,	12,	13,	18,	8,	6,	5,	19,	20,	11,	13,	34,	9,	10,	16,	9,	5,	7,	19,	7,	0,	9,	6,	40,	13,	13,	13,	16],
[0,	9,	19,	15,	8,	16,	14,	6,	6,	15,	2,	3,	18,	11,	0,	13,	19,	8,	5,	12,	19,	8,	9,	6,	18,	17,	11,	14,	12,	1,	7,	7,	7,	0,	7,	16,	13,	1,	3,	12],
[4,	12,	19,	0,	8,	8,	7,	18,	6,	0,	6,	19,	8,	6,	17,	5,	1,	8,	9,	5,	15,	15,	1,	14,	12,	0,	12,	8,	5,	0,	4,	6,	2,	4,	0,	14,	11,	14,	1,	3],
[13,	15,	7,	18,	6,	26,	19,	44,	9,	11,	11,	8,	13,	1,	8,	10,	14,	0,	5,	39,	39,	13,	13,	39,	1,	0,	1,	12,	19,	12,	22,	6,	31,	3,	7,	0,	14,	8,	5,	10],
[3,	4,	7,	8,	4,	18,	2,	13,	14,	8,	6,	16,	12,	8,	1,	3,	9,	13,	19,	13,	2,	12,	10,	13,	18,	9,	18,	11,	5,	10,	10,	8,	3,	9,	9,	2,	0,	0,	1,	16],
[2,	0,	41,	4,	8,	5,	47,	8,	19,	9,	26,	47,	12,	6,	21,	2,	16,	19,	31,	8,	10,	17,	14,	6,	8,	6,	14,	6,	1,	34,	10,	13,	12,	2,	0,	5,	16,	0,	1,	7],
[6,	10,	0,	8,	19,	14,	18,	8,	8,	6,	4,	6,	13,	6,	16,	15,	1,	12,	9,	14,	15,	17,	17,	19,	8,	6,	6,	13,	0,	16,	19,	11,	17,	11,	1,	2,	16,	10,	0,	19],
[3,	1,	1,	5,	7,	2,	5,	4,	9,	10,	18,	11,	12,	8,	7,	2,	5,	19,	15,	15,	11,	14,	1,	5,	2,	0,	14,	1,	4,	17,	16,	13,	3,	17,	11,	14,	9,	8,	2,	0]]
```

The real OD matrix for the complete day.

```python
# Average daily OD demand Matrix after scaling
OD={}
for i in LNodos:
    for j in LNodos:
        OD[i,j]=ListaOD[i-1][j-1]  * ScaleMatrix
```

Now, all the elements are defined, edges and arcs, nodes, OD demand Matrix. Recall that the 40 nodes are considered to be the origin and destination of trips.
---------------------------------------------------------------------------







