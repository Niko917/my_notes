Дек - Линейная структура данных, обобщение очереди (Queue), которая позволяет вставлять и удалять элементы на обоих концах.


реализовать Дек можно несколькими способами

- реализация на Циклическом массиве

- реализация на двусвязном списке


**Свойства Дека:** 

- Deque — это обобщенная версия очереди, которая позволяет нам вставлять и удалять элементы на обоих концах. 

- Deque не подчиняется правилу FIFO.


---
# Implementation of Deque using circular array.

Для реализации deque нам необходимо отслеживать два индекса, передний и задний.

Мы ставим в очередь (выталкиваем) элемент в конце или на переднем конце очереди и выводим из очереди (выталкиваем) элемент как из конца, так и из переднего конца.

```cpp
// C++ implementation of Dequeue using circular array
#include <iostream>

// Maximum size of array or Dequeue
#define MAX 100

// A structure to represent a Deque
class Deque {
private:
	int arr[MAX];
	int front;
	int rear;
	int size;

public:
	// constructor
	Deque(int size) {
		front = -1;
		rear = 0;
		this->size = size;
	}

	// Methods:
	void insertfront(int key);
	void insertrear(int key);
	void deletefront();
	void deleterear();
	bool isFull();
	bool isEmpty();
	int getFront();
	int getRear();
};

// Checks whether Deque is full or not.
bool Deque::isFull() {
	return ((front == 0 && rear == size - 1) || front == rear + 1);
}

// Checks whether Deque is empty or not.
bool Deque::isEmpty() { return (front == -1); }

// Inserts an element at front
void Deque::insertfront(int key) {
	// check whether Deque if full or not
	if (isFull()) {
		std::cout << "Overflow\n" << endl;
		return;
	}

	// If queue is initially empty
	if (front == -1) {
		front = 0;
		rear = 0;
	}

	// front is at first position of queue
	else if (front == 0)
		front = size - 1;

	else // decrement front end by '1'
		front = front - 1;

	// insert current element into Deque
	arr[front] = key;
}

// function to inset element at rear end
// of Deque.
void Deque ::insertrear(int key) {
	if (isFull()) {
		std::cout << " Overflow\n " << endl;
		return;
	}

	// If queue is initially empty
	if (front == -1) {
		front = 0;
		rear = 0;
	}

	// rear is at last position of queue
	else if (rear == size - 1)
		rear = 0;

	// increment rear end by '1'
	else
		rear = rear + 1;

	// insert current element into Deque
	arr[rear] = key;
}

// Deletes element at front end of Deque
void Deque ::deletefront() {
	// check whether Deque is empty or not
	if (isEmpty()) {
		std::cout << "Queue Underflow\n" << endl;
		return;
	}

	// Deque has only one element
	if (front == rear) {
		front = -1;
		rear = -1;
	}
	else
		// back to initial position
		if (front == size - 1)
		front = 0;

	else // increment front by '1' to remove current
		// front value from Deque
		front = front + 1;
}

// Delete element at rear end of Deque
void Deque::deleterear() {
	if (isEmpty()) {
		std::cout << " Underflow\n" << endl;
		return;
	}

	// Deque has only one element
	if (front == rear) {
		front = -1;
		rear = -1;
	}
	else if (rear == 0)
		rear = size - 1;
	else
		rear = rear - 1;
}

// Returns front element of Deque
int Deque::getFront() {
	// check whether Deque is empty or not
	if (isEmpty()) {
		std::cout << " Underflow\n" << endl;
		return -1;
	}
	return arr[front];
}

// function return rear element of Deque
int Deque::getRear() {
	// check whether Deque is empty or not
	if (isEmpty() || rear < 0) {
		std::cout << " Underflow\n" << endl;
		return -1;
	}
	return arr[rear];
}

// Driver code
int main() {
	Deque dq(5);

	// Function calls
	std::cout << "Insert element at rear end : 5 \n";
	dq.insertrear(5);

	std::cout << "insert element at rear end : 10 \n";
	dq.insertrear(10);

	std::cout << "get rear element "
		<< " " << dq.getRear() << endl;

	dq.deleterear();
	std::cout << "After delete rear element new rear"
		<< " become " << dq.getRear() << endl;

	std::cout << "inserting element at front end \n";
	dq.insertfront(15);

	std::cout << "get front element "
		<< " " << dq.getFront() << endl;

	dq.deletefront();

	std::cout << "After delete front element new "
		<< "front become " << dq.getFront() << endl;
	return 0;
}
```

---

## Implementation of Deque using doubly linked list.

```cpp
// C++ implementation of Deque using doubly linked list
#include <bits/stdc++.h>


// Node of a doubly linked list
struct Node {
	int data;
	Node *prev, *next;
	// Function to get a new node
	static Node* getnode(int data)
	{
		Node* newNode = (Node*)malloc(sizeof(Node));
		newNode->data = data;
		newNode->prev = newNode->next = NULL;
		return newNode;
	}
};

// A structure to represent a deque
class Deque {
	Node* front;
	Node* rear;
	int Size;

public:
	Deque() {
		front = rear = NULL;
		Size = 0;
	}

	// Operations on Deque
	void insertFront(int data);
	void insertRear(int data);
	void deleteFront();
	void deleteRear();
	int getFront();
	int getRear();
	int size();
	bool isEmpty();
	void erase();
};

// Function to check whether deque is empty or not
bool Deque::isEmpty() { return (front == NULL); }

// Function to return the number of elements in the deque
int Deque::size() { return Size; }

// Function to insert an element at the front end
void Deque::insertFront(int data) {
	Node* newNode = Node::getnode(data);
	// If true then new element cannot be added and it is an 'Overflow' condition
	if (newNode == NULL)
		cout << "OverFlow\n";
	else {
		// If deque is empty
		if (front == NULL)
			rear = front = newNode;

		// Inserts node at the front end
		else {
			newNode->next = front;
			front->prev = newNode;
			front = newNode;
		}

		// Increments count of elements by 1
		Size++;
	}
}

// Function to insert an element at the rear end
void Deque::insertRear(int data) {
	Node* newNode = Node::getnode(data);
	// If true then new element cannot be added and it is an 'Overflow' condition
	if (newNode == NULL)
		cout << "OverFlow\n";
	else {
		// If deque is empty
		if (rear == NULL)
			front = rear = newNode;

		// Inserts node at the rear end
		else {
			newNode->prev = rear;
			rear->next = newNode;
			rear = newNode;
		}

		Size++;
	}
}

// Function to delete the element from the front end
void Deque::deleteFront() {
	// If deque is empty then 'Underflow' condition
	if (isEmpty())
		std::cout << "UnderFlow\n";

	// Deletes the node from the front end and makes the adjustment in the links
	else {
		Node* temp = front;
		front = front->next;

		// If only one element was present
		if (front == NULL)
			rear = NULL;
		else
			front->prev = NULL;
		free(temp);

		// Decrements count of elements by 1
		Size--;
	}
}

// Function to delete the element from the rear end
void Deque::deleteRear() {
	// If deque is empty then 'Underflow' condition
	if (isEmpty())
		std::cout << "UnderFlow\n";

	// Deletes the node from the rear end and makes the adjustment in the links
	else {
		Node* temp = rear;
		rear = rear->prev;

		// If only one element was present
		if (rear == NULL)
			front = NULL;
		else
			rear->next = NULL;
		free(temp);

		// Decrements count of elements by 1
		Size--;
	}
}

// Function to return the element at the front end
int Deque::getFront()
{
	// If deque is empty, then returns garbage value
	if (isEmpty())
		return -1;
	return front->data;
}

// Function to return the element at the rear end
int Deque::getRear() {
	// If deque is empty, then returns garbage value
	if (isEmpty())
		return -1;
	return rear->data;
}

// Function to delete all the elements from Deque
void Deque::erase() {
	rear = NULL;
	while (front != NULL) {
		Node* temp = front;
		front = front->next;
		free(temp);
	}
	Size = 0;
}

// Driver program to test above
int main() {
	Deque dq;
	std::cout << "Insert element '5' at rear end\n";
	dq.insertRear(5);

	std::cout << "Insert element '10' at rear end\n";
	dq.insertRear(10);

	std::cout << "Rear end element: " << dq.getRear() << endl;

	dq.deleteRear();
	std::cout << "After deleting rear element new rear"
		<< " is: " << dq.getRear() << endl;

	std::cout << "Inserting element '15' at front end \n";
	dq.insertFront(15);

	std::cout << "Front end element: " << dq.getFront() << endl;

	std::cout << "Number of elements in Deque: " << dq.size()
		<< endl;

	dq.deleteFront();
	std::cout << "After deleting front element new "
		<< "front is: " << dq.getFront() << endl;

	return 0;
}
```

---


```cpp
// C++ implementation of Deque using doubly linked list
#include <bits/stdc++.h>

// Node of a doubly linked list
struct Node {
	int data;
	Node *prev, *next;
	// Function to get a new node
	static Node* getnode(int data) {
		Node* newNode = (Node*)malloc(sizeof(Node));
		newNode->data = data;
		newNode->prev = newNode->next = NULL;
		return newNode;
	}
};

// A structure to represent a deque
class Deque {
	Node* front;
	Node* rear;
	int Size;

public:
	Deque() {
		front = rear = NULL;
		Size = 0;
	}

	// Operations on Deque
	void insertFront(int data);
	void insertRear(int data);
	void deleteFront();
	void deleteRear();
	int getFront();
	int getRear();
	int size();
	bool isEmpty();
	void erase();
};

// Function to check whether deque is empty or not
bool Deque::isEmpty() { return (front == NULL); }

// Function to return the number of elements in the deque
int Deque::size() { return Size; }

// Function to insert an element at the front end
void Deque::insertFront(int data) {
	Node* newNode = Node::getnode(data);
	// If true then new element cannot be added and it is an 'Overflow' condition
	if (newNode == NULL)
		cout << "OverFlow\n";
	else {
		// If deque is empty
		if (front == NULL)
			rear = front = newNode;

		// Inserts node at the front end
		else {
			newNode->next = front;
			front->prev = newNode;
			front = newNode;
		}

		// Increments count of elements by 1
		Size++;
	}
}

// Function to insert an element at the rear end
void Deque::insertRear(int data) {
	Node* newNode = Node::getnode(data);
	// If true then new element cannot be added and it is an 'Overflow' condition
	if (newNode == NULL)
		std::cout << "OverFlow\n";
	else {
		// If deque is empty
		if (rear == NULL)
			front = rear = newNode;

		// Inserts node at the rear end
		else {
			newNode->prev = rear;
			rear->next = newNode;
			rear = newNode;
		}
		Size++;
	}
}

// Function to delete the element from the front end
void Deque::deleteFront() {
	// If deque is empty then 'Underflow' condition
	if (isEmpty())
		cout << "UnderFlow\n";

	// Deletes the node from the front end and makes the adjustment in the links
	else {
		Node* temp = front;
		front = front->next;
		
		// If only one element was present
		if (front == NULL)
			rear = NULL;
		else
			front->prev = NULL;
		free(temp);

		// Decrements count of elements by 1
		Size--;
	}
}

// Function to delete the element from the rear end
void Deque::deleteRear() {
	// If deque is empty then 'Underflow' condition
	if (isEmpty())
		cout << "UnderFlow\n";

	// Deletes the node from the rear end and makes the adjustment in the links
	else {
		Node* temp = rear;
		rear = rear->prev;

		// If only one element was present
		if (rear == NULL)
			front = NULL;
		else
			rear->next = NULL;
		free(temp);
		// Decrements count of elements by 1
		Size--;
	}
}

// Function to return the element at the front end
int Deque::getFront() {
	// If deque is empty, then returns garbage value
	if (isEmpty())
		return -1;
	return front->data;
}

// Function to return the element at the rear end
int Deque::getRear() {
	// If deque is empty, then returns garbage value
	if (isEmpty())
		return -1;
	return rear->data;
}

// Function to delete all the elements from Deque
void Deque::erase() {
	rear = NULL;
	while (front != NULL) {
		Node* temp = front;
		front = front->next;
		free(temp);
	}
	Size = 0;
}

// Driver program to test above
int main() {
	Deque dq;
	std::cout << "Insert element '5' at rear end\n";
	dq.insertRear(5);

	std::cout << "Insert element '10' at rear end\n";
	dq.insertRear(10);

	std::cout << "Rear end element: " << dq.getRear() << endl;

	dq.deleteRear();
	std::cout << "After deleting rear element new rear" << " is: " << dq.getRear() << std::endl;

	std::cout << "Inserting element '15' at front end \n";
	dq.insertFront(15);

	std::cout << "Front end element: " << dq.getFront() << std::endl;

	std::cout << "Number of elements in Deque: " << dq.size() << std::endl;

	dq.deleteFront();
	std::cout << "After deleting front element new " << "front is: " << dq.getFront() << std::endl;

	return 0;
}
```