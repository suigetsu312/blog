---
date: 2026-03-02T00:35:15+08:00
draft: false
title: "TopKFrequentElements"
categories: ["programming"]
tags: ["C++", "STL", "Heap", "HashTable"]
difficulty: "Medium"
source: "LeetCode"
problem_id: 347
---

# TopKFrequentElements

**Difficulty:** Medium  
**Topics:** Hash Table, Heap, Frequency Counting

> Given an integer array nums and an integer k, return the k most frequent elements. You may return the answer in any order.

## Approach

This problem asks us to find the k most frequent elements.
We first use an `unordered_map` to count the frequency of each element.
Then we use a `min-heap` of size k to maintain the current top-k elements.
When the heap size exceeds k, we remove the smallest element.
Finally, we obtain a list of size k that contains the top-k elements.

## Code

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int,int> freq;

        for(int n : nums)
            freq[n]++;

        // min heap (freq, num)
        priority_queue<
            pair<int,int>,
            vector<pair<int,int>>,
            greater<pair<int,int>>
        > pq;

        for(auto& [num, f] : freq) {
            pq.push({f, num});
            if(pq.size() > k)
                pq.pop();
        }

        vector<int> res;
        while(!pq.empty()) {
            res.push_back(pq.top().second);
            pq.pop();
        }

        return res;
    }
};

```

## Complexity Analysis

> Let n be the size of the input array, and u be the number of unique elements.
> Here, u is the number of unique elements stored in the unordered_map, and k is the required number of most frequent elements.

time complexity : $O(n + u \log k)$
space complexity : $O(u + k)$

---
