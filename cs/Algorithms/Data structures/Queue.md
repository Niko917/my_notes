Очередь - Линейная структура данных, организованная по принципу FIFO (First In Fiest Out)
- линейная  структура - структура, которая организована в последовательном расположении элементов структуры

Разница между стеками и очередями заключается в удалении.
	
	В стеке мы удаляем элемент, добавленный последним;
	
	в очереди мы удаляем элемент, добавленный последним

---
### Basic methods

Основные операции Очереди:
- **enqueue():** вставляет элемент в конец очереди
- **dequeue():** Эта операция удаляет и возвращает элемент, который находится в начале очереди.
- **front():** Эта операция возвращает элемент во внешнем интерфейсе, не удаляя его.
- **rear():** Эта операция возвращает элемент в конце, не удаляя его.
- **isEmpty():** Эта операция указывает, пуста очередь или нет.
- **isFull():** Эта операция указывает, заполнена очередь или нет.
- **size():** Эта операция возвращает размер очереди


---
## Types of queue

Существует несколько видов построения очереди:

- Basic queue - Простая очередь, также известная как линейная очередь, является самой базовой версией очереди. 

- Circular queue - Круговая очередь. 
	- В круговой очереди элемент queue действует как круговое кольцо.
	- Работа циклической очереди аналогична работе с линейной очередью, за исключением того факта, что последний элемент соединен с первым элементом.
	- Его преимущество в том, что память используется лучшим образом.

- Priority queue - Очередь с приоритетом.
	- Эта очередь относится к особому типу очередей.
	- Ее особенность в том, что она упорядочивает элементы в очереди на основе некоторого приоритета.
	- Приоритетом может быть что-то, где элемент с наибольшим значением имеет приоритет, поэтому он создает очередь с порядком убывания значений.
	- Приоритет также может быть таким, что элемент с наименьшим значением получает наивысший приоритет, поэтому, в свою очередь, он создает очередь с возрастающим порядком значений.

- Dequeue - Дек, также известный как Double Ended Queue.
	- Как следует из названия double ended , это означает, что элемент может быть вставлен или удален с обоих концов очереди, в отличие от других очередей, в которых это может быть сделано только с одного конца. 
	- Из-за этого свойства он может не подчиняться свойству First In First Out .


---
---
## Array implementation Of Queue


Для реализации queue нам нужно отслеживать два индекса, передний и задний.
Мы помещаем элемент в очередь сзади и удаляем элемент из очереди спереди.

C++ realization of queue:

```cpp
class Queue {
public:
    int front, back, size;

    unsigned capacity;
    int* array;
};

//constructor
Queue* createQueue(unsigned capacity) {
    Queue* queue = new Queue();

    queue->capacity = capacity;
    queue->front = queue->size = 0;
    // This is important, see the enqueue`
    queue->back = capacity - 1;

    queue->array = new int[queue->capacity];
    return queue;
}


bool is_Full(Queue* queue) {
	return (queue->size == queue->capasity);
}

bool is_Empty(Queue* queue) {
	return (queue->size == 0);
}

// Function to add an item to the queue
void enqueue(Queue* queue, int item) {
    if (isFull(queue))
        return;
    queue->back = (queue->back + 1) % queue->capacity;
    queue->array[queue->back] = item;
    queue->size = queue->size + 1;
}


int dequeue(Queue* queue) {
	if (is_Empty(queue)) {
		return;
	}
	int item = queue->array[queue->front];
	queue->front = (queue->front+1) % queue->capasity;
	queue->size = queue->size - 1;
	return item;
}

int front (Queue* queue) {
	if (is_Empty) {
		return;
	}
	return queue->array[queue->front];

}

int back(Queue* queue) {
	if (is_Empty) {
		return;
	}
	return queue->array[queue->back];
}
``` 


---
## Circular Queue

Круговая очередь - это просто разновидность линейной очереди, в которой голова и хвост соединены друг с другом, чтобы оптимизировать расход места в линейной очереди и сделать ее эффективной.

Преимущества Круговой очереди над линейной:

- **Проще для вставки-удаления:** 
	- В круговой очереди элементы могут быть легко вставлены, если есть свободные места, пока они не будут полностью заняты, тогда как в случае линейной очереди вставка невозможна, как только конец достигает последнего индекса, даже если в очереди присутствуют пустые места.
	
- **Эффективное использование памяти:** 
	- В циклической очереди нет потери памяти, поскольку используется незанятое пространство, и память используется должным образом ценным и эффективным образом по сравнению с линейной очередью.
	
- **Простота выполнения операций:** 
	- В линейной очереди соблюдается **FIFO**, поэтому элемент, вставленный первым, является элементом, подлежащим удалению первым. Это не сценарий в случае круговой очереди, поскольку задняя и передняя части не фиксированы, поэтому порядок вставки-удаления может быть изменен, что очень полезно.


Array implimentation of Circular Queue:

```cpp
// C or C++ program for insertion and
// deletion in Circular Queue
#include<bits/stdc++.h>
using namespace std;

class Queue {
	// Initialize front and back
	int back, front;

	// Circular Queue
	int size;
	int *arr;
public:
	Queue(int s) {
		front = back = -1;
		size = s;
		arr = new int[s];
	}

	void enQueue(int value);
	int deQueue();
	void displayQueue();
};


/* Function to create Circular queue */
void Queue::enQueue(int value) {
	if ((front == 0 && back == size-1) ||
			((back + 1) % size == front)) {
		return;
	}

	else if (front == -1) /* Insert First Element */ {
		front = back = 0;
		arr[back] = value;
	}

	else if (back == size-1 && front != 0) {
		back = 0;
		arr[back] = value;
	}

	else
	{
		back++;
		arr[back] = value;
	}
}

// Function to delete element from Circular Queue
int Queue::deQueue() {
	if (front == -1) {
		return 0;
	}

	int data = arr[front];
	arr[front] = -1;
	if (front == back) {
		front = -1;
		back = -1;
	}
	else if (front == size-1)
		front = 0;
	else
		front++;
	return data;
}


void Queue::displayQueue() {
	if (front == -1) {
		return;
	}
	if (back >= front) {
		for (int i = front; i <= back; i++)
			std::cout << arr[i] << endl;
	}
	else {
		for (int i = front; i < size; i++)
			std::cout << arr[i] << endl;

		for (int i = 0; i <= back; i++)
			std::cout << arr[i] << endl;
	}
}

/* Driver of the program */
int main() {
	Queue q(5);
	q.enQueue(14);
	q.enQueue(22);
	q.enQueue(13);
	q.enQueue(-6);

	q.displayQueue();

	q.displayQueue();

	q.enQueue(9);
	q.enQueue(20);
	q.enQueue(5);

	q.displayQueue();

	q.enQueue(20);
	return 0;
}

```


---

---
## Priority Queue

**Приоритетная очередь** - это тип очереди, которая упорядочивает элементы на основе их значений приоритета. 


В приоритетной очереди с каждым элементом связано значение приоритета. 
Когда вы добавляете элемент в очередь, он вставляется в позицию, основанную на его значении приоритета.

Существует несколько способов реализации приоритетной очереди, включая использование массива, связанного списка, кучи или бинарного дерева поиска.



Свойства приоритетной очереди:

- С каждым элементом связан приоритет.

- Элемент с высоким приоритетом удаляется из очереди перед элементом с низким приоритетом.

- Если два элемента имеют одинаковый приоритет, они обслуживаются в соответствии с их порядком в очереди.  



## Array implementation of Priority queue:

```cpp
// C++ program to implement Priority Queue using Arrays
#include <bits/stdc++.h>

// Structure for the elements in the priority queue
struct item {
	int value;
	int priority;
};

// Store the element of a priority queue
item pr[100000];

// Pointer to the last index
int size = -1;

// Function to insert a new element into priority queue
void enqueue(int value, int priority) {
	// Increase the size
	size++;

	// Insert the element
	pr[size].value = value;
	pr[size].priority = priority;
}

// Function to check the top element
int peek() {
	int highestPriority = INT_MIN;
	int ind = -1;

	// Check for the element with highest priority
	for (int i = 0; i <= size; i++) {
		// If priority is same choose the element with the highest value
		if (highestPriority == pr[i].priority && ind > -1
			&& pr[ind].value < pr[i].value) {
			highestPriority = pr[i].priority;
			ind = i;
		}
		else if (highestPriority < pr[i].priority) {
			highestPriority = pr[i].priority;
			ind = i;
		}
	}

	// Return position of the element
	return ind;
}

// Function to remove the element with the highest priority
void dequeue() {
	// Find the position of the element with highest priority
	int ind = peek();

	// Shift the element one index before from the position of the element with highest priority is found
	for (int i = ind; i < size; i++) {
		pr[i] = pr[i + 1];
	}
	// Decrease the size of the priority queue by one
	size--;
}


int main() {
	// Function Call to insert elements as per the priority
	enqueue(10, 2);
	enqueue(14, 4);
	enqueue(16, 4);
	enqueue(12, 3);

	// Stores the top element at the moment
	int ind = peek();

	cout << pr[ind].value << endl;

	// Dequeue the top element
	dequeue();

	// Check the top element
	ind = peek();
	cout << pr[ind].value << endl;

	// Dequeue the top element
	dequeue();

	// Check the top element
	ind = peek();
	cout << pr[ind].value << endl;

	return 0;
}
```

---

## Linked List Implementation of Priority Queue

```cpp
// C++ program to implement Priority Queue
// using Arrays
#include <bits/stdc++.h>

// Structure for the elements in the
// priority queue
struct item {
	int value;
	int priority;
};

// Store the element of a priority queue
item pr[100000];

// Pointer to the last index
int size = -1;

// Function to insert a new element
// into priority queue
void enqueue(int value, int priority) {
	// Increase the size
	size++;

	// Insert the element
	pr[size].value = value;
	pr[size].priority = priority;
}

// Function to check the top element
int peek() {
	int highestPriority = INT_MIN;
	int ind = -1;

	// Check for the element with highest priority
	for (int i = 0; i <= size; i++) {

		// If priority is same choose the element with the highest value
		if (highestPriority == pr[i].priority && ind > -1
			&& pr[ind].value < pr[i].value) {
			highestPriority = pr[i].priority;
			ind = i;
		}
		else if (highestPriority < pr[i].priority) {
			highestPriority = pr[i].priority;
			ind = i;
		}
	}
	// Return position of the element
	return ind;
}

// Function to remove the element with the highest priority
void dequeue() {
	// Find the position of the element with highest priority
	int ind = peek();

	// Shift the element one index before from the position of the element with highest priority is found
	for (int i = ind; i < size; i++) {
		pr[i] = pr[i + 1];
	}

	// Decrease the size of the priority queue by one
	size--;
}

// Driver Code
int main() {
	// Function Call to insert elements as per the priority
	enqueue(10, 2);
	enqueue(14, 4);
	enqueue(16, 4);
	enqueue(12, 3);

	// Stores the top element at the moment
	int ind = peek();

	cout << pr[ind].value << endl;

	// Dequeue the top element
	dequeue();

	// Check the top element
	ind = peek();
	cout << pr[ind].value << endl;

	// Dequeue the top element
	dequeue();

	// Check the top element
	ind = peek();
	cout << pr[ind].value << endl;

	return 0;
}
```

---
## Heap imlementation of Priority queue


### Операция с двоичной кучей 

- **Insert(p):** Вставляет новый элемент с приоритетом **p** .

- **ExtractMax():** извлекает элемент с максимальным приоритетом.

- **Remove(i):** удаляет элемент, на который указывает итератор i.

- **getMax():** возвращает элемент с максимальным приоритетом.

- **ChangePriority(i, p):** изменяет приоритет элемента, на который указывает **i** , на **p** .


```cpp
#include <iostream>
#include <vector>


// Priority queue implementation in C++
template<typename T>
class PriorityQueue {
private:
	vector<T> data;

public:
// Implementing Priority Queue using inbuilt available vector in C++
	PriorityQueue() {}

// Element Inserting function
void Enqueue(T item) {
	// item Insertion
	data.push_back(item);
	int ci = data.size() - 1;

	// Re-structure heap(Max Heap) so that after 
	// addition max element will lie on top of pq
	while (ci > 0) {
	int pi = (ci - 1) / 2;
	if (data[ci] <= data[pi])
		break;
	T tmp = data[ci];
	data[ci] = data[pi];
	data[pi] = tmp;
	ci = pi;
	}
}

T Dequeue() {
	// deleting top element of pq
	int li = data.size() - 1;
	T frontItem = data[0];
	data[0] = data[li];
	data.pop_back();

	--li;
	int pi = 0;

	// Re-structure heap(Max Heap) so that after 
	// deletion max element will lie on top of pq
	while (true) {
	int ci = pi * 2 + 1;
	if (ci > li)
		break;
	int rc = ci + 1;
	if (rc <= li && data[rc] < data[ci])
		ci = rc;
	if (data[pi] >= data[ci])
		break;
	T tmp = data[pi];
	data[pi] = data[ci];
	data[ci] = tmp;
	pi = ci;
	}
	return frontItem;
}

// Function which returns peek element
T Peek() {
	T frontItem = data[0];
	return frontItem;
}

int Count() {
	return data.size();
}
};


int main() {
// Creating priority queue which contains int in it
PriorityQueue<int> pq;

// Insert element 1 in pq
pq.Enqueue(1);

cout << "Size of pq is : " << pq.Count() << 
	" and Peek Element is : " << pq.Peek() << endl;

// Insert element 10 and -8 in pq
pq.Enqueue(10);
pq.Enqueue(-8);

cout << "Size of pq is : " << pq.Count() << 
	" and Peek Element is : " << pq.Peek() << endl;

// Delete element from pq
pq.Dequeue();

cout << "Size of pq is : " << pq.Count() << 
	" and Peek Element is : " << pq.Peek() << endl;

// Delete element from pq
pq.Dequeue();

cout << "Size of pq is : " << pq.Count() << 
	" and Peek Element is : " << pq.Peek() << endl;

// Insert element 25 in pq
pq.Enqueue(25);

cout << "Size of pq is : " << pq.Count() << 
	" and Peek Element is : " << pq.Peek() << endl;

return 0;
}

```