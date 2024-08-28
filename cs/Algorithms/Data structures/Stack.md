Stack - структура данных, которая работаем по принципу LIFO

## Основная информация о стеке

- General methods

	1. Push() - add element on the top

	2. Pop() - delete top element

	3. Peek() - return top element without deleting it 

	4. is_Empty() - check if the stack is empty 

	5. is_Full() - check if the stack is full  

---
## Реализация на основе Динамического массива

```cpp

#include <iostream>

template <typename T>
class Stack {
private:
    T* arr;
    int top;
    size_t capacity;

public:
    Stack(int size = 10); // constructor
    ~Stack(); // destructor

    void push(T);
    T pop();
    T peek();

    int size();
    bool isEmpty();
    bool isFull();
};

template <typename T>
Stack<T>::Stack(int size) {
    arr = new T[size];
    capacity = size;
    top = -1;
}

template <typename T>
Stack<T>::~Stack() {
    delete[] arr;
}

template <typename T>
void Stack<T>::push(T item) {
    if (isFull()) {
        std::cout << "Stack is full. Increasing capacity." << std::endl;
        T* temp = new T[2 * capacity];
        std::copy(arr, arr + capacity, temp);
        delete[] arr;
        capacity *= 2;
        arr = temp;
    }
    arr[++top] = item;
}

template <typename T>
T Stack<T>::pop() {
    if (isEmpty()) {
        std::cout << "Stack is empty." << std::endl;
        exit(EXIT_FAILURE);
    }
    return arr[top--];
}

template <typename T>
T Stack<T>::peek() {
    if (!isEmpty())
        return arr[top];
    else
        exit(EXIT_FAILURE);
}

template <typename T>
int Stack<T>::size() {
    return top + 1;
}

template <typename T>
bool Stack<T>::isEmpty() {
    return top == -1;
}

template <typename T>
bool Stack<T>::isFull() {
    return top == capacity - 1;
}		
```

---
## Реализация на основе Связного списка

```cpp
#include <iostream>

template <typename T>

class StackNode {
public:
    T data;
    StackNode* next;

    // Конструктор для упрощения создания новых узлов
    StackNode(T data, StackNode* next = nullptr) : data(data), next(next) {}
};

template <typename T>
bool isEmpty(StackNode<T>* root) {
    return !root;
}

template <typename T>
void push(StackNode<T>*& root, T data) {
    // Используем конструктор для создания нового узла
    root = new StackNode<T>(data, root);
}

template <typename T>
T pop(StackNode<T>*& root) {
    if (isEmpty(root)) {
        throw std::out_of_range("Stack is empty");
    }
    T popped = root->data;
    StackNode<T>* temp = root;
    root = root->next;
    delete temp;
    return popped;
}
```

---
## Persistance stack

- Персистентный стек - это структура данных, которая сохраняет все свои предыдущие состояния после модификаций. Это означает, что вы можете получить доступ к любому предыдущему состоянию стека в любое время, а не только к текущему состоянию.

	Персистентность в контексте структур данных обычно означает, что старые версии структуры данных остаются доступными даже после того, как они были изменены. Это достигается путем копирования изменяемых частей структуры данных при каждом изменении, вместо их перезаписи.
	
- Реализация:

	```cpp
#include <iostream>
#include <string>
#include <vector>
#include <stdexcept>
#include <utility>
 
// персистентный стек
template<typename T>
class pstack
{
private:
    struct node;
public:
    pstack()
    {
        root = new node(1);
        last = root;
    }
 
    void push(size_t const num_, T const& val_)// num_ это порядковый номер стека в который делаем push
    {
        if(num_ > last->nums.back())
            throw std::runtime_error("pstack::push failed: num_ > last->nums.back()");
        last = root->push(last->nums.back()+1, num_, val_);
    }
 
    T top(size_t const num_) const// num_ это порядковый  номер стека для которого делаем top
    {
        if(num_ > last->nums.back())
            throw std::runtime_error("pstack::top failed: num_ > last->nums.back()");
        for(auto const num : root->nums)
        {
            if(num_ == num)
                throw std::runtime_error("pstack::top failed: pstack is empty");
        }
        return root->top(num_)->val;
    }
 
    void pop(size_t const num_)
    {
        if(num_ > last->nums.back())
            throw std::runtime_error("pstack::pop failed: num_ > last->nums.back()");
        for(auto const num : root->nums)
        {
            if(num_ == num)
                throw std::runtime_error("pstack::pop failed: pstack is empty");
        }
        last = (root->pop(last->nums.back()+1, num_)).first;
    }
 
    size_t count() const // возвращает количество стеков
    {
        return last->nums.back();
    }
 
    ~pstack()
    {
        delete root;
        root = 0;
        last = 0;
    }
private:
    struct node
    {
        node(size_t const mynum, T const& val_ = T()): val(val_)
        {
            nums.push_back(mynum);
        }
        node* push(size_t const mynum, size_t const num_, T const& val_)
        {
            node* ret = 0;
            bool found = false;// убрать
            for(auto const num : nums)
            {
                if(num_ == num)
                {
                    ret = new node(mynum, val_);
                    ptrs.push_back(ret);
                    found = true;
                    break;
                }
            }
            if(!found)
            {
                for(auto const ptr : ptrs)
                {
                    if(ret = ptr->push(mynum, num_, val_))
                        break;
                }
            }
            return ret;
        }
        node const* top(size_t const num_) const
        {
            node const* ret = 0;
            bool found = false;
            for(auto const num : nums)
            {
                if(num_ == num)
                {
                    ret = this;
                    found = true;
                    break;
                }
            }
            if(!found)
            {
                for(auto const ptr : ptrs)
                {
                    if(ret = ptr->top(num_))
                        break;
                }
            }
            return ret;
        }
        std::pair<node const*, bool> pop (size_t const mynum, size_t const num_)
        {
            std::pair<node const*, bool> ret(0, false);
            for(auto const num : nums)
            {
                if(num_ == num)
                {
                    ret.second = true;
                }
            }
            if(!(ret.second))
            {
                for(auto const ptr : ptrs)
                {
                    ret = ptr->pop(mynum, num_);
                    if(ret.first || ret.second)
                    {
                        break;
                    }
                }
                if(ret.second)
                {
                    nums.push_back(mynum);
                    ret.first = this;
                    ret.second = false;
                }
            }
            return ret;
        }
        ~node()
        {
            for(auto& ptr : ptrs)
            {
                delete ptr;
                ptr = 0;
            }
        }
 
        std::vector<size_t> nums;// порядковые номера стеков для которых данный node является вершиной
        T val;
        std::vector<node*> ptrs;
    private:
        node(const node&);
        node& operator=(node);
    };
    node* root;
    node const* last;// указатель на последний node последнего стека
    pstack(pstack const&);
    pstack& operator=(pstack);
};
 
int main()
{
    pstack<std::string> stk;
    try
    {
        stk.push(1, "222");//2
        stk.push(2, "333");//3
        stk.push(1, "444");//4
        stk.push(3, "555");//5
        stk.pop(5);//6  333
        stk.pop(5);//7  333
        stk.pop(7);//8  222
        stk.pop(8);//9  empty
        stk.push(9, "101010");// 10
        stk.pop(10);//11  empty
        //std::cout << stk.last->nums.back() << '\n';
        std::cout << stk.count();
    }
    catch(std::exception const& e)
    {
        std::cerr << "Exception: " << e.what() << '\n';
    }
    return 0;
}
```

## [Монотонный стек](https://www.geeksforgeeks.org/introduction-to-monotonic-stack-data-structure-and-algorithm-tutorials/)

