Passos da implementação:

### Passo 1: Definindo a Classe Node
```java
class Node implements Comparable<Node> {
    int x, y;             // Posição do nó no grid
    int gCost;            // Custo real desde o início até este nó
    int hCost;            // Estimativa de custo do nó até o destino
    Node parent;          // Nó pai no caminho

    public Node(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int fCost() {
        return gCost + hCost;
    }

    @Override
    public int compareTo(Node other) {
        return Integer.compare(this.fCost(), other.fCost());
    }
}
```

- Aqui, definimos a classe `Node` para representar os nós no grid.
- `x` e `y` representam as coordenadas do nó no grid.
- `gCost` representa o custo real percorrido desde o início até este nó.
- `hCost` representa a estimativa de custo do nó até o destino.
- `parent` é uma referência ao nó pai no caminho.
- `fCost()` é um método para calcular a soma dos custos `gCost` e `hCost`.
- `compareTo()` é implementado para permitir a comparação de nós com base em seus custos `fCost`.

### Passo 2: Implementando o Algoritmo A*
```java
public class AStarAlgorithm {
    public static List<Node> findPath(int[][] grid, Node startNode, Node endNode) {
        PriorityQueue<Node> openSet = new PriorityQueue<>();
        Set<Node> closedSet = new HashSet<>();

        openSet.add(startNode);

        while (!openSet.isEmpty()) {
            Node current = openSet.poll();

            if (current.equals(endNode)) {
                return reconstructPath(current);
            }

            closedSet.add(current);

            for (Node neighbor : getNeighbors(current, grid)) {
                if (closedSet.contains(neighbor)) {
                    continue;
                }

                int tentativeGCost = current.gCost + distanceBetween(current, neighbor);

                if (tentativeGCost < neighbor.gCost || !openSet.contains(neighbor)) {
                    neighbor.gCost = tentativeGCost;
                    neighbor.hCost = distanceBetween(neighbor, endNode);
                    neighbor.parent = current;

                    if (!openSet.contains(neighbor)) {
                        openSet.add(neighbor);
                    }
                }
            }
        }

        return null; // Caminho não encontrado
    }
}
```

- Aqui, temos o método `findPath` que implementa o algoritmo A*.
- Usamos uma `PriorityQueue` chamada `openSet` para armazenar os nós a serem explorados.
- Usamos um `HashSet` chamado `closedSet` para armazenar os nós já explorados.
- Começamos adicionando o `startNode` à `openSet`.
- Iniciamos um loop enquanto a `openSet` não estiver vazia.
- Retiramos o nó com o menor custo `fCost` da `openSet`, chamado de `current`.
- Verificamos se `current` é o `endNode`, e se for, retornamos o caminho reconstruído.
- Adicionamos `current` ao `closedSet` para indicar que já foi explorado.
- Iteramos pelos vizinhos de `current` usando `getNeighbors`.
- Se um vizinho já estiver no `closedSet`, o ignoramos.
- Calculamos o `tentativeGCost`, que é o custo real até o vizinho a partir do `current`.
- Se o `tentativeGCost` for menor que o `gCost` do vizinho ou o vizinho não estiver na `openSet`, atualizamos seus custos e o adicionamos à `openSet`.

### Passo 3: Reconstruindo o Caminho
```java
private static List<Node> reconstructPath(Node node) {
    List<Node> path = new ArrayList<>();
    while (node != null) {
        path.add(node);
        node = node.parent;
    }
    Collections.reverse(path);
    return path;
}
```

- O método `reconstructPath` é usado para reconstruir o caminho a partir do nó final até o nó inicial.
- Começamos com o nó final e iteramos pelos nós pais, adicionando cada nó ao caminho.
- No final, revertemos a ordem do caminho usando `Collections.reverse`.

### Passo 4: Obtendo Vizinhos Válidos
```java
private static List<Node> getNeighbors(Node node, int[][] grid) {
    List<Node> neighbors = new ArrayList<>();
    int[][] directions = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} }; // Movimentos possíveis: cima, baixo, esquerda, direita

    for (int[] dir : directions) {
        int newX = node.x + dir[0];
        int newY = node.y + dir[1];

        if (isValid(newX, newY, grid)) {
            neighbors.add(new Node(newX, newY));
        }
    }

    return neighbors;
}

private static boolean isValid(int x, int y, int[][] grid) {
    return x >= 0 && x < grid.length && y >= 0 && y < grid[0].length && grid[x][y] == 0;
}
```

- `getNeighbors` gera os vizinhos válidos para um nó dado.
- Utilizamos um array de `directions` para representar os movimentos possíveis: cima, baixo, esquerda e direita.
- Iteramos pelas direções, calculamos as novas coordenadas para os vizinhos e verificamos se são válidas usando `isValid`.
- Se as coordenadas forem válidas, criamos um novo nó e o adicionamos à lista de vizinhos.

### Passo 5: Calculando Distância entre Nós
```java
private static int distanceBetween(Node a, Node b) {
    return Math.abs(a.x - b.x) + Math.abs(a.y - b.y);
}
```

- `distanceBetween` calcula a distância entre dois nós usando a métrica de Manhattan.
- A distância é a soma das diferenças absolutas das coordenadas `x` e `y`.

### Passo 6: Função Main para Teste
```java
public static void main(String[] args) {
    int[][] grid = {
        {0, 0, 0, 0},
        {0, 1, 1, 0},
        {0, 0, 0, 0},
        {0, 0, 0, 0}
    };

    Node startNode = new Node(0, 0);
    Node endNode = new Node(3, 3);

    List

<Node> path = findPath(grid, startNode, endNode);

    if (path != null) {
        System.out.println("Caminho encontrado:");
        for (Node node : path) {
            System.out.println("(" + node.x + ", " + node.y + ")");
        }
    } else {
        System.out.println("Caminho não encontrado.");
    }
}
```

- Na função `main`, criamos uma matriz `grid` que representa o ambiente.
- Definimos os nós de início e destino.
- Chamamos a função `findPath` para encontrar o caminho entre os nós.
- Se um caminho for encontrado, exibimos as coordenadas dos nós no caminho. Caso contrário, informamos que o caminho não foi encontrado.

Espero que esta explicação passo a passo tenha esclarecido como o algoritmo A* é implementado em Java e como cada parte do código contribui para a busca do caminho mais curto.

## Dúvidas que eu tive:

`O que é uma PriorityQueue e um HashSet?`

Vamos explicar o que é uma `PriorityQueue` e um `HashSet` e por que foram usados nesta implementação do algoritmo A*.

### `PriorityQueue`:
Uma `PriorityQueue` é uma estrutura de dados que armazena elementos de maneira ordenada, de acordo com uma ordem específica. Neste contexto, usamos a `PriorityQueue` para manter os nós candidatos a serem explorados pelo algoritmo A*. Os nós são ordenados com base no custo total estimado (fCost), onde o nó com menor fCost é retirado primeiro. Isso ajuda o algoritmo a explorar primeiro os nós que têm maior probabilidade de levar a um caminho mais curto.

No código, a `PriorityQueue<Node>` é usada para manter os nós que estão na lista de abertos (`openSet`), onde os nós com os menores custos estimados ficam no topo da fila. Isso ajuda a garantir que o algoritmo priorize a exploração dos nós mais promissores.

### `HashSet`:
Um `HashSet` é uma estrutura de dados que armazena elementos únicos, não ordenados. No contexto do algoritmo A*, usamos um `HashSet<Node>` para manter o controle dos nós que já foram explorados, ou seja, os nós que já foram adicionados à lista de fechados (`closedSet`). Isso evita que o algoritmo re-explore os mesmos nós, o que poderia levar a um desperdício de recursos computacionais e a um comportamento ineficiente.

No código, o `closedSet` é utilizado para verificar se um nó já foi explorado anteriormente. Se um nó já estiver no `closedSet`, o algoritmo A* não irá considerá-lo novamente.

Em resumo, a `PriorityQueue` ajuda a determinar a ordem de exploração dos nós com base em seus custos estimados, enquanto o `HashSet` ajuda a controlar quais nós já foram explorados, garantindo eficiência e evitando repetições na busca do caminho. Ambas as estruturas são cruciais para o funcionamento eficaz do algoritmo A*.
