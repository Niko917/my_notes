[Бинарный поиск ](https://neerc.ifmo.ru/wiki/index.php?title=Целочисленный_двоичный_поиск)- это алгоритм поиска объекта по заданному признаку в множестве объектов, упорядоченных по тому же самому признаку, работающий за логарифмическое время.


### description

Бинарный поиск относится к алгоритмам, работающим по принципу "Разделяй и властвуй".

- 𝐴 - отсортированный массив,
- 𝑛 - количество элементов в массиве,
- 𝑥 - искомый элемент


1. Устанавливаем начальные границы поиска:
	- low = 0, high = n - 1
2. Основной цикл:
	- while low $\leq$ high
		- mid = $[\frac{low+high}{2}]$ 
		- if A[mid] == x 
			- => return mid
		- else if A[mid] > x
			- high = mid - 1 (search in the left half)
		- else: (if A[mid] < x)
			- => low = mid + 1 (search in the right half)

### Asymptotic

количество делений N множества равно $\log_{2}n = N$, 
где  n - количество элементов в множестве.

=> Асимптотическая сложность алгоритма: $O(\log n)$ 


## Реализация (C++, Rust)

### C++

``` cpp
#include <vector>
#include <iostream>

int binary_search(const vector<int>& set, const int el) {
	int left, right = 0, set.size() - 1;

	while (left <= right) {
		int mid = (left + right) / 2;
		if (set[mid] == el) {
			return mid;
		}
		else if(set[mid] > el) {
			right = mid - 1;
		}
		else {
			left = mid + 1;
		}
	}
}
```

---
### Rust

``` Rust
fn binary_search(set: &[i32], el: i32) -> Option<usize> {
	let mut left = 0;
	let mut right = set.len() - 1;

	while left <= right {
		let mid = (left + (right - left)) / 2;

		if set[mid] == el {
			return Some(mid);
		}
		else if set[mid] > el {
			right = mid - 1;
		}
		else {
			left = mid + 1;
		}
	}
	None
}
```


---

## [Binary search on answer](https://ru.algorithmica.org/cs/interactive/answer-search/)

Бинарный поиск по ответу - это алгоритм, который используется в задачах, где пространство возможных решений упорядочено и требуется найти некоторый оптимальный параметр, а также можно определить, является ли текущее предположение лучше или хуже искомого решения.


Основная идея заключается в следующем:

1. Определить диапазон возможных значений для искомого параметра.
    
2. Использовать бинарный поиск для уменьшения этого диапазона, проверяя, удовлетворяет ли среднее значение условию задачи.
    
3. Продолжать бинарный поиск до тех пор, пока не будет найдено оптимальное значение.


**Показательная задача**: Дан массив целых чисел `arr` и целое число `k`.
Необходимо найти минимальное значение `x`, такое что количество элементов в массиве, которые больше или равны `x`, не меньше `k`.


Code:

``` cpp
#include <vector>
#include <algorithm>

int count_elements_greater_or_equal(const std::vector<int>& arr, int x) {
	int count = 0;
	for (int num: arr) {
		if (num >= x){
			count++;
		}
	}
	return count;
}

int find_min_x(const std::vector<int>& arr, ink k) {
	int left = *std::min_element(arr.begin(), arr.end());
	int right = *std::max_element(arr.begin(), arr.end());

	while (left < right) {
		int mid = (left + right) / 2;

		if (count_elements_greater_or_equal(arr, mid)) >= k {
		right = mid;
		}
		else {
			left = mid + 1;
		}
	}
	return left;
}
```


---

### Задачи

- [Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/description/)
- [Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/description/)
- [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)
- [Minimum Number of Days to Make m Bouquets](https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/)
- [Find the Smallest Divisor Given a Threshold](https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/)
- [Minimum Limit of Balls in a Bag](https://leetcode.com/problems/minimum-limit-of-balls-in-a-bag/)
- [Maximum Side Length of a Square with Sum Less than or Equal to Threshold](https://leetcode.com/problems/maximum-side-length-of-a-square-with-sum-less-than-or-equal-to-threshold/)

