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
[20211219](#12-19-2021)

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