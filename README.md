# Travelling Salesman Problem – AntRoute

A concise ACO-based solver for the Travelling Salesman Problem.

### Features
- **Ant Colony Optimization** core: probabilistic route construction and pheromone updating  
- Configurable parameters:  
  - **α**: pheromone influence  
  - **β**: heuristic (inverse distance) influence  
  - **ρ**: pheromone evaporation rate  
  - **n_ants**: number of ants per iteration  
  - **n_iters**: total iterations  
- Supports any fully connected weighted graph in CSV format
