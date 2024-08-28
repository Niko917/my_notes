sliding window - это алгоритм, для решения задач, связанных с упорядоченными и итерируемыми структурами данных, в которых необходимо найти некую подпоследовательность, удовлетворяющую некоторому условию.


### Основные принципы

-  **Окно**: Окно представляет собой подмассив или подстроку, которая определяется двумя параметрами - началом и концом окна.
    
- **Движение окна**: Окно может двигаться по массиву или строке двумя способами:
    
    - **Расширение**: Увеличение размера окна путем сдвига конца окна вправо.
        
    - **Сжатие**: Уменьшение размера окна путем сдвига начала окна вправо.

- **Асимптотика:** 
	- $O(n)$, так как каждый символ обрабатывается не более двух раз: при добавлении в окно и при удалении из окна.
		- сдвиг указателей и удаление символа из unordered_map работает за $O(1)$ и на время работы влияет только работа с окном
	
---

### [Пример](https://www.geeksforgeeks.org/problems/length-of-the-longest-substring3036/1)

***Условие: Необходимо найти максимальную длину подстроки, которая содержит не более K различных символов.***


***Идея:***

- храним символы строки и их количество в unordered_map 

- последовательно **двигаем окно по строке**, добавляя символы в unordered_map

- проверяем количество символов в текущей подстроке
	- пока их количество больше чем K
		- проверяем счётчики уникальных символов
			- если счётчик символа = 1
				- выбрасываем этот символ из подстроки
			- если счётчик символа > 1
				- декремент счётчика символа и дальнейшая проверка условия
		- сдвигаем правую границу окна

- определяем длину полученной подстроки из K уникальных символов, которая является максимальной


Code:

``` c++

#include <string>
#include <unordered_map>

int longest_substr_with_k_distinct(const std::string& str, int k) {
	std::unordered_map<char, int> count;
	if (str.lenght() == 0 && k == 0) return 0;
	
	int left, max_len = 0, 0;

	for (int right = 0; right < str.size(); ++right) {
		count[str[right]]++;

		while (count.size() > K) {
			count[str[left]]--;
			if (count[str[left]] == 0){
				count.erase(str[left]);
			}
			left++;
		}
	max_len = std::max(max_len, right - left + 1);
	}
	return max_len;
}
```

---
## Задачи

- https://leetcode.com/problems/substring-with-concatenation-of-all-words/

- https://leetcode.com/problems/minimum-window-substring/

- https://leetcode.com/problems/minimum-size-subarray-sum/

- https://leetcode.com/problems/longest-repeating-character-replacement/

- https://leetcode.com/problems/find-all-anagrams-in-a-string/

- https://leetcode.com/problems/permutation-in-string/

- https://leetcode.com/problems/fruit-into-baskets/

- https://leetcode.com/problems/max-consecutive-ones-iii/