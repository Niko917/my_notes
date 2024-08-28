
Двоичная куча - это двоичное подвешенное дерево, в котором выполнены 3 условия :

- значение в любой вершине не (больше / меньше), чем значения потомков

- на $i-ом$ слое - $2^i$ вершин. *(кроме последнего слоя)*

- Последний слой заполнен слева направо

--- 
## Представление двоичной кучи

Удобнее всего двоичную кучу хранить в виде массива $a[0..n−1]$, у которого нулевой элемент, $a[0]$ — элемент в корне, а потомками элемента $a[i]$ являются $a[2i+1]$ и $a[2i+2]$.

Высота кучи определяется как высота двоичного дерева.

То есть она равна количеству рёбер в самом длинном простом пути, соединяющем корень кучи с одним из её листьев.

Высота кучи есть $O(log n)$, где $n$ — количество узлов дерева.

Двоичные кучи являются частным случаем приоритетной очереди.


---


## 1. Восстановление свойств кучи 

Если в куче изменяется один из элементов, то это изменение может повлияет на структуру всей кучи, ведь потеряется иерархия.

Для восстановления свойств кучи используется операция просеивания (sift_Up и sift_Down)

## 1.1 Sift_Down
	
- если значение измененного элемента увеличивается, то свойства кучи восстанавливаются с помощью операции Sift_Down.
	
- Работа процедуры: 
		
	- если $i-й$ элемент меньше, чем его сыновья, всё поддерево уже является кучей, и делать ничего не надо.
		
	- В противном случае меняем местами $i-й$ элемент с наименьшим из его сыновей, после чего выполняем siftDownsiftDown для этого сына. Процедура выполняется за время $O(logn)$.

- code:
```cpp
template <typename T>
void siftDown(size_t i, bin_heap<T>& a) {

    size_t heapSize = a.size;
    // Предполагаем, что размер кучи хранится в переменной heapSize
    
    while (2 * i + 1 < heapSize) { 
    // Пока у узла есть левый потомок
        size_t left = 2 * i + 1; // индекс левого потомка
        size_t right = 2 * i + 2; // индекс правого потомка
        size_t j = left;
        
        if (right < heapSize && a.arr[right] < a.arr[left]) { 
        // Если правый потомок существует и меньше левого
            j = right; 
            // индекс j указывает на потомка с наименьшим значением
        }
        
        if (a.arr[i] <= a.arr[j]) { 
        // Если родительский узел меньше или равен меньшему из потомков
            break; 
            // свойство кучи восстановлено
        }
        
        std::swap(a.arr[i], a.arr[j]); 
        // иначе меняем местами родительский узел с меньшим потомком
        i = j; 
        // перемещаемся вниз к узлу, с которым был обмен
    }
}
```

## 1.2 Sift_Up

  
- Если значение измененного элемента уменьшается, то свойства кучи восстанавливаются функцией sift_Up.

- Работа процедуры:
	
	- если элемент больше своего отца, условие 1 соблюдено для всего дерева, и больше ничего делать не нужно.
		- Иначе, мы меняем местами его с отцом.
	
	- После чего выполняем siftUpsiftUp для этого отца.
		- Иными словами, слишком маленький элемент всплывает наверх.
		- Процедура выполняется за время $O(logn)$.

- code:
```cpp
template <typename T>

void sift_up(int i, bin_heap<T>& a) {
    while (i > 0) {
        int parent = (i - 1) / 2;
        if (a.arr[i] >= a.arr[parent]) { 
            break;
        }
        std::swap(a.arr[i], a.arr[parent]);
        
        i = parent;
    }
}
```


---

## 2. Извлечение элемента (min / max)

Выполняет извлечение (минимального / максимального) элемента из кучи за время $O(logn)$.

Извлечение выполняется в четыре этапа:

1. Значение корневого элемента (он и является минимальным) сохраняется для последующего возврата.

2. Последний элемент копируется в корень, после чего удаляется из кучи.

3. Вызывается siftDownsiftDown для корня.

4. Сохранённый элемент возвращается.


- Extract_Min

```cpp
template <typename T>

T extractMin(bin_heap<T>& a) {
    if (a.empty()) {
        throw std::runtime_error("Cannot extract from an empty heap.");
    }

    // Запоминаем минимальный элемент, который находится на вершине кучи
    T minElement = a.arr[0];

    // Перемещаем последний элемент в корень кучи
    a.arr[0] = a.back();
    a.pop_back(); // Удаляем последний элемент из кучи

    // Восстанавливаем свойство кучи
    if (!a.empty()) {
        siftDown(0, a);
    }

    return minElement;
}
```

- Extract_max (В минимальной куче)

```cpp
template <typename T>
T findMax(const bin_heap<T>& a) {
    if (a.empty()) {
        throw std::runtime_error("Cannot find maximum in an empty heap.");
    }

    // Находим индекс первого листа
    int firstLeafIndex = a.size() / 2;
    int lastLeafIndex = a.size() - 1;

    // Находим максимальный элемент среди листьев
    return *std::max_element(a.arr.begin() + firstLeafIndex, a.arr.end());
}
```



## 3. Вставка элемента в кучу

```cpp
    void insert(T key) {
        if (a.size < a.arr.size()) {
            a.arr[size] = key;
        } else {
            a.arr.push_back(key);
        }
        a.size++;
        siftUp(a.size - 1);
    }
```


## 4. Алгоритм поиска $k-ой$ статистики в куче

```cpp
#include <vector>
#include <queue>
#include <utility>
#include <stdexcept>

template <typename T>
class bin_heap {
public:
    std::vector<T> arr;

    bin_heap(const std::vector<T>& data) : arr(data) {}

    size_t leftChildIndex(size_t parent) {
        return 2 * parent + 1;
    }

    size_t rightChildIndex(size_t parent) {
        return 2 * parent + 2;
    }

    bool isValidIndex(size_t index) {
        return index < arr.size();
    }
};

template <typename T>
T findKthSmallest(bin_heap<T>& heap, int k) {
    if (heap.arr.empty() || k <= 0 || k > heap.arr.size()) {
        throw std::invalid_argument("Invalid argument for k or empty heap");
    }

    // Define a lambda to compare pairs by their first element
    auto compare = [](const std::pair<T, size_t>& a, const std::pair<T, size_t>& b) {
        return a.first > b.first;
    };

    // Create a min heap to store pairs of <value, index>
    std::priority_queue<std::pair<T, size_t>, std::vector<std::pair<T, size_t>>, decltype(compare)> minHeap(compare);

    // Add the root of the heap
    minHeap.push({heap.arr[0], 0});

    std::pair<T, size_t> element;
    for (int i = 0; i < k; ++i) {
        if (minHeap.empty()) {
            throw std::range_error("Heap is empty before reaching the kth element");
        }

        // Extract the smallest element from the min heap
        element = minHeap.top();
        minHeap.pop();

        // Get the indices of the left and right children in the original heap
        size_t left = heap.leftChildIndex(element.second);
        size_t right = heap.rightChildIndex(element.second);

        // Add the children to the min heap, if they exist
        if (heap.isValidIndex(left)) {
            minHeap.push({heap.arr[left], left});
        }
        if (heap.isValidIndex(right)) {
            minHeap.push({heap.arr[right], right});
        }
    }

    // The root of the min heap is the kth smallest element
    return element.first;
}

```


