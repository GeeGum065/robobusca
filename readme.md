# Labirinto — Navegação Manual e Busca Gulosa em C

Projeto composto por dois programas em C que simulam a navegação em um labirinto 5×5: um modo **manual** (controlado pelo usuário) e um modo **automático** com o algoritmo de **busca gulosa** (greedy best-first search).

---

## Estrutura do Projeto

```
.
├── labirinto.c       # Navegação manual pelo teclado
└── busca_gulosa.c    # Navegação automática por busca gulosa
```

---

## O Labirinto

O labirinto é uma grade 5×5 fixa, representada por caracteres:

```
. . . # .
. # . # .
. # . . .
. . . # G
. # . . .
```

| Símbolo | Significado         |
|---------|---------------------|
| `.`     | Célula livre        |
| `#`     | Parede (bloqueada)  |
| `G`     | Objetivo (linha 3, coluna 4) |
| `S`     | Posição atual do agente |

O agente começa sempre na posição `(0, 0)` (canto superior esquerdo).

---

## Heurística

Ambos os programas utilizam a **distância de Manhattan** como função heurística:

```
h(x, y) = |x - 3| + |y - 4|
```

onde `(3, 4)` é a posição do objetivo `G`.

---

## `labirinto.c` — Modo Manual

O usuário controla o agente via teclado, digitando um caractere por vez.

### Controles

| Tecla | Ação       |
|-------|------------|
| `w`   | Mover para cima    |
| `s`   | Mover para baixo   |
| `a`   | Mover para esquerda |
| `d`   | Mover para direita  |
| `q`   | Encerrar o programa |

A cada movimento válido, o programa exibe a distância de Manhattan atual até o objetivo. Movimentos para paredes ou fora dos limites são rejeitados com a mensagem `movimento invalido`.

### Como compilar e executar

```bash
gcc labirinto.c -o labirinto
./labirinto
```

---

## `busca_gulosa.c` — Modo Automático (Busca Gulosa)

O agente se move automaticamente a cada iteração, escolhendo sempre o vizinho adjacente (cima, baixo, esquerda, direita) que minimiza a distância de Manhattan até o objetivo — sem nenhuma entrada do usuário.

### Como compilar e executar

```bash
gcc busca_gulosa.c -o busca_gulosa
./busca_gulosa
```

---

##  Limitação Crítica: A Busca Gulosa NÃO Resolve Este Labirinto

> **O algoritmo de busca gulosa implementado não é capaz de encontrar o caminho até o objetivo `G` neste labirinto.**

### Por quê?

A busca gulosa segue sempre o vizinho de menor custo heurístico, **sem backtracking** (retrocesso). Quando o agente atinge uma célula da qual todos os vizinhos que reduziriam a distância são paredes ou já foram bloqueados, ele **fica preso em um ciclo ou em um beco sem saída**, nunca alcançando `G`.

No labirinto deste projeto, a disposição das paredes (`#`) faz com que o caminho guloso seja barrado antes de atingir o objetivo. Como o algoritmo não mantém histórico de células visitadas nem considera retroceder, ele entra em **loop infinito**.

### Exemplo do problema

A partir de certas posições, todos os movimentos que diminuem a distância Manhattan levam a paredes. O código testa a colisão *após* mover o agente, restaurando a posição anterior — mas como nenhum outro movimento é tentado, o programa apenas imprime `movimento invalido` repetidamente e trava.

### Como corrigir

Para garantir que o labirinto seja resolvido, seria necessário usar um algoritmo completo, como:

- **Busca em largura (BFS)** — garante o caminho mais curto em grafos sem peso
- **A\*** — combina custo real acumulado (`g`) com heurística (`h`), sendo completo e ótimo
- **Busca em profundidade com backtracking** — menos eficiente, mas completa

A busca gulosa pura só é adequada em ambientes sem becos sem saída ou quando a heurística é perfeita.

---

## Dependências

- Compilador C (GCC recomendado)
- Biblioteca padrão C (`stdio.h`, `stdlib.h`, `unistd.h`)
