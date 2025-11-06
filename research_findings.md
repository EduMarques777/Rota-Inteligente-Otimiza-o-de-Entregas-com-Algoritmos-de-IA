# Fundamentação Teórica e Pesquisa de Casos

## 1. O Problema de Otimização de Rotas (VRP/TSP)

O desafio proposto para a "Sabor Express" é um caso clássico do **Problema de Roteamento de Veículos (VRP - Vehicle Routing Problem)**, especificamente uma variação do **Problema do Caixeiro Viajante (TSP - Traveling Salesperson Problem)**, onde o objetivo é encontrar a rota mais curta que visita um conjunto de pontos e retorna ao ponto de partida (ou, no caso de múltiplas entregas, a rota mais eficiente para um entregador visitar todos os pontos designados).

O problema é classificado como NP-hard, o que significa que encontrar a solução ótima em tempo polinomial é impraticável para um grande número de pontos. Por isso, a solução deve se basear em heurísticas e algoritmos de aproximação.

## 2. Abordagem Proposta: Híbrida (Clustering + Busca Heurística)

Para lidar com a complexidade e a necessidade de agrupar entregas em horários de pico, a solução será dividida em duas etapas principais:

1.  **Agrupamento de Entregas (Clustering):** Utilizar um algoritmo de aprendizado não supervisionado para agrupar pedidos próximos geograficamente, otimizando o trabalho de cada entregador.
2.  **Otimização da Rota (Busca Heurística):** Para cada grupo (cluster) de entregas, aplicar um algoritmo de busca para determinar a sequência ideal de visitas, minimizando a distância total percorrida.

### 2.1. Clustering: K-Means

O algoritmo **K-Means** é ideal para esta etapa. Ele agrupará os pontos de entrega em $K$ clusters, onde $K$ representa o número de entregadores disponíveis ou o número máximo de rotas que a empresa deseja criar.

*   **Entrada:** Coordenadas geográficas (latitude e longitude) de todos os pedidos.
*   **Saída:** $K$ grupos de pedidos, onde os pedidos dentro de um mesmo grupo estão geograficamente próximos.

### 2.2. Otimização da Rota: Algoritmo A* (A-Star)

Para encontrar o caminho mais curto dentro de cada cluster, o problema se assemelha ao TSP. Embora o TSP seja complexo, o algoritmo **A\*** (A-Star) é uma escolha poderosa para encontrar o caminho mais curto em um grafo, especialmente quando combinado com uma heurística bem definida.

*   **Representação do Grafo:** A cidade será modelada como um grafo, onde os nós são os locais de entrega (e o ponto de partida/base) e as arestas são as rotas entre eles. O peso da aresta será a distância ou o tempo de viagem.
*   **Heurística:** A heurística $h(n)$ pode ser a distância euclidiana (ou de Manhattan) restante até o próximo ponto de entrega não visitado.
*   **Função de Avaliação:** $f(n) = g(n) + h(n)$, onde $g(n)$ é o custo real (distância percorrida) do ponto inicial até o nó $n$.

Para o problema de múltiplos pontos (TSP), o A* pode ser adaptado para buscar a melhor permutação de nós a serem visitados, embora isso possa se tornar computacionalmente caro. Uma alternativa mais prática e eficiente para o TSP de pequeno porte (que é o caso de um cluster de entregas) é o uso de **heurísticas específicas para TSP**, como o algoritmo **Nearest Neighbor** ou o **2-opt**, que são mais adequados para otimizar a sequência de visitas.

**Decisão de Implementação:**
*   **Clustering:** K-Means.
*   **Otimização de Rota:** Para manter a aderência ao conteúdo sugerido (A*, BFS, DFS), implementaremos o **A\*** para encontrar o caminho mais curto entre **dois pontos** no grafo da cidade. Para resolver o problema de **múltiplos pontos (TSP)** dentro de um cluster, utilizaremos uma **heurística de aproximação (ex: Nearest Neighbor)**, que é a abordagem padrão em logística.

## 3. Casos de Estudo Reais

### 3.1. UPS ORION (On-Road Integrated Optimization and Navigation)

O sistema ORION da UPS é um exemplo de sucesso na aplicação de IA e otimização em larga escala.

*   **Tecnologia:** Combina algoritmos de otimização (baseados em heurísticas complexas e programação linear) com dados de tráfego em tempo real.
*   **Impacto:** Reduz milhões de milhas percorridas anualmente, economizando centenas de milhões de dólares em combustível e custos operacionais.
*   **Princípio:** O ORION resolve o VRP para a frota da UPS, determinando a sequência ideal de paradas para cada motorista, considerando restrições como janelas de tempo de entrega e regras de trânsito (como evitar viradas à esquerda).

### 3.2. Clustering e MILP em Logística

A combinação de clustering e Programação Linear Inteira Mista (MILP) é uma abordagem comum em logística.

*   **Clustering (K-Means/DBSCAN):** Usado para dividir a área de serviço em zonas menores e gerenciáveis.
*   **MILP:** Após o agrupamento, o MILP é usado para resolver o VRP/TSP para cada zona, garantindo que as restrições (capacidade do veículo, janelas de tempo) sejam estritamente respeitadas.

Esta pesquisa confirma a validade da abordagem híbrida (Clustering + Otimização de Rota) para o desafio da "Sabor Express". O próximo passo é modelar o grafo da cidade e os dados de entrega.
