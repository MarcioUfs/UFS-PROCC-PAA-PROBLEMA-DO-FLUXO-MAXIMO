# UFS PROCC PAA PROBLEMA DO FLUXO MAXIMO

Este repositório faz parte da atividade da disciplina de mestrado Projeto e Análise de Algoritmos sobre o "problema do fluxo máximo".

# INTRODUÇAO

O problema do fluxo máximo é uma ferramenta de modelagem capaz de apresentar uma grande variedade de problemas em áreas como logística, telecomunicações, engenharia de tráfego, inteligência artificial entre outras. inicialmente Ford e Fulkeson propuseram inicialmente o método geral de fluxo máximo em 1956, onde efetuava-se a escolha dos caminhos, nesse sentido considere um grafo direcionado ponderado com "pesos" de arestas que representam fluxos.
1. Em um grafo de fluxo temos dois vértices que se distinguem dos demais: **vértice inicial(fonte)** e **vértice final (sumidouro ou sorvedouro)**
2. Considere que os outros vértices podem ser chamados de conexões, essas conexões levam em consideração que o valor do fluxo de entrada ser igual ao valor do fluxo de saida, ou seja não há retenção de fluxo nesses vértices.
![Imagem grafo junções](https://github.com/MarcioUfs/UFS-PROCC-PAA-PROBLEMA-DO-FLUXO-MAXIMO/blob/main/1-fluxoJuncoes.png)
O grafo acima representa um exemplo simples, onde 's' e 't' são respectivamente o inicio e o fim e os vértices 'a', 'b' e 'c' são as junções (conexões), as setas indicam o sentido, ou seja, partindo de 's' até 'a' sendo 10 o valor do fluxo, note que 'a' recebe fluxo de 's' e 'c' e "repassa" esse valor para 't' num total de 13 que trata-se da soma dos valores recebidos por 'a'.

Edmonds e Karp, em 1972, aprimoraram a estratégia, substituindo a escolha arbitrária de caminhos por busca em largura (BFS).
![Imagem grafo BFS](https://github.com/MarcioUfs/UFS-PROCC-PAA-PROBLEMA-DO-FLUXO-MAXIMO/blob/main/2-BFSGrafoEdmondsKarp.png)

Exemplo do uso da busca em largura, onde a primeira elipse é o ponto inicial da busca em 's', partindo para seus vizinhos 'a' e 'b', seguindo para 'c' e por ultimo chegando a 't', percorrendo todos os caminhos possiveis. 

# PROBLEMA

O problema consiste em encontrar o fluxo máximo no código fonte abaixo. A matriz contida na linha 65 do codigo fonte
```
grafo = [
    [0, 16, 13, 0, 0, 0],
    [0, 0, 10, 12, 0, 0],
    [0, 4, 0, 0, 14, 0],
    [0, 0, 9, 0, 0, 20],
    [0, 0, 0, 7, 0, 4],
    [0, 0, 0, 0, 0, 0],
]
``` 
Tem sua representação em grafo como mostra a figura abaixo:
![Imagem grafo código fonte](https://github.com/MarcioUfs/UFS-PROCC-PAA-PROBLEMA-DO-FLUXO-MAXIMO/blob/main/3-grafoInicialCodigoFonte.png)

# RESOLUÇÃO

O algoritmo de Ford-Fulkerson pode levar até M iterações (fluxo aumenta pelo menos 1 unidade por iteração) então sua complexidade será O(M.|E|), ou seja, número de iterações vezes o número de arestas, isto pode se tornar complexo dependendo do número e peso das arestas e isso pode impactar na resolução do problema, partindo desse raciocínio será utilizado o algoritmo de Edmonds-Karp por ser um melhoramento o mesmo obtém a caminho de comprimento mínimo atraves de BFS (busca em largura) com a complexidade O(|E|^2|V|) quadrado do número de arestas vezes o número de vértices.

Resultado das iterações do algoritmo de Edmonds-Karp 
![Imagem resultado iterações codigo fonte](https://github.com/MarcioUfs/UFS-PROCC-PAA-PROBLEMA-DO-FLUXO-MAXIMO/blob/main/4-resultdoIteracoesCodigoFonte.png)

# CÓDIGO FONTE

```
from collections import deque

def bfs(residual, origem, destino, caminho):
    visitado = [False] * len(residual)
    fila = deque([origem])
    visitado[origem] = True

    while fila:
        u = fila.popleft()
        for v, capacidade in enumerate(residual[u]):
            if not visitado[v] and capacidade > 0:
                visitado[v] = True
                caminho[v] = u
                if v == destino:
                    return True
                fila.append(v)
    return False

def edmonds_karp(grafo, origem, destino):
    n = len(grafo)
    residual = [linha[:] for linha in grafo]
    caminho = [-1] * n
    fluxo_maximo = 0

    while bfs(residual, origem, destino, caminho):
        fluxo = float("inf")
        v = destino
        while v != origem:
            u = caminho[v]
            fluxo = min(fluxo, residual[u][v])
            v = caminho[v]

        v = destino
        while v != origem:
            u = caminho[v]
            residual[u][v] -= fluxo
            residual[v][u] += fluxo
            v = caminho[v]

        fluxo_maximo += fluxo

    return fluxo_maximo

grafo = [
    [0, 16, 13, 0, 0, 0],
    [0, 0, 10, 12, 0, 0],
    [0, 4, 0, 0, 14, 0],
    [0, 0, 9, 0, 0, 20],
    [0, 0, 0, 7, 0, 4],
    [0, 0, 0, 0, 0, 0],
]
origem = 0
destino = 5

print("Fluxo máximo:", edmonds_karp(grafo, origem, destino))
```
