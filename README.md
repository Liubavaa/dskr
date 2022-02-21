# Звіт

Лабораторну виконали:
 * Шевченко Іван
 * Олексюк Любомир

## Мета завдання

Завдання полягало в створенні двох алгоритмів для генерування дерева з мінімальною вагою по алгоритму Краскала та Прима. Створити графіки часу роботи: вісь X - кількість вершин графу, вісь Y - час обчислення дерева. Порівняти ефективність цих алгоритмів та зробити висновок.

## Середовище проведення експерименту

 * комп'ютер з характеристиками: 
   * 12 ядер 
   * тактова частота 3500 МГц
   * пам'ять 16 Гб
   * операційна система Linux
 * веб-сайт herokum


# tree.py

Модуль містить наступні функції:

## minimum_spanning_tree

Функція оприділяє який з алгоритмів використати

``` go
def minimum_spanning_tree(graph: nx.Graph, algorithm):
    if algorithm == 'prima':
        prima(graph)
    else:
        kruskal(graph)
```

## kruskal

Алгоритм Краскала, що для кожної вершини визначає її батька. Ця реалізація розбиття вершин на множини робить алгоритм швидше. Також дерево створюється максимально розгалуженим, в цьому випадку, під час процесу, потрібно менше рекурсивних запусків. 

``` go
def kruskal(graph):
    def get_root_parent(ver: int) -> int:
        if ver == parents[ver]:
            return ver
        return get_root_parent(parents[ver])

    answer_tree = nx.Graph()
    answer_tree.add_nodes_from(range(len(graph)))
    edges = list(graph.edges(data=True))
    edges.sort(key=lambda x: x[2]['weight'])
    parents = [-1] * len(graph)
    size = [1] * len(graph)
    for u in graph.nodes():
        parents[u] = u
    for u, v, data in edges:
        u_parent = get_root_parent(u)
        v_parent = get_root_parent(v)
        if u_parent == v_parent:
            continue
        if size[u_parent] < size[v_parent]:
            u_parent, v_parent = v_parent, u_parent
        parents[v_parent] = u_parent
        size[u_parent] += size[v_parent]
        answer_tree.add_edge(u, v, **data)
    return answer_tree
```

## prima

Алгортм Прима використовує пріоритетну чергу, вибираючи ребро найменшої ваги.

```go
def prima(graph):
    queue = []

    graph_edges = [[] for _ in range(len(graph))]
    for idx, (u, v, data) in enumerate(graph.edges(data=True)):
        graph_edges[u] += [(data['weight'], idx, v, u)]
        graph_edges[v] += [(data['weight'], idx, u, v)]
    edge_used = [False] * len(graph.edges())
    node_used = [False] * len(graph)

    nodes_got = 1
    nodes_to_get = len(graph)
    answer_graph = nx.Graph()
    answer_graph.add_node(0)
    for edge in graph_edges[0]:
        heapq.heappush(queue, edge)
        edge_used[edge[1]] = True
    node_used[0] = True

    while nodes_got < nodes_to_get:
        edge = heapq.heappop(queue)
        if node_used[edge[2]] and node_used[edge[3]]:
            continue
        if node_used[edge[2]]:
            node_used[edge[3]] = True
            nodes_got += 1
            answer_graph.add_edge(edge[2], edge[3], weight=edge[0])
            for u_edge in graph_edges[edge[3]]:
                if not node_used[u_edge[2]]:
                    heapq.heappush(queue, u_edge)
        else:
            node_used[edge[2]] = True
            nodes_got += 1
            answer_graph.add_edge(edge[2], edge[3], weight=edge[0])
            for u_edge in graph_edges[edge[2]]:
                if not node_used[u_edge[2]]:
                    heapq.heappush(queue, u_edge)

    return answer_graph
```

# prima_vs_kruskal

Модуль містить наступні функці:

## get_firebase

YOUR TEXT

```go
def get_firebase(algorithm):
    cred = credentials.Certificate('firebase_token.json')
    firebase_admin.initialize_app(cred)
    db = firestore.client()
    return db.collection('dm_lab15').document(algorithm)
```

## main

Функція вимірює час будування дерева для графа з певною кількість вершин і ймовірністю проведення ребра від однієї вершини до іншої. (опиши як відповідає за будування графіку)

```go
def main(algorithm):
    doc = get_firebase(algorithm)
    start_node = len(doc.get().to_dict())
    for nodes in tqdm(range(start_node, 1001, 15)):
        time_taken = 0
        for i in range(1000):
            graph = gnp_random_connected_graph(nodes, 0.5, False)

            start = time.time()
            tree.minimum_spanning_tree(graph, algorithm=algorithm)
            end = time.time()

            time_taken += end - start

        doc.update({
            str(nodes): (time_taken / 1000)
        })
```

# Результати

## Графіки

![telegram-cloud-photo-size-2-5229237784164809116-x](https://user-images.githubusercontent.com/92572643/154930513-729faf27-c6fa-470b-ae0e-6ba93646f2d1.jpg)

З графіку видно, що алгоритм Прима, при будь-якій кількості вершин і про середній ймовірності виникнення ребра між вершинами, працює ефективніше ніж алгоритм Краскала. Також очевидно, що на сервері алгоритми прцюють менш ефективно, тому в подальшому буде зображено тільки локальні графіки.


![telegram-cloud-photo-size-2-5229237784164809117-x](https://user-images.githubusercontent.com/92572643/154930835-85b794b0-0559-4794-83ec-dbf3b110e960.jpg)

При густині 0.1 різниці практично немає, але Прима працює швидше.


![telegram-cloud-photo-size-2-5229237784164809118-x](https://user-images.githubusercontent.com/92572643/154931021-c21b9590-4525-4b19-ae9d-700efc53fa50.jpg)

При густині 0.9 різниця ефективності значно зростає з збільшенням кількості вершин.

## Висновки

З грфіків видно, що алгоритм Прима при будь-якій кількості вершин працює ефективніше ніж алгоритм Краскала, що стає помітним від 200 вершин. Також, при збільшенні густини графу, ефективність Краскала в порівнянні з Прима значно зменшується. Складність обох алгоритмів складає O(m * log n), де m - кількість ребер, n - кількість вершин, проте з різними коефіцієнтами k.
