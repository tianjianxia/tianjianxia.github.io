---
layout: post
title: Leetcode daily
tags: [leetcode]
---

<br/>

Long separating from Leetcode and algorithm leads my failure on my recent online assesment, SHAME ON ME. Thus I would like to restart my daily leetcode training and the Saturday contest. The rule I obey will present below.

- Record every daily challenge
- Follow algorithm category on [Zhihu](https://zhuanlan.zhihu.com/p/349940945), one category once a time period. Recorded at [My google sheet](https://docs.google.com/spreadsheets/d/1JoyLqikGBHg1AVwxTBICTxjAal9bVbj03D9Ek7VKc58/edit#gid=0)
- Record every Saturday contest

<br/>

***

<br/>

## Index

<br/>

#### Daily Challenge
- [20211219](#12-19-2021)
- [20211220](#12-20-2021)

<br/>

#### Category

<br/>

#### Weekly Contest

<br/>

<br/>

***

<br/>
<br/>
<br/>

### 12 19 2021
Leetcode 394 Decode String
```java
    // One Stack
    public String decodeString(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        
        for (char c : s.toCharArray()) {
            if (c == ']') {
                String str = "";
                while (!stack.isEmpty() && stack.getLast() != '[') {
                    str = stack.pollLast() + str;
                }
                
                stack.pollLast();
                
                String numStr = "";
                while (!stack.isEmpty() && Character.isDigit(stack.getLast())) {
                    numStr = stack.pollLast() + numStr;
                }
                Integer count = Integer.parseInt(numStr);
                for (int i = 0; i < count; i++) {
                    for (char ch : str.toCharArray()) {
                        stack.addLast(ch);
                    }
                }
            } else {
                stack.addLast(c);
            }
        }
        
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) {
            char c = stack.pollFirst();
            sb.append(c);
        }
        
        return sb.toString();
    }

    // Two Stack
    public String decodeString(String s) {
        Deque<Integer> countStack = new ArrayDeque<>();
        Deque<StringBuilder> stringStack = new ArrayDeque<>();
        StringBuilder currentString = new StringBuilder();
        int k = 0;
        for (char ch : s.toCharArray()) {
            if (Character.isDigit(ch)) {
                k = k * 10 + ch - '0';
            } else if (ch == '[') {
                // push the number k to countStack
                countStack.addLast(k);
                // push the currentString to stringStack
                stringStack.addLast(currentString);
                // reset currentString and k
                currentString = new StringBuilder();
                k = 0;
            } else if (ch == ']') {
                StringBuilder decodedString = stringStack.pollLast();
                // decode currentK[currentString] by appending currentString k times
                for (int currentK = countStack.pollLast(); currentK > 0; currentK--) {
                    decodedString.append(currentString);
                }
                currentString = decodedString;
            } else {
                currentString.append(ch);
            }
        }
        return currentString.toString();
    }
```

<br/>

### 12 20 2021
Leetcode 1200 Minimum Absolute Difference
```java
    // Sort solution
    public List<List<Integer>> minimumAbsDifference(int[] arr) {
        Arrays.sort(arr);
        int slow = 0;
        int fast = 1;
        int curAbs = Integer.MAX_VALUE;
        
        List<List<Integer>> ans = new ArrayList<>();
        
        while (fast < arr.length) {
            int abs = Math.abs(arr[fast] - arr[slow]);
            List<Integer> list = new ArrayList(List.of(arr[slow], arr[fast]));
            if (curAbs > abs) {
                ans.clear();
                ans.add(list);
                curAbs = abs;
            } else if (curAbs == abs) {
                ans.add(list);
            }
            
            slow++;
            fast++;
        }
        
        return ans;
    }

    // Use index as difference
    public List<List<Integer>> minimumAbsDifference(int[] arr) {
        // Initialize the auxiliary array `line`.
        // Keep a record of the minimum element and the maximum element.
        int minElement = arr[0];
        int maxElement = arr[0];
        for (int num : arr) {
            minElement = Math.min(minElement, num);
            maxElement = Math.max(maxElement, num);
        }
        int shift = -minElement;
        int[] line = new int[maxElement - minElement + 1];
        List<List<Integer>> answer = new ArrayList();
        
        // For each integer `num` in `arr`, we increment line[num + shift] by 1.
        for (int num : arr) {
            line[num + shift] = 1;
        }
        
        // Start from the index representing the minimum integer, initialize the 
        // absolute difference `min_pair_diff` as a huge value such as
        // `max_element - min_element` in order not to miss the absolute 
        // difference of the first pair.
        int minPairDiff = maxElement - minElement;
        int prev = 0;
        
        // Iterate over the array `line` and check if line[curr] 
        // holds the occurrence of an input integer.
        for (int curr = 1; curr <= maxElement + shift; ++curr) {
            // If line[curr] == 0, meaning there is no occurrence of the integer (curr - shift) 
            // held by this index, we will move on to the next index.
            if (line[curr] == 0) {
                continue;
            }
            
            // If the difference (curr - prev) equals `minPairDiff`, we add this pair 
            // {prev - shift, curr - shift} to the answer list. Otherwise, if the difference 
            // (curr - prev) is smaller than `minPairDiff`, we empty the answer list and add 
            // the pair {curr - shift, prev - shift} to the answre list and update the `minPairDiff`
            if (curr - prev == minPairDiff) {       
                answer.add(Arrays.asList(prev - shift, curr - shift));
            } else if (curr - prev < minPairDiff) {
                answer = new ArrayList();
                minPairDiff = curr - prev;
                answer.add(Arrays.asList(prev - shift, curr - shift));                
            } 

            // Update prev as curr.           
            prev = curr;
        }  
        
        return answer;
    }
```