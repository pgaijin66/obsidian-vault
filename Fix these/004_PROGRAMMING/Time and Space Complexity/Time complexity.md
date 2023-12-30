- Time complexity is a measure of how the running time of an algorithm increases as the size of the input increases.
- It quantifies the amount of time taken by an algorithm to run as a function of the input size.
- It helps in analyzing and comparing the efficiency of different algorithms. 
- Common notations used to denote time complexity include O-notation (asymptotic upper bound), Ω-notation (asymptotic lower bound), and Θ-notation (asymptotically tight bound).


Big O - Worst case scenario
Omega Ω - Best case scenario

O(1) - Constant Time Complexity:
    - Accessing an element from an array by index.
O(N) - Linear Time Complexity:
    - Finding the maximum element in an unsorted array by iterating through each element.
    - Linear search in an unsorted list.
O(N*N) - Quadratic Time Complexity:
    - Bubble sort: Sorting an array by comparing and swapping adjacent elements.
O(logN) - Logarithmic Time Complexity:
    - Binary search in a sorted array.
O(NlogN) - Linearithmic Time Complexity:
    - Merge sort: Sorting an array by dividing it into halves and merging them recursively.

## O(1) - Constant Time Complexity
```go
func getMaxElement(arr []int) int {
    max := arr[0]
    for i := 1; i < len(arr); i++ {
        if arr[i] > max {
            max = arr[i]
        }
    }
    return max
}
```


## O(N) - Linear Time Complexity

```go
func linearSearch(arr []int, target int) bool {
    for _, num := range arr {
        if num == target {
            return true
        }
    }
    return false
}

```


## O(N * N) - Quadratic Time Complexity

```go
func bubbleSort(arr []int) {
    n := len(arr)
    for i := 0; i < n-1; i++ {
        for j := 0; j < n-i-1; j++ {
            if arr[j] > arr[j+1] {
                arr[j], arr[j+1] = arr[j+1], arr[j]
            }
        }
    }
}

```


## O(logN) - Logarithmic Time Complexity

```go
func binarySearch(arr []int, target int) bool {
    low, high := 0, len(arr)-1
    for low <= high {
        mid := (low + high) / 2
        if arr[mid] == target {
            return true
        } else if arr[mid] < target {
            low = mid + 1
        } else {
            high = mid - 1
        }
    }
    return false
}

```


## O(NlogN) - Linearithmic Time Complexity

```go
func mergeSort(arr []int) []int {
    if len(arr) <= 1 {
        return arr
    }
    mid := len(arr) / 2
    left := mergeSort(arr[:mid])
    right := mergeSort(arr[mid:])
    return merge(left, right)
}

func merge(left, right []int) []int {
    merged := make([]int, 0, len(left)+len(right))
    for len(left) > 0 && len(right) > 0 {
        if left[0] <= right[0] {
            merged = append(merged, left[0])
            left = left[1:]
        } else {
            merged = append(merged, right[0])
            right = right[1:]
        }
    }
    merged = append(merged, left...)
    merged = append(merged, right...)
    return merged
}

```