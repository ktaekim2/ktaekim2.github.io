---
layout: post
title: "Optimizing Array Operations: A Move Zeroes Retrospective"
date: 2026-01-27 12:00:00 +0900
categories: [Algorithms, Optimization, Java]
tags: [LeetCode, Array, Two Pointers, Time Complexity]
description: "An analysis of the 'Move Zeroes' problem, exploring the trade-off between minimizing write operations and code readability using a two-pointer approach."
---

## Introduction

In system reliability engineering (SRE) and high-performance computing, **optimizing data movement** is often just as critical as optimizing algorithmic complexity. Even simple array manipulations can reveal interesting trade-offs between memory writes and code simplicity.

This post explores the "Move Zeroes" problem (LeetCode 283), analyzing two different $O(N)$ approaches and discussing why minimizing write operations matters.

## The Problem

Given an integer array `nums`, move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.
**Note:** You must do this in-place without making a copy of the array.

**Example:**
```
Input: nums = [0,1,0,3,12]
Output: [1,3,12,0,0]
```

---

## Approach 1: The "Fill and Flush" (Two-Pass)

My initial intuition was straightforward:
1.  Iterate through the array and write all non-zero elements to the front.
2.  Fill the remaining positions with zeros.

### Implementation

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int writePointer = 0;
        
        // Pass 1: Consolidate non-zero elements
        for (int readPointer = 0; readPointer < nums.length; readPointer++) {
            if (nums[readPointer] != 0) {
                nums[writePointer] = nums[readPointer];
                writePointer++;
            }
        }
        
        // Pass 2: Fill remaining space with zeros
        for (int i = writePointer; i < nums.length; i++) {
            nums[i] = 0;
        }
    }
}
```

### Analysis
*   **Time Complexity:** $O(N)$. We iterate through the array twice (technically $2N$ operations in the worst case).
*   **Space Complexity:** $O(1)$. No extra space used.
*   **Pros:** Very readable and intuitive. Sequential writes are cache-friendly.
*   **Cons:** Performs unnecessary writes if the array has very few zeros (e.g., `[1, 1, 1, 0, 1]`).

---

## Approach 2: The "Snowball" Swap (One-Pass)

We can optimize this by performing a **swap** whenever we find a non-zero element. The `writePointer` essentially tracks the position of the "first zero" in our consolidated zero-block (the snowball).

### Implementation

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int writePointer = 0;
        
        for (int readPointer = 0; readPointer < nums.length; readPointer++) {
            if (nums[readPointer] != 0) {
                // Swap non-zero element with the zero at writePointer
                int temp = nums[writePointer];
                nums[writePointer] = nums[readPointer];
                nums[readPointer] = temp;
                
                writePointer++;
            }
        }
    }
}
```

### Optimization Analysis
This approach is also $O(N)$ but technically performs fewer operations in specific scenarios.
*   It combines the "move" and "fill" steps into a single iteration.
*   It minimizes the total number of writes when the array is sparse (mostly zeros).
*   However, swapping involves 3 assignments per operation, whereas the two-pass method involves 1 assignment per element.

## Conclusion

For most modern CPUs, the **Two-Pass approach** is often faster due to **Branch Prediction** and **Cache Locality**. Sequential memory writes are extremely efficient. However, the **Swap approach** is intellectually satisfying as it minimizes the logic into a single atomic pass.

In an interview or architectural discussion, acknowledging this **trade-off between "Algorithmic Elegance" and "Hardware Reality"** demonstrates a deeper understanding of system performance.

<details>
<summary><strong>Original Notes (Korean)</strong></summary>
<div markdown="1">

### 1/27 Move Zeroes 회고

**초기 접근 (Two-Pass):**
처음에는 `writePointer`를 두고 0이 아닌 숫자들을 앞으로 다 밀어넣은 다음, 나머지 뒷부분을 0으로 채우는 방식을 썼다.
직관적이고 코드가 읽기 쉽다. 시간복잡도도 $O(N)$이라 충분히 빠르다.

**최적화 접근 (Swap):**
0을 만났을 때 스왑을 해버리면 loop를 두 번 돌 필요가 없다.
`writePointer`가 가리키는 곳(0이 있는 곳)과 현재 숫자를 바꾸면, 자연스럽게 0들이 뒤로 밀려나는 "눈덩이(Snowball)" 효과가 난다.

**생각할 거리:**
실제 하드웨어 레벨에서는 Two-Pass가 더 빠를 수도 있다. (Sequential Write가 빠르니까).
하지만 "불필요한 쓰기 작업(Write Operation)을 줄인다"는 관점에서는 Swap 방식이 의미가 있다.
</div>
</details>

{% comment %}
### Meta Section: Interview Prep

#### 1. Pitch Script (1 Minute)
"I solved the 'Move Zeroes' problem using a two-pointer technique.
Initially, I used a two-pass approach: first moving all non-zero elements to the front, then filling the rest with zeros. It's readable and cache-friendly.
However, to optimize for the number of write operations, I also explored a one-pass solution using swapping. This maintains the relative order of elements while performing the operation in a single traversal. In a real-world system, I would choose the approach based on the specific constraints—whether we prioritize code readability or minimizing memory writes."

#### 2. Vocabulary Mapping
*   **In-place modification:** 제자리 연산 (별도 메모리 안 씀)
*   **Relative order:** 상대적 순서
*   **Traversal:** 순회 (Loop 도는 것)
*   **Sequential writes:** 순차적 쓰기
*   **Cache locality:** 캐시 지역성

#### 3. Why this post?
To demonstrate that I don't just solve problems, but I think about **efficiency** and **hardware implications** (SRE Mindset).
{% endcomment %}
