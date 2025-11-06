# Otimização de Rotas para Sabor Express: Uma Solução Híbrida de IA

## 1. Descrição do Problema, Desafio Proposto e Objetivos

A **Sabor Express**, uma pequena empresa de delivery de alimentos, enfrenta desafios críticos na gestão de suas entregas durante horários de pico. A roteirização manual resulta em rotas ineficientes, atrasos, aumento de custos com combustível e, consequentemente, insatisfação dos clientes.

O desafio proposto é desenvolver uma **solução inteligente baseada em algoritmos de Inteligência Artificial** para otimizar as rotas de entrega.

### Objetivos

1.  **Agrupamento Eficiente:** Utilizar clustering para agrupar pedidos próximos, otimizando o trabalho de cada entregador.
2.  **Roteirização Otimizada:** Implementar um algoritmo de busca para encontrar o menor caminho entre os múltiplos pontos de entrega dentro de cada grupo.
3.  **Redução de Custos:** Minimizar a distância total percorrida, impactando diretamente na redução de custos operacionais e tempo de entrega.
4.  **Documentação Completa:** Apresentar a solução de forma clara, com fundamentação teórica e análise de resultados.

## 2. Explicação Detalhada da Abordagem Adotada

A solução adota uma **abordagem híbrida** em duas fases, combinando aprendizado não supervisionado e busca heurística para resolver o problema de roteamento de veículos (VRP) com múltiplos pontos de entrega.

### Fase 1: Agrupamento de Entregas (Clustering)

O problema de rotear todos os 20 pedidos para um único entregador é complexo. A estratégia inicial é dividir o problema em subproblemas menores e mais gerenciáveis.

*   **Algoritmo:** **K-Means** (Aprendizado Não Supervisionado).
*   **Função:** Agrupar os pedidos em $K$ clusters, onde $K$ é o número de entregadores disponíveis (neste caso, $K=4$). O K-Means garante que os pedidos dentro de um mesmo cluster estejam geograficamente próximos, minimizando o tempo de deslocamento entre eles.

### Fase 2: Otimização da Rota (Busca Heurística)

Para cada cluster (rota de um entregador), o problema se transforma em um **Problema do Caixeiro Viajante (TSP)** de pequeno porte: encontrar a sequência de visitas que minimiza a distância total percorrida, começando e terminando na Base.

*   **Modelagem:** A cidade é modelada como um **Grafo Completo**, onde os nós são os locais de entrega e a Base, e o peso das arestas é a **Distância Euclidiana** entre os pontos (simulando o custo de deslocamento).
*   **Algoritmo de Busca:** **A\*** (A-Star). O A\* é implementado para encontrar o caminho mais curto entre quaisquer dois pontos no grafo, servindo como a função de custo exata ($g(n)$) para a heurística de roteamento.
*   **Heurística TSP:** **Nearest Neighbor (Vizinho Mais Próximo)**. Para resolver o TSP de forma eficiente (NP-hard), utilizamos esta heurística simples e rápida. O entregador, a partir de sua posição atual, sempre se move para o ponto de entrega **não visitado mais próximo** até que todos os pontos do cluster sejam atendidos, retornando então à Base.

## 3. Algoritmos Utilizados

| Algoritmo | Tipo | Função no Projeto |
| :--- | :--- | :--- |
| **K-Means** | Aprendizado Não Supervisionado | Agrupamento geográfico dos 20 pedidos em 4 clusters (rotas). |
| **A\*** (A-Star) | Busca Heurística (Grafo) | Cálculo do custo (distância) entre quaisquer dois pontos no grafo, garantindo o menor caminho local. |
| **Nearest Neighbor** | Heurística de Otimização (TSP) | Determinação da sequência de visitas dentro de cada cluster para minimizar a distância total da rota. |

## 4. Diagrama do Grafo/Modelo Usado na Solução

O diagrama abaixo ilustra o resultado da otimização para $K=4$ entregadores. A Base (ponto 0) está localizada em (0,0). Cada cor representa um cluster (rota) diferente, e os números ao longo das rotas indicam a sequência de visitas determinada pela heurística Nearest Neighbor.

![Diagrama de Rotas Otimizadas](outputs/optimized_routes.png)

<img width="671" height="542" alt="image" src="https://github.com/user-attachments/assets/553f2c1f-636c-42a4-a85c-718431c3ddfd" />


## 5. Análise dos Resultados, Eficiência e Limitações

### Resultados e Eficiência

*   **Distância Total Otimizada (K=4):** **43.32** unidades euclidianas.
*   **Agrupamento:** O K-Means agrupou os 20 pedidos de forma eficiente, resultando em 4 rotas com 4, 5, 6 e 5 entregas, respectivamente. A visualização confirma que os clusters são geograficamente coesos.
*   **Roteirização:** A combinação A\* + Nearest Neighbor forneceu uma solução rápida e de alta qualidade. A rota de cada entregador é logicamente sequencial, cobrindo uma área geográfica limitada, o que é altamente eficiente em termos de tempo de viagem.

| Rota | Entregas | Sequência (ID) | Distância (Euclidiana) |
| :--- | :--- | :--- | :--- |
| **1** | 4 | 0 -> 1 -> 3 -> 6 -> 8 -> 0 | 6.74 |
| **2** | 5 | 0 -> 10 -> 14 -> 12 -> 16 -> 19 -> 0 | 14.26 |
| **3** | 6 | 0 -> 2 -> 4 -> 9 -> 11 -> 7 -> 5 -> 0 | 8.83 |
| **4** | 5 | 0 -> 13 -> 17 -> 18 -> 20 -> 15 -> 0 | 13.49 |
| **Total** | **20** | - | **43.32** |

### Limitações Encontradas

1.  **Modelo de Grafo Simplificado:** O modelo utiliza a **Distância Euclidiana** (linha reta) como peso da aresta. Em um cenário real, o peso deveria ser o **tempo de viagem real** ou a **distância real da rua**, considerando o tráfego, sentido das vias e barreiras urbanas.
2.  **Heurística TSP:** O Nearest Neighbor é rápido, mas não garante a solução ótima global (o menor caminho absoluto). Para um TSP de pequeno porte, a diferença é pequena, mas para um número muito maior de entregas, algoritmos mais robustos (como 2-opt ou Algoritmos Genéticos) seriam necessários.
3.  **Ausência de Restrições:** A solução não considera restrições reais como capacidade do veículo, janelas de tempo de entrega (Time Windows) ou tempo de serviço em cada parada.

### Sugestões de Melhoria

1.  **Integração com API de Mapas:** Substituir a Distância Euclidiana por dados de distância/tempo de viagem de APIs como Google Maps ou OpenStreetMap, tornando o grafo mais realista.
2.  **Algoritmos TSP Mais Robustos:** Implementar o algoritmo **2-opt** para refinar as rotas geradas pelo Nearest Neighbor, buscando melhorias locais na sequência de visitas.
3.  **VRP com Restrições:** Adaptar a solução para um VRP completo, incorporando janelas de tempo e capacidade do veículo, utilizando técnicas como **Programação Linear Inteira Mista (MILP)** ou **Simulated Annealing**.

## 6. Instruções Claras para Execução do Projeto

O projeto está estruturado para ser executado em um ambiente Python 3.

### Estrutura do Repositório

```
sabor_express_optimization/
├── src/
│   └── route_optimizer.py  # Script principal com K-Means, A* e Nearest Neighbor
├── data/
│   └── deliveries.csv      # Dados simulados de entregas e Base
├── outputs/
│   ├── optimized_routes.png  # Diagrama de rotas gerado
│   ├── clustered_deliveries.csv # Resultados do K-Means
│   └── total_distance.txt    # Custo total da otimização
└── README.md               # Este arquivo
```

### Pré-requisitos

É necessário ter o Python 3 e as seguintes bibliotecas instaladas:

*   `pandas`
*   `numpy`
*   `scikit-learn`
*   `matplotlib`

### Instalação das Dependências

```bash
pip3 install pandas numpy scikit-learn matplotlib
```

### Execução do Projeto

Para executar a otimização e gerar os arquivos de saída (`outputs/`), execute o script principal:

```bash
https://colab.research.google.com/drive/19JZWxUAONOw8YU_j_7A_N75hZQvoXohD?usp=sharing
```

O script irá:
1.  Carregar os dados de `data/deliveries.csv`.
2.  Aplicar o K-Means para agrupar as entregas.
3.  Aplicar a heurística Nearest Neighbor (utilizando o A\* para cálculo de custo) para roteirizar cada cluster.
4.  Imprimir os resultados no console.
5.  Salvar o diagrama de rotas em `outputs/optimized_routes.png`.
6.  Salvar os dados de clustering em `outputs/clustered_deliveries.csv`.
