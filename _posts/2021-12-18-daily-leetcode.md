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
- [20211221](#12-21-2021)
- [20211222](#12-22-2021)

<br/>

#### Category

<br/>

##### Backtracking
- [Restore-IP-Address](#Restore-IP-Address)
- [Generate-Parentheses](#Generate-Parentheses)
- [Remove-Invalid-Parentheses](#Remove-Invalid-Parentheses)

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

<br/>

### 12 21 2021
Leetcode 231 Power of two
```java
    // Recursive
    public boolean isPowerOfTwo(int n) {
        if (n == 1) {
            return true;
        }
        
        if (n % 2 != 0) {
            return false;
        }
        
        if (n < 1) {
            return false;
        }
        
        
        return isPowerOfTwo(n / 2);
    }

    // Bit
    public boolean isPowerOfTwo(int n) {
        if (n == 0) return false;
        long x = (long) n;
        return (x & (x - 1)) == 0;
    }
```

<br/>

### 12 22 2021
Leetcode 143 Reorder List
```java
    public void reorderList(ListNode head) {
        if (head == null) return;

        // find the middle of linked list [Problem 876]
        // in 1->2->3->4->5->6 find 4 
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        }

        // reverse the second part of the list [Problem 206]
        // convert 1->2->3->4->5->6 into 1->2->3->4 and 6->5->4
        // reverse the second half in-place
        ListNode prev = null, curr = slow, tmp;
        while (curr != null) {
        tmp = curr.next;

        curr.next = prev;
        prev = curr;
        curr = tmp;
        }

        // merge two sorted linked lists [Problem 21]
        // merge 1->2->3->4 and 6->5->4 into 1->6->2->5->3->4
        ListNode first = head, second = prev;
        while (second.next != null) {
        tmp = first.next;
        first.next = second;
        first = tmp;

        tmp = second.next;
        second.next = first;
        second = tmp;
        }               
    }
```

<br/>

### Restore IP Address
Leetcode 93
```java
    private List<String> ans = new ArrayList<>();
    
    public List<String> restoreIpAddresses(String s) {
        dfs(s, 0, 3, new StringBuilder(), new String());
        
        return ans;
    }
    
    private void dfs(String s, int cur, int remain, StringBuilder sb, String curStr) {
        if (cur >= s.length()) {
            return;
        }
        
        if (remain == 0) {
            String last = s.substring(cur, s.length());
            if (isValidPart(last)) {
                String validIP = sb.toString() + last;
                ans.add(validIP);
            }
            
            return;
        }
        
        if (!curStr.isEmpty()) {
            if (isValidPart(curStr)) {
                sb.append('.');
                dfs(s, cur, remain - 1, sb, "");
                sb.setLength(sb.length() - 1);
            }
            
        }
        
        sb.append(s.charAt(cur));
        curStr = curStr + s.charAt(cur);
        dfs(s, cur + 1, remain, sb, curStr);
        curStr = curStr.substring(0, curStr.length() - 1);
        sb.setLength(sb.length() - 1);
        
        return;
    }
    
    private Boolean isValidPart(String str) {
        Boolean len = str.length() <= 3;
        if (!len) {
            return false;
        }
        Boolean size = Integer.parseInt(str) < 256;
        Boolean startWithZero = str.equals("0") ? true : str.charAt(0) != '0';
        
        return len && size && startWithZero;
    }    
```

<br/>

### Generate Parentheses
Leetcode 22
```java
    private List<String> ans = new ArrayList<>();
    
    public List<String> generateParenthesis(int n) {
        dfs(new StringBuilder(), n, n);
        return ans;
    }
    
    private void dfs(StringBuilder sb, int left, int right) {
        if (left == 0 && right == 0) {
            String res = sb.toString();
            ans.add(res);
            return;
        }
        
        if (left > 0) {
            sb.append('(');
            dfs(sb, left - 1, right);
            sb.setLength(sb.length() - 1);
        }
        
        if (right > 0 && right > left) {
            sb.append(')');
            dfs(sb, left, right - 1);
            sb.setLength(sb.length() - 1);
        }
        
        return;
    }
```

<br/>

### Remove Invalid Parentheses
Leetcode 301
```java
    // Prework + DFS
    private List<List<Integer>> perms = new ArrayList<>();
    
    public List<String> removeInvalidParentheses(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        
        List<Character> list = new ArrayList<>();
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack.addLast(c);
            } else if (c == ')') {
                if (!stack.isEmpty() && stack.getLast() == '(') {
                    stack.removeLast();
                } else {
                    stack.addLast(c);
                }
            }
        }
        
        while (!stack.isEmpty()) {
            char wasted = stack.pollFirst();
            list.add(wasted);
        }
        
        dfs(list, 0, s, 0, new ArrayList<>());
        
        Set<String> ans = new HashSet<>();
        
        for (List<Integer> idxList : perms) {
            Set<Integer> set = new HashSet<>(idxList);
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < s.length(); i++) {
                char ch = s.charAt(i);
                if (!set.contains(i)) {
                    sb.append(ch);
                }
            }
            
            String str = sb.toString();
            if (isValid(str)) {
                ans.add(str);
            }
        }
        
        return new ArrayList<>(ans);
    }
    
    private void dfs(List<Character> list, int idx, String s, int start, List<Integer> idxList) {
        if (idx == list.size()) {
            List<Integer> res = new ArrayList<>(idxList);
            perms.add(res);
            return;
        }
        
        int cur = start;
        while (cur < s.length()) {
            while (cur < s.length() && s.charAt(cur) != list.get(idx)) {
                cur++;
            }
            
            idxList.add(cur);
            dfs(list, idx + 1, s, cur + 1, idxList);
            idxList.remove(idxList.size() - 1);
            
            cur++;
        }
        
        return;
    }
    
    private Boolean isValid(String s) {
        int count = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                count++;
            } else if (c == ')') {
                count--;
            }
            
            if (count < 0) {
                return false;
            }
        }
        
        return count == 0;
    }

    // DFS
    private Set<String> validExpressions = new HashSet<String>();

    private void recurse(
        String s,
        int index,
        int leftCount,
        int rightCount,
        int leftRem,
        int rightRem,
        StringBuilder expression) {

        // If we reached the end of the string, just check if the resulting expression is
        // valid or not and also if we have removed the total number of left and right
        // parentheses that we should have removed.
        if (index == s.length()) {
        if (leftRem == 0 && rightRem == 0) {
            this.validExpressions.add(expression.toString());
        }

        } else {
        char character = s.charAt(index);
        int length = expression.length();

        // The discard case. Note that here we have our pruning condition.
        // We don't recurse if the remaining count for that parenthesis is == 0.
        if ((character == '(' && leftRem > 0) || (character == ')' && rightRem > 0)) {
            this.recurse(
                s,
                index + 1,
                leftCount,
                rightCount,
                leftRem - (character == '(' ? 1 : 0),
                rightRem - (character == ')' ? 1 : 0),
                expression);
        }

        expression.append(character);

        // Simply recurse one step further if the current character is not a parenthesis.
        if (character != '(' && character != ')') {

            this.recurse(s, index + 1, leftCount, rightCount, leftRem, rightRem, expression);

        } else if (character == '(') {

            // Consider an opening bracket.
            this.recurse(s, index + 1, leftCount + 1, rightCount, leftRem, rightRem, expression);

        } else if (rightCount < leftCount) {

            // Consider a closing bracket.
            this.recurse(s, index + 1, leftCount, rightCount + 1, leftRem, rightRem, expression);
        }

        // Delete for backtracking.
        expression.deleteCharAt(length);
        }
    }

    public List<String> removeInvalidParentheses(String s) {

        int left = 0, right = 0;

        // First, we find out the number of misplaced left and right parentheses.
        for (int i = 0; i < s.length(); i++) {

        // Simply record the left one.
        if (s.charAt(i) == '(') {
            left++;
        } else if (s.charAt(i) == ')') {
            // If we don't have a matching left, then this is a misplaced right, record it.
            right = left == 0 ? right + 1 : right;

            // Decrement count of left parentheses because we have found a right
            // which CAN be a matching one for a left.
            left = left > 0 ? left - 1 : left;
        }
        }

        this.recurse(s, 0, 0, 0, left, right, new StringBuilder());
        return new ArrayList<String>(this.validExpressions);
    }
```

<br/>

