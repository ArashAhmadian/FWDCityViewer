# FWDCityViewer

![DEMO](/pictures/DEMO.gif)
![GitHubStats](/pictures/githubStats1.png)<br/>

A map application written in C++ from a very low level API reading from an OpenStreetMaps database<br/>
Note: because this was a University project, the source code cannot be posted on GitHub, contact me if you wish to view the source code. 
## Features
### Technical Features
* Read data from Open Street Map database
* Read weather data using Open-Weather APT and parse relevant results
* Navigation route calculations between 2 points 
* Read data from OpenWeather API using libbCurl upon user request
* Navigation routes for a Car and a combination of Car and Walking
* Dijkstra's Algorithm with A* search optimization
  * (Single-Source, Single Destination) + (Multiple-Source, Single Destination) + (Single-Source, Multiple Destination) were all implemented for different versions of the pathfinding problem
* Travelling Salesman problem (given start, end position, must go to pickup stations and drop-off stations with specific carrying capacity in the most efficient way)
  * Computed various solutions that progressively approached the optimal solution
  * Implemented Stimulated annealing in addition to iterative improvement algorithms(2-Opt,3-Opt, and Intersection swap) and Multi-start to generate the best solution determined by QoR data. 
  * Optimized the runtime of this computation using various techniques including multi-threading and precomputing/caching pathfinding results
### Visual Features
* Display points of interests
* Display one way streets
* Dynamic street names (prevent visual crowding of the screen)
* Street names stay on the screen
* Dynamic loading of various visual elements based on zoom level
* High detail mode, smoothing out jagged road edges
* Distinction between main roads and non-main roads
* Distinction between geographic features such as water, parks, beaches, etc.
### User Interface and Interaction Features
* Intuitive map selection
* Weather data display 
* Auto-correct of search 
* Autocomplete of search if partially filled in
* Ability to see information: one way streets, subway stops, points of interest, weather data 
* Points of interest categorized and shown with custom icons
* Zooming in/out
* Panning around
* Search for a point of interest and locate on map
* Search for a street intersection and locate on map
* Click to get details about specific intersections
* Help text menu
* Error dialog popups
* Status bar with information and instructions of usage
* Can start/end navigation route at searched intersection
* Can start/end navigation route at searched point of interest`
* Highlight route as panned through, and display information in status bar
* Navigation instructions for transportation communicated in a clear and easy to understand way

## Algorithms
### Modelling the City as a Graph
Our application performs various calculations on city maps by modelling the map as a directed, weighted graph. Each node of the graph represents an intersection and each street is an edge connecting two intersections (two-way streets are modelled with two directed edges). The weights of each edge of the graph were based on actual data about the streets (e.g. the length of the street length and the driving speed limit).
### Pathfinding - Dijkstra's Algorithm + A* Search Optimization
The application models the start and end intersections as nodes in the city map's graph, and employs Dijkstra's Algorithm to find the shortest path.

Our implementation of Dijkstra's Algorithm is optimized as it uses A* Search to decide which routes to check first in its search for the shortest path. Since we know the physical locations of each intersection, we can determine the straight line distance between the start and end intersections. Then, using the maximum speed limit in the city, we can calculate the minimum travel time between any two intersections in the map. This allows the algorithm to pick the "most promising routes" first in its search, and also allows it to stop following a route if the route is taking it further away from the destintation.

The result is that the algorithm searches in approximately the "correct" direction, allowing it to find shortest paths much more quickly (especially when the start and end points are very far apart).
### Navigation Problem - Finding the Best Start Point
For the special problem of walking to a pickup intersection and then driving for the rest of the route, we made a slight modification to the algorithm above by introducing multiple start points (known as Multi-Source Dijkstra's Algorithm). Specifying multiple starts speeds up the computation since only one run of Dijkstra's Algorithm is required. The sources were all the points that someone could walk to within some prespecified amount of time. Once the optimized algorithm above finds the shortest path from one of these sources to the destination, we can use the path to determine the optimal point to get to the destination. 

### Travelling Salesman Problem - Progressively Finding Better Solutions
First, our application generates a few "good" initial solutions using a greedy algorithm (which starts at some initial point and always visits the closest points) and multi-start (running the greedy algorithm with various start points). Then, our application invokes various simulated annealing & various other iterative improvement algorithms including 2-opt and (random) intersection swap continuously on these initial solutions until it runs out of time (specified to be 50 seconds). These continuous cycles of iterative improvement progressively improve upon the initial solutions, and at the end we have a fairly good solution for the paths between all the points.

## Libraries Used
This application was built to run on a Linux machine (it's been tested on Debian and Ubuntu). It makes use of the following libraries:
* GTK+ 3
* OpenMP
* libCurl



