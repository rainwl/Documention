# std::vector&lt;int&gt; and int  *

## Assign int * to std::vector<int>

```C++
#include <vector>

int main() {
    int* arr = new int[5] {1, 2, 3, 4, 5};
    int n = 5; // 数组的大小

    std::vector<int> v(arr, arr + n);

    delete[] arr; // 如果使用new分配了arr，记得释放内存
    return 0;
}
```

```C++
#include <vector>

int main() {
    int* arr = new int[5] {1, 2, 3, 4, 5};
    int n = 5; // 数组的大小

    std::vector<int> v;
    for (int i = 0; i < n; ++i) {
        v.push_back(arr[i]);
    }

    delete[] arr; // 如果使用new分配了arr，记得释放内存
    return 0;
}
```

```C++
#include <vector>

int main() {
    int* arr = new int[5] {1, 2, 3, 4, 5};
    int n = 5; // 数组的大小

    std::vector<int> v;
    v.assign(arr, arr + n);

    delete[] arr; // 如果使用new分配了arr，记得释放内存
    return 0;
}
```

```C++
#include <vector>
#include <algorithm> // for std::copy

int main() {
    int* arr = new int[5] {1, 2, 3, 4, 5};
    int n = 5; // 数组的大小

    std::vector<int> v(n);
    std::copy(arr, arr + n, v.begin());

    delete[] arr; // 如果使用new分配了arr，记得释放内存
    return 0;
}
```

## Assign std::vector<int> to int *

```C++
#include <vector>

int main() {
    std::vector<int> v = {1, 2, 3, 4, 5};

    // 分配内存
    int* arr = new int[v.size()];

    // 复制数据
    for (size_t i = 0; i < v.size(); ++i) {
        arr[i] = v[i];
    }

    // 使用 arr...

    // 记得释放内存
    delete[] arr;
    return 0;
}
```

```C++
#include <vector>
#include <algorithm> // for std::copy

int main() {
    std::vector<int> v = {1, 2, 3, 4, 5};

    // 分配内存
    int* arr = new int[v.size()];

    // 使用 std::copy 复制数据
    std::copy(v.begin(), v.end(), arr);

    // 使用 arr...

    // 记得释放内存
    delete[] arr;
    return 0;
}
```

```C++
#include <vector>
#include <cstring> // for memcpy

int main() {
    std::vector<int> v = {1, 2, 3, 4, 5};

    // 分配内存
    int* arr = new int[v.size()];

    // 使用 memcpy 复制数据
    memcpy(arr, v.data(), v.size() * sizeof(int));

    // 使用 arr...

    // 记得释放内存
    delete[] arr;
    return 0;
}
```