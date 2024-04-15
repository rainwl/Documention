# std::vector&lt;int&gt; and int pointer

## Assign int pointer to vector int

```C++
int *array = new int[5]{1, 2, 3, 4, 5};
int n = 5;

// 1
std::vector<int> list(array, array + n);

// 2
std::vector<int> list2;
list2.reserve(n);
for (int i = 0; i < n; ++i) {
  list2.push_back(array[i]);
}

// 3
std::vector<int> list3;
list3.assign(array, array + n);

// 4
std::vector<int> list4(n);
std::copy(array, array + n, list4.begin());
```

## Assign vector int to int pointer

```C++
std::vector<int> vector = {1, 2, 3, 4, 5};

// 1
int *array = new int[vector.size()];
for (int i = 0; i < vector.size(); ++i) {
  array[i] = vector[i];
}

// 2
int *array2 = new int[vector.size()];
std::copy(vector.begin(), vector.end(), array2);

// 3
int *array3 = new int[vector.size()];
memcpy(array3, vector.data(), vector.size() * sizeof(int));
```