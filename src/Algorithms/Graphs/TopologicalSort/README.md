# Топологическая сортировка (англ. Topological sort)

__Топологическая сортировка__ — один из основных алгоритмов на графах, который применяется для решения множества более сложных задач. Задача топологической сортировки графа состоит в следующем: указать такой линейный порядок на его вершинах, чтобы любое ребро вело от вершины с меньшим номером к вершине с большим номером. 

Топологическая сотрировка возможна только для ориентированного ациклического графа.

Существует несколько способов топологической сортировки. Наиболее популярна и проста реализация с помощью [обхода в глубину](../DFS).

<br/>

__Сложность алгоритма:__ 

<p style='margin: 0px; text-align: center; font-weight: 700'>O(V+E)</p>

<small>***V, E – количество вершин и рёбер соответственно***</small>

_____
#### Принцип работы:

*Очень схож с принципом работы [алгоритма для проверки графа  на ацикличность](../DetectCycles/СheckForAcyclicity).*

На вход подаётся невзвешенная форма графа и список всех его вершин ***allGraphVertices***. 


__Для хранения данных потребуется еще 4 множества__:
- <small>*На уровне области видимости функции инициализируется*:</small>
    1. ***haveNoIncomingEdges*** – список вершин, которые не имеют входящих ребер (для каждой вершины из этого списка будем запускать DFS со стартом из нее);
    2. ***outputStack*** – стек вершин, прошедших топологическую сортировку.
- <small> *На уровне области видимости цикла, каждая итерация которого запускает DFS для вершины из ***haveNoIncomingEdges****, инициализируется:</small>
    1. ***vStack*** – множество вершин, которые надо обработать, представленное в виде [стека](../../../DataStructures/Stack);
    2. ***visited*** – список уже посещенных вершин. Каждая вершина в этом списке помечена серым или черным цветом. Серым, если еще не все пути из этой вершины рассмотрены, черным – если  рассмотрены уже все возможные пути из них.

<br/>

__Последовательность действий:__

1. Пробегаемся по списку смежности графа и получаем массив всех вершин, которые не имеют входящих ребер ***haveNoIncomingEdges***. Для каждой вершины из этого массива запустим DFS, начинающий свою работу с нее <small>*(если в ***haveNoIncomingEdges*** не попала ни одна вершина или мы встречаем ребро-петлю, значит граф имеет цикл(ы) и топологическая сортировка для него невозможна – возвращаем null)*</small>;

2. В цикле от каждой вершины из ***haveNoIncomingEdges*** (на каждой итерации это будет вершина ***start***) запускаем обход в глубину:

    1. Объявляем пустые множества ***vStack*** и ***visited***, помещаем в них ***start*** (в ***visited*** кладем ее под серым цветом);
    2. Крутимся в цикле, пока стек ***vStack*** не пуст:

        1. Забираем из стека последний элемент ***ТВ*** и получаем его прямых соседей, в которые есть путь из ***ТВ***;
        2. Крутимся по списку соседей в цикле и проверяем каждого соседа ***N*** на наличие в ***visited***:
        &nbsp; &nbsp;   - если ***N*** нет в ***visited*** вообще – добавляем ***N*** в ***vStack*** и в ***visited***, а цикл по соседям прерываем;
        &nbsp; &nbsp;   - если ***N*** есть в ***visited*** под серым цветом – значит мы наткнулись на цикл и топологическая сортировка для графа невозможна - возвращаем null;
        3. Если ни для одного из соседей не сработало ни одно из «если» в предыдущем пункте, то из ***vStack*** извлекаем ***ТВ***, в ***visited*** помечаем ***ТВ*** черным цветом и в ***outputStack*** кладем ***ТВ***;

3. Проверяем количество вершин, которые по итогу попали в ***outputStack***, и, если там оказались не все вершины графа – значит в нем есть цикл(ы) и топологической сортировки для него не существует – возвращаем null, иначе - переставляем вершины из ***outputStack*** в обратном порядке – это и есть топологическая сортировка переданного графа.

_____
#### Пример работы:

__Задача__: получить топологическую сортировку графа.

Кружками обозначены вершины, линиями – пути между ними (ребра графа). 
Вершины, по которым сейчас проходит путь DFS, помечены серым.
Вершины, для которых уже рассмотрены все возможные пути из них и они лежат в ***outputStack***, помечены черным крестиком. 
Рассматриваемая в данный момент вершина помечается ***ТВ***.

<p align='center'><img  width="500px" src='./images/1.webp' alt='граф'></p>

##### Шаг 1:

Инициализируем стек ***outputStack***, в который будут складываться топологически отсортированные вершины.
Из списка вершин, переданного в качестве параметра, ***allGraphVertices*** получаем список вершин, у которых нет входящих ребер – ***haveNoIncomingEdges***: [ 'A', 'G' ]. Забираем из этого списка последний элемент – вершину ***G*** и запускаем из нее обход в глубину.

Инициализируем  ***vStack*** – множество вершин, которые надо обработать, представленное в виде [стека](../../../DataStructures/Stack) и ***visited*** – список уже посещенных вершин, которые будут в нем помечаться серым или черным цветом.
Кладем в ***vStack*** – ***G***, в ***visited*** – ***G*** под серым цветом и все вершины из ***outputStack*** под черным (т.к. это первая итерация DFS ***outputStack*** пуст).

Запускаем цикл, который будет работать, пока ***vStack*** не опустеет.

Получаем из ***vStack*** последний элемент - вершину ***G*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***G***, в которые можно попасть из ***G*** – [ H ]. Последовательно обрабатываем вершины из этого списка (в данном случае только 1): вершины ***H*** нет в ***visited***, поэтому кладем ее в конец ***vStack*** и в ***visited*** под серым цветом.

<p align='center'><img  width="500px" src='./images/2.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ G, H ];
- В списке посещенных ***visited***: { G: grey, H: grey };
- В ***outputStack***: [ ];
- В ***haveNoIncomingEdges***: [ A ]

<br/>


##### Шаг 2:
Получаем из ***vStack*** последний элемент - вершину ***H*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***H***, в которые можно попасть из ***H*** – [ D, F ]. Последовательно обрабатываем вершины из этого списка: вершины ***D*** нет в ***visited***, поэтому кладем ее в конец ***vStack*** и в ***visited*** под серым цветом, а цикл по списку соседей прерываем.

<p align='center'><img  width="500px" src='./images/3.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ G, H, D ];
- В списке посещенных ***visited***: { G: grey, H: grey, D: grey };
- В ***outputStack***: [ ];
- В ***haveNoIncomingEdges***: [ A ]

<br/>


##### Шаг 3:
Получаем из ***vStack*** последний элемент - вершину ***D*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***D***, в которые можно попасть из ***D*** – [ ]. Так как у ***D*** нет ни одного «серого» или непосещенного соседа – удаляем из ***vStack*** вершину ***D***, в ***visited*** помечаем ее черным цветом и добавляем в ***outputStack***.

<p align='center'><img  width="500px" src='./images/4.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ G, H ];
- В списке посещенных ***visited***: { G: grey, H: grey, D: black };
- В ***outputStack***: [ D ];
- В ***haveNoIncomingEdges***: [ A ]

<br/>


##### Шаг 4:
Получаем из ***vStack*** последний элемент - вершину ***H*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***H***, в которые можно попасть из ***H*** – [ D, F ]. Последовательно обрабатываем вершины из этого списка: вершина ***D*** уже есть в ***visited*** под черным цветом – ее пропускаем, вершины ***F*** нет в ***visited***, поэтому кладем ее в конец ***vStack*** и в ***visited*** под серым цветом.

<p align='center'><img  width="500px" src='./images/5.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ G, H, F ];
- В списке посещенных ***visited***: { G: grey, H: grey, D: black, F: grey };
- В ***outputStack***: [ D ];
- В ***haveNoIncomingEdges***: [ A ]

<br/>


##### Шаг 5:
Получаем из ***vStack*** последний элемент - вершину ***F*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***F***, в которые можно попасть из ***F*** – [ ]. Так как у ***F*** нет ни одного «серого» или непосещенного соседа – удаляем из ***vStack*** вершину ***F***, в ***visited*** помечаем ее черным цветом и добавляем в ***outputStack***.

<p align='center'><img  width="500px" src='./images/6.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ G, H ];
- В списке посещенных ***visited***: { G: grey, H: grey, D: black, F: black };
- В ***outputStack***: [ D, F ];
- В ***haveNoIncomingEdges***: [ A ]

<br/>


##### Шаг 6:
Получаем из ***vStack*** последний элемент - вершину ***H*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***H***, в которые можно попасть из ***H*** – [ D, F ]. Так как у ***H*** нет ни одного «серого» или непосещенного соседа – удаляем из ***vStack*** вершину ***H***, в ***visited*** помечаем ее черным цветом и добавляем в ***outputStack***.

<p align='center'><img  width="500px" src='./images/7.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ G ];
- В списке посещенных ***visited***: { G: grey, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H ];
- В ***haveNoIncomingEdges***: [ A ]

<br/>


##### Шаг 7:
Получаем из ***vStack*** последний элемент - вершину ***G*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***G***, в которые можно попасть из ***G*** – [ H ]. Так как у ***G*** нет ни одного «серого» или непосещенного соседа – удаляем из ***vStack*** вершину ***G***, в ***visited*** помечаем ее черным цветом и добавляем в ***outputStack***.

<p align='center'><img  width="500px" src='./images/8.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ ];
- В списке посещенных ***visited***: { G: black, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H, G ];
- В ***haveNoIncomingEdges***: [ A ]

***DFS, стартующий из G, закончил свою работу. Переходим к вершине A.***

<br/>


##### Шаг 8:
Забираем из списка ***haveNoIncomingEdges*** последний элемент – вершину ***A*** и запускаем из нее обход в глубину.

Снова инициализируем  ***vStack*** и ***visited***.
Кладем в ***vStack*** – ***A***, в ***visited*** – ***A*** под серым цветом и все вершины из ***outputStack*** (***D, F, H, G***) под черным.

Запускаем цикл, который будет работать, пока ***vStack*** не опустеет.

Получаем из ***vStack*** последний элемент - вершину ***A*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***A***, в которые можно попасть из ***A*** – [ B, E ]. Последовательно обрабатываем вершины из этого списка: вершины ***B*** нет в ***visited***, поэтому кладем ее в конец ***vStack*** и в ***visited*** под серым цветом, а цикл по списку соседей прерываем.

<p align='center'><img  width="500px" src='./images/9.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ A, B ];
- В списке посещенных ***visited***: { A: grey; B: grey; G: black, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H, G ];
- В ***haveNoIncomingEdges***: [ ]

<br/>


##### Шаг 9:
Получаем из ***vStack*** последний элемент - вершину ***B*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***B***, в которые можно попасть из ***B*** – [ C ]. Последовательно обрабатываем вершины из этого списка (в данном случае только 1): вершины ***C*** нет в ***visited***, поэтому кладем ее в конец ***vStack*** и в ***visited*** под серым цветом.

<p align='center'><img  width="500px" src='./images/10.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ A, B, C ];
- В списке посещенных ***visited***: { A: grey; B: grey; C: grey; G: black, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H, G ];
- В ***haveNoIncomingEdges***: [ ]

<br/>


##### Шаг 10:
Получаем из ***vStack*** последний элемент - вершину ***C*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***C***, в которые можно попасть из ***C*** – [ D ]. Так как у ***C*** нет ни одного «серого» или непосещенного соседа – удаляем из ***vStack*** вершину ***C***, в ***visited*** помечаем ее черным цветом и добавляем в ***outputStack***.

<p align='center'><img  width="500px" src='./images/11.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ A, B ];
- В списке посещенных ***visited***: { A: grey; B: grey; C: black; G: black, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H, G, C ];
- В ***haveNoIncomingEdges***: [ ]

<br/>


##### Шаг 11:
Получаем из ***vStack*** последний элемент - вершину ***B*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***B***, в которые можно попасть из ***B*** – [ C ]. Так как у ***B*** нет ни одного «серого» или непосещенного соседа – удаляем из ***vStack*** вершину ***B***, в ***visited*** помечаем ее черным цветом и добавляем в ***outputStack***.

<p align='center'><img  width="500px" src='./images/12.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ A ];
- В списке посещенных ***visited***: { A: grey; B: black; C: black; G: black, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H, G, C, B ];
- В ***haveNoIncomingEdges***: [ ]

<br/>


##### Шаг 12:
Получаем из ***vStack*** последний элемент - вершину ***A*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***A***, в которые можно попасть из ***A*** – [ B, E ]. Последовательно обрабатываем вершины из этого списка: вершина ***B*** уже есть в ***visited*** под черным цветом – ее пропускаем, вершины ***E*** нет в ***visited***, поэтому кладем ее в конец ***vStack*** и в ***visited*** под серым цветом.

<p align='center'><img  width="500px" src='./images/13.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ A, E ];
- В списке посещенных ***visited***: { A: grey; E: grey; B: black; C: black; G: black, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H, G, C, B ];
- В ***haveNoIncomingEdges***: [ ]

<br/>


##### Шаг 13:
Получаем из ***vStack*** последний элемент - вершину ***E*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***E***, в которые можно попасть из ***E*** – [ C ]. Так как у ***E*** нет ни одного «серого» или непосещенного соседа – удаляем из ***vStack*** вершину ***E***, в ***visited*** помечаем ее черным цветом и добавляем в ***outputStack***.

<p align='center'><img  width="500px" src='./images/14.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ A ];
- В списке посещенных ***visited***: { A: grey; E: black; B: black; C: black; G: black, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H, G, C, B, E ];
- В ***haveNoIncomingEdges***: [ ]

<br/>


##### Шаг 14:
Получаем из ***vStack*** последний элемент - вершину ***A*** – это рассматриваемая вершина (***ТВ***). Получаем список прямых соседей ***A***, в которые можно попасть из ***A*** – [ B, E ]. Так как у ***A*** нет ни одного «серого» или непосещенного соседа – удаляем из ***vStack*** вершину ***A***, в ***visited*** помечаем ее черным цветом и добавляем в ***outputStack***.

<p align='center'><img  width="500px" src='./images/15.webp' alt='граф'></p>

К концу шага:
- В списке вершин, которые нужно рассмотреть ***vStack*** [ ];
- В списке посещенных ***visited***: { A: black; E: black; B: black; C: black; G: black, H: black, D: black, F: black };
- В ***outputStack***: [ D, F, H, G, C, B, E, A ];
- В ***haveNoIncomingEdges***: [ ]

***DFS, стартующий из A, закончил свою работу, haveNoIncomingEdges пуст – все пути из вершин, не имеющих входящих ребер, рассмотрены.***

Переставляем вершины из ***outputStack*** в обратном порядке – это и есть топологическая сортировка переданного графа – возвращаем [ 'A', 'E', 'B', 'C', 'G', 'H', 'F', 'D' ].

_____
#### Источники:
+ [Habr](https://habr.com/ru/articles/100953/)
+ [Habr](https://habr.com/ru/companies/otus/articles/499138/)
+ [Youtube video](https://www.youtube.com/watch?v=o0P8oNXoA_w)
+ [Algorithmica](https://ru.algorithmica.org/cs/graph-traversals/topological-sorting/)
+ [IFMO-Wiki](https://neerc.ifmo.ru/wiki/index.php?title=%D0%98%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5_%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%D0%B0_%D0%B2_%D0%B3%D0%BB%D1%83%D0%B1%D0%B8%D0%BD%D1%83_%D0%B4%D0%BB%D1%8F_%D1%82%D0%BE%D0%BF%D0%BE%D0%BB%D0%BE%D0%B3%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%BE%D0%B9_%D1%81%D0%BE%D1%80%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%BA%D0%B8)