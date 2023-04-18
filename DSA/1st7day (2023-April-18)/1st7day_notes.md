# Arrays

### Insertion - at begin/end/any

- for accessing the last use `arr.length-1`
- for accessing first `index 0`

### 1. Removing even nums

1. Iterate through the array from beginning to the end.
2. Count the number of odds.
3. Create an array of counts of odd number.
4. put the values that are not even numbers.

### 2. Reversing array

We can use 2 pointers which has the TC : O(n).

- Initialize two pointers, `left` and `right`, pointing to the first and last elements of the array, respectively.
- Swap the elements at the `left` and `right` indices.
- Increment `left` pointer and decrement `right` pointer.
- Repeat steps 2-3 until `left` < `right`.
- The array is now in reversed order.

> this is for when we want to alter the same array.

If we want to make a new array, that is reversed:

- Iterate from beginning to the end.

- assign it `i`th index of new Array, to `arr.length-1 -i`

  this way, each time, the last element will get stored in the new array.

### 3. How to find Minimum/Maximum value in Array

There are 2 conditions :

1. array is sorted
   - then we can use binary search to find the min/max and store the it in variable.
   - TC : O(log n)
2. array is unsorted
   - then we can use a simple for loop and check every element and if found, store it in min/max variable.
   - TC : O(n)

### 4. Find the second Max/Min value in array

example : [13, 34, 14, 34, 33, 31]

first max = 5, the second max = 4

- The idea is using two variables

  - max & secondMax which by default have `Integer.MIN_VALUE or Integer.MAX_VALLUE`

- While iteration, when we compare max and with array value, if the condition turn out true, we update `secondMax to max & max to the array element` because after we have found the max, the previous number holding that number would have been max, so it'll be secondMax,

  else if , (we're using else if bcuz' suppose our secondMax is 13 and max is 34, in the next iteration which `i=2` 14 is greater than secondMax but not max, so we need to update secondMax), so in the condition, simply check if the current element is greater than secondMax && current element is not equal to the max. 

  - the second check : if there was a duplicate max value like in the example, it won't update the secondMax, because we already know what the max is so updating the secondMax to max is pretty useless and the code will broke.

```java
private int findSecondMax(int[] arr) {
	int max = Integer.MIN_VALUE;
    int secondMax = Integer.MIN_VALUE;
    
    for (int i=0; i<arr.length; i++) {
        if (arr[i] > max) {
            secondMax = max; // so that the second max is running behind max
            max = arr[i];
        } else if (
        	arr[i] > secondMax
        &&  arr[i] != max // so we can check for duplicate max, if found, don't update second max
        ) {
            secondMax = arr[i];
        }
    }
    return secondMax;
}
```

- TC : O(n)

### 5. Move zeroes to end of array

Q. given an array of integers, write a function to move all o's to end of array while maintaining the relative order of the non-zero elements.

e.g. [0, 1, 0, 4, 12]

solution [1, 4, 12, 0, 0]

- The idea is to use Two pointers.
  - One pointer focuses on non-zero elements
  - Another pointer focuses on zero elements
  - and in this two-pointers, where one is at non-zero element, and the other is at zero element, we simply perform a swap. 

```java
public void moveZeroesToEnd(int[] arr) {
    int holdZeroElement = 0;
    for (int nonZero=o; nonZero<arr.length; nonZero++) {
        // check if the current element is zero or not, if not do nothing, else swap zero with element
        if (
            arr[nonZero]!=0
        &&  arr[holdZeroElement]==0) {
            int temp = arr[nonZero];
            arr[nonZero] = arr[holdZeroElement];
            arr[holdZeroElement] = temp;
        }
        // if the current element is not zero, move to next element
        if (arr[holdZeroElement] != 0)
            holdZeroElement++;
    }
}
```

or

```java
public void moveZeroesToEnd(int[] nums) {
    int nonZero = 0; // pointer for non-zero elements
    int holdZeroElement = 0; // pointer for zero elements
    
    // iterate through the array
    while (nonZero < nums.length) {
        if (nums[nonZero] != 0) { // current element is not zero
            // swap with first zero element if any
            int temp = nums[nonZero];
            nums[nonZero] = nums[holdZeroElement];
            nums[holdZeroElement] = temp;
            holdZeroElement++; // increment zero pointer
        }
        nonZero++; // always increment non-zero pointer
    }
}
```

### 6. How to resize an array

What we do is we create a temporary array of size, let's say 2 times of the original array. And copy the elements of original inside the temporary array.

```Java
public int[] resize(int[] arr, int capacity) {
    int[] temp = new int[capacity];
    for (int i=0; i<arr.length; i++)
        temp[i] = arr[i];
	return temp;
}
```

### 7. Find the missing number in an array

<u>note</u> : array consists of distinct numbers from 1 to n.

Constraints : 

- TC : O(n)

```Java
// arr = {1,2,3,5,6}
public int findMissingNum(int[] arr) {
    int n = arr.length + 1; // bcuz' of the missing number
    int sum = n * (n-1) / 2;
    for (int num : arr)
        sum -= num;
    return sum;
}
//output  = 4
```

explanation :

- we get the sum of n natural numbers in the array. since it is distinct nums, then there will be only one of the same num. Then we'll subtract the sum from the sum of elements of our array. The answer of subtraction is our ans.
- <u>note</u> : we can use another approach where we find the sum of our array and subtract it from the sum as well. The answer won't change.

### How to check if a given string is palindrome or not

- using two pointers (more efficient since it returns false as soon as it finds an unequal word) :

```Java
public boolean isPalidrome(String word) {
    char[] charArray = word.toCharArray();
    int start = 0;
    int end = word.length() - 1;
    while (start<end) {
        if (charArray[start] != charArray[end])
            return false;
        start++;
        end--;
    }
    return true;
}
```

- by reversing the word and comparing it to the original word :

```Java
public boolean isPalindrome(String word) {
    // reverse the word
    String reversedWord = "";
    for (int i=word.length-1; i>=0; i--)
        reversedWord = word.charAt(i);
    return word == reversedWord;
}
```





# Binary Search

kunal

## Q3 : Ceiling of a number

**Find the ceiling number of  the provided target number in a sorted array.**  *(ceiling : smallest element in array that is greater than or equal to target)*

```java
Example :

arr = {2, 3, 5, 9, 14, 16, 18}
target = 4
    
ceiling num = 5
```

#### Approach

*The point is we're searching for a number in a sorted array, so we're going to use BinarySearch, and when we don't find the number, then we'll return the next element, i.e. start pointer.*

explanation :

in binary search algorithm, we try to find the number by minimizing the array into halves. When we don't find the element, the algorithm ends up with `start > end`, and when this happens, then the start element is the next greater element of target🎯!

#### ANSWER

```java
int ceilingNum(int arr[], int target) {
    if (target < arr[arr.length-1])
    return -1; // no ceiling
	int start = 0;
    int end = arr.length-1;
    int mid = 0;
    while (start <= end) {
  		mid = start + (end - start) / 2;
        if (target < arr[mid])
            end = mid -1;
        else if (target > arr[mid])
            start = mid + 1;
        else 
            return mid; //found the number
    }
    return arr[start];
}
```



## Q4 : Floor of a number

**Find the floor of a target in the array.** *(floor : greatest number smaller or equal to target)*

```java
Example :

arr = {2, 3, 5, 9, 14, 16, 18}
target = 15
    
ceiling num = 14
```

#### Approach

*The point is we're searching for a number in a sorted array, so we're going to use BinarySearch, and when we don't find the number, then we'll return the next element, i.e. start pointer.*

explanation :

in binary search algorithm, we try to find the number by minimizing the array into halves. When we don't find the element, the algorithm ends up with `start > end`, and when this happens, then the end element is the previous smaller element of target🎯!

#### ANSWER

```java
int floorNum(int arr[], int target) {
    if (target < arr[0])
        return -1; // no floor
	int start = 0;
    int end = arr.length-1;
    int mid = 0;
    while (start <= end) {
  		mid = start + (end - start) / 2;
        if (target < arr[mid])
            end = mid -1;
        else if (target > arr[mid])
            start = mid + 1;
        else 
            return mid; //found the number
    }
    return arr[end];
}
```



## Q5 : Find smallest letter greater than target

**Given a characters array `letters` that is sorted in <u>non-decreasing</u> order and a character `target`, return *the smallest character in the array that is larger than* `target`.**

**Note** that the letters wrap around.

- For example, if `target == 'z'` and `letters == ['a','b']`, the answer is `a`. (if there was no element greater than the target, return the first element)

#### Approach

Ceiling of a number!

#### ANSWER

```Java
public char nextGreatestLetter(char[] letters, char target) {
    int start = 0;
    int end = letters.length - 1;
    
    while (start <= end) {
        int mid = start + (end - start) / 2;
        if (target < letters[mid])
            end = mid - 1;
        else 
            start = mid + 1;
    }
    return letters[start % letters.length]; // explanation 👇
}
```

considering the example : ['a', 'b', 'c', 'd']

- **Iteration 1:** In the first iteration, `start` is set to `0`, `end` is set to `3`, and `mid` is calculated as `(0 + 3) / 2 = 1`. The value of `letters[mid]` is `'b'`, which is less than the target value `'c'`, so we update `start` to `mid + 1`, i.e., `start` becomes `2`.
- **Iteration 2:** In the second iteration, `start` is set to `2`, `end` is set to `3`, and `mid` is calculated as `(2 + 3) / 2 = 2`. The value of `letters[mid]` is `'c'`, which is equal to the target value `'c'`, so we update `start` to `mid + 1`, i.e., `start` becomes `3`.
- Since `start` is now equal to the length of the array, we need to wrap around to the beginning by taking the modulo of `start` with the length of the array. This gives us `start % letters.length` = `3 % 4` = `3`, so the index we are looking for is the element at position `3` in the original array. This corresponds to the letter `'d'`.

Therefore, the output of the method for the input `[a,b,c,d]` and target value `'c'` should be the letter `'d'`.

When we take the modulo of `3` with respect to `4`, we are essentially trying to divide `3` by `4` and find the remainder. Here's one way to calculate this:

```java
   0    <-- quotient (how many times 4 goes into 3? none)
4) 3    <-- dividend (what we're dividing)
   --
   3     <-- remainder (what's left over after dividing)
```

## Q6 : Find First and Last Position of Element in Sorted Array <span style='color: orange'>medium</span>

Given an array of integers `nums` sorted in ascending order, find the starting and ending position of given `target` value.

If `target` is not found in the array, return `[-1, -1]`

You must write an algorithm with `O(log n)` runtime complexity.

```java
Example :
input : nums = [5,7,7,8,8,10], target = 8
output: [3,4]
```



### Approach

##### <span style='color: red'>Brute force</span> 

we can use 2 linear search. One from the start & One from the end. Whenever we see the target element, we mark the `start` and `end` as found!

##### <span style='color: green'>Prefered Solution</span>

Binary Search!

we can run binary search twice. One from the start  to find the first & another from the end to find the last. The complexity won't change ==> `log n + log n = log n`

There's a catch. When we perform binary search, we might end up with the mid element to be as found, but there might be a chance that the found number is not the first element. To solve that, we'll perform another binary search in the left/right side of array again. We can check this by giving a Boolean value to the method.

### ANSWER

```java
public int[] firstAndLastPosition(int[] nums, int target) {
    int foundIndex = new int {-1, -1};
    int start = search(nums, target, true);
    int end = search(nums, target, false);
    foundIndex[0] = start;
    foundIndex[1] = end;
    return foundIndex;
}

public int search(int[] nums, int target, boolean findStartIndex) {
    int foundIndex = -1;
    int start=0, mid=0, end = nums.length-1;
    while (start <= end) {
        mid = start + (end-start)/2;
        if (target < nums[mid])
            end = mid - 1;
        else if (target > nums[mid])
            start = mid + 1;
        else {
            // potential answer found
            foundIndex = mid;
            if (findStartIndex) // if the user said; yes i want to search from start
                end = mid - 1; // search left side of the array (to find first index occurence)
            else // otherwise, if the user said; no, I don't want to search the start
                start = mid + 1; // then search the right side of array (to find last index occurence)
        }
    }
    return foundIndex;
}
```

a little bit more compact code :

```java
public int[] firstAndLastPosition(int[] nums, int target) {
    int foundIndex = new int {-1, -1};
    foundIndex[0] = search(nums, target, true);
    if (foundIndex[0] != -1)
        foundIndex[1] = search(nums, target, false);
    return foundIndex;
}
```

here, we're saying, if you find the answer in the left side of middle, just then run the binary search, otherwise there is no need to run the right side, the index has been marked already by the `search(...)` method. 

???????????????????????????????????????? what ??????????

## Q7 : Find position of an element in a sorted array of infinite numbers

When it is saying "infinite numbers" that means we cannot use the `.length` function.

### Approach

#### <span style='color: red'>Brute force</span>

Linear search.

## > Finding the length of Subarray by indices

![Finding length of subarray by indices](C:\Users\aliro\Downloads\Eitaa Desktop\IMG_20230414_235400.jpg)



## > Doubling size of array after an index

Basic doubling is easy, just multiply by 2.
but in array, in binary search, the indexing is from zero **plus** start has also moved 1+middle.

![Doubling size of array after an index](C:\Users\aliro\Downloads\Eitaa Desktop\IMG_20230415_000346.jpg)

#### <span style='color: green'>Preferred Solution</span>

Binary Search.

There is a problem, we don't know the start and index 🤔🙄.

- We can solve this problem by searching in chunks. Search in a range of chunks (let's say 6 elements), if target wasn't found in that chunk, move that chunk to the next elements.

- We can solve this by reversing the logic of binary search. In binary search, we divide by 2 to find the middle element until we reach at a point where we have one element, So by reversing this logic, we'll start from one, and go all the way to the up by multiplying by until we find the target element.

  let's explain more :

  <div style='color: #8B0000'>
  [2, 3, 5, 5, 7, 8, 10, 19, 59, 69, 89, 91, ...] &nbsp;&nbsp;, &nbsp;&nbsp; target = 89<br>
  1. First find the range. We're going to start from the bottom that is, we'll start from index 0 and index 1 (start with box of size 2).<br>
  2. Condition for the target to lie in the range ==> target should be less than end. We'll keep increasing the size by 2 until target element is smaller than end. Let's explain by the example : <br>
     First iteration :<br>
     start = 0; &nbsp;&nbsp;&nbsp; end = 1;<br>
     we'll check if 89 is > 3. <br>
     Second iteration :
     start = 2; &nbsp;&nbsp;&nbsp; end = 5;<br>
     we'll check if 89 > 8, increase<br>
     ...<br>
      3. We'll check, is target, greater than the elements in that chunk, yes it is. Then grow the chunk by 2 times. And so on until we find the element.
  </div>

### ANSWER

```java
public int answer(int[] arr, int target) {
    // first find the range
    // first start with a box
    int start=0, end=0;
    
    // condition for the target to lie in the range (double the size of chunk and move ahead)
    while (target > arr[end]) {
     	int newStart = end + 1;
        // double the box
        // end = previous end + sizeofbox * 2
        end = end + (end-(start-1)) * 2;
        start = newStart;
    }
    return binarySearch(arr, target, start, end);
}

// simple binary search
private int binarySearch(int[] arr, int target, int start, int end) {
    while (start <= end) {
        int mid = start + (end-start) / 2;
        if (target < arr[mid])
            end = mid - 1;
        else if (target > arr[mid])
            start = mid + 1;
        else return mid;
    }
    return -1;
}
```

# Day 10 - 16-April-2023

nothing

# Day 11 - 17-April-2023

## Q8 : Peak index in a mountain array (bitonic array)

Let's call an array `arr` a **mountain** if the following properties hold :

- `arr.length >= 3`
- There exists some `i` with `0 < i < arr.length - 1` such that :
  - `arr[0] < arr[1] < ... arr[i-1] < arr[i]`
  - `arr[i] > arr[i+1] > ... > arr[arr.length - 1]`

Given an integer array `arr` that is **guaranteed** to be a mountain, return any `i` such that `arr[0] < arr[1] < .. arr[i - 1] < arr[i] > arr[i + 1] > ... > arr[arr.length - 1]` (does not contain duplicate element).

```java
example :
input : arr = [0,1,0]
output : 1
    
input : arr = [0,2,1,0]
output : 1
    
input : arr = [0,10,5,2]
output : 1
```

### Approach

#### <span style='color: green'>Preferred solution</span>

We know that the array is sorted. So in any case we're going to use **Binary search**. It's just different in order from the middle. At some point it is decreasing and increasing.

some possibilities :

- if the `arr[mid]` element is greater than `arr[mid+1]`, then we know that the answer is going to lie in the left hand side and the subarray is in decreasing form. (of course, the middle might be the possible answer.)
- if the `arr[mid]` element is smaller than `arr[mid+1]`, then we know that the answer is going to lie in the right hand side and the subarray is in increasing form.
- when will the loop break? when start and end point to one pointer(become equal)

### ANSWER

```java
public int peakIndexInMountainArray(int[] arr) {
    int start = 0;
    int end = arr.length - 1;
    
    while (start < end) {
        int mid = start + (end-start) / 2;
        if (arr[mid] > arr[mid+1]) {
            // you are in the decreasing part of array
            // this may be the potential ans, but look at left
            // this is why end != mid-1;
            end = mid;
        } else {
            // you are in ascending part of array
            start = mid + 1; // becuz we know that mid+1 element > mid element
        }
        // in the end, start == end and pointing to the largest num bcuz of 2 checks above
        return start; // or end since they both point to element
    }
}
```

## Find Peak element <span style='color:orange'>medium</span>

A peak element is an element that is strictly greater than its neighbors.

Given an integer array `nums`, find a peak element, and return its index. If the array contains multiple peaks, return the index to **any of the peaks**.
You may imagine that `nums[-1] = nums[n] = -♾️(infinite)`.
You must write an algorithm that runs in `O(log n)` time.

### ANSWER

literally the previous questions answer.

## Find in Mountain array <span style='color: crimson'>hard</span>

(*This problem is an interactive problem.*)
You may recall that an array `A` is a mountain array if and only if:

- `A.length >= 3`
- There exists some `i` with `0 < i < A.length - 1` such that :
  - `A[0] < A[1] < ... A[i-1] < A[i]`
  - `A[i] > A[i+1] > ... > A[A.length - 1]`

Given a mountain array `mountainArr`, return the **minimum** `index` such that
`mountainArr.get(index) == target`. If such an `index` doesn't exist, return
-`1`.
**You can't access the mountain array directly.** You may only access the array using a `MountainArray` interface :



## > Order Agnostic Binary Search

```java
public int orderAgnosticBS(int[] arr, int target, int start, int end) {
    // find wether the array is sorted in asc or not
    boolean isAsc = arr[start] < arr[end];
    
    while (start <= end) {
        int mid = start + (end-start) / 2;
        if (arr[mid] == target)
            return mid;
        if (isAsc) {
            if (target < arr[mid])
                end = mid - 1;
            else start = mid + 1;
        } else {
            if (target > arr[mid])
                end = mid - 1;
            else start = mid + 1;
        }
    }
    return -1;
}
```

### ANSWER

```java
public int searchInMountainArray(int[] arr, int target) {
    int peak = peakIndexInMountainArray(arr);
    int firstTry = orderAgnosticBS(arr, target, 0, peak);
    if (firstTry != -1)
        return firstTry
    // else try to search in second half
    return orderAgnosticBS(arr, target, peak+1, arr.legnth-1);
}

private int peakInMountainArray(int[] arr) {
    int start = 0;
    int end = arr.length - 1;
    
    while (start < end) {
        int mid = start + (end-start) / 2;
        if (arr[mid] > arr[mid+1]) //we are in desc
            end = mid;//might be potential ans
        else start = mid + 1;
    }
    return start; //or end, doesn't matters since both of them end up pointing to the same element
}
```

## Q9 : Search in Rotated Sorted Array <span style='color: orange'>medium</span>

There is an integer array `nums` sorted in ascending order (with **distinct** values).
Prior to being passed to your function, `nums` is **rotated** at an unknown pivot index `k(0 <= k < nums.length)` such that the resulting array is `[nums[k]`, `nums[k+1], ... , nums[n-1], nums[0], nums[1], ... , nums[k-1]]`**(0-indexed)**. For example, `[0,1,2,4,5,6,7]` might be rotated at pivot index `3` and become `[4,5,6,7,0,1,2]`.
Given the array `nums` **after** the rotation and an integer `target`, return the *index* of `target` *if it is in* `nums`,or `-1` *if it is not in* `nums`.
You must write an algorithm with `0(log n)` runtime complexity.

```java
Example 1:
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
    
Example 2:
Input: nums = [4,5,6,7,0,1,2], target 3
Output: —1
```

> `[9,33,5,5,6,1,6]`
>
> After one Rotation :
>
> `[6,9,33,5,5,6,1]`
>
> After second Rotation :
>
> `[1,6,9,33,5,5,6]`
>
> . . .

### Approach

#### <span style='color:red'>1- Find the pivot in the array</span>

The pivot is the first element that got rotated. In this example, since it is sorted array, then the largest number is the pivot. 

- find the pivot, then compare with the pivot
- search in first half `BS(0, pivot);`
- otherwise, search in second half `BS(pivot+1, end);`

> how to find the pivot? 
>
> 1. when you find `arr[mid] > arr[mid+1] element, i.e. pivot`. 
>
>    > `&& mid<end`, so that we won't get caught in `ArraysOutOfBoundException`.
>
> 2. when `arr[mid] < arr[mid-1] element, i.e. pivot`.
>
>    `&& mid>start`, so that we won't get caught in `ArraysOutOfBoundException`.
>
> 3. start element is >= middle element, then there is no need to search in the right hand side of array bcuz we know that it is sorted and rotated. so `end = mid - 1;`
>
> 4. start element is < middle element, then `start = mid + 1;`

```java
public int findPivot(int[] arr) {
    int start = 0;
    int end = arr.length - 1;
    while (start <= end) {
        int mid = start + (end-start)/2;
        //4 cases
        if (mid<end && arr[mid] > arr[mid+1])
            return mid;
        if (mid>start && arr[mid] < arr[mid-1])
            return mid-1;
        if (arr[mid] <= arr[start])
            end = mid - 1;
        else // arr[mid] > arr[start] 
            start = mid + 1;
        return -1;
    }
}
```

now, for the main answer :

- case 1 : `pivot element == target` | we found the answer

- case 2 : `start element is < target` | search case is reduced to the left side `BS(s, p-1);`

  why? bcuz all the numbers after the pivot are smaller than the start. So why check in the right side when we know, target is greater than those?

- case 3 : `target < start element` | `BS(pivot+1, end)`

### ANSWER

```java
public int search(int[] nums, int target) {
    int pivot = findPivot(nums);
    
    //if u didn't find a pivot, it means the array is not rotatted
    if (pivot == -1)
        //just do normal binary search
        return binarySearch(nums, target, 0, nums.length-1);
    //if pivot is found, you have found 2 asc arrays
    if (nums[pivot] == target) 
        return pivot;
    if (target >= nums[0])
        return binarySearch(nums, target, 0, pivot-1);
    return binarySearch(nums, target, pivot+1, nums.length-1); //else, target < start element
}
```

