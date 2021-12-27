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
- [20211223](#12-23-2021)
- [20211224](#12-24-2021)
- [20211225](#12-25-2021)
- [20211226](#12-26-2021)
- [20211227](#12-27-2021)

<br/>

#### Category

<br/>

##### Backtracking
- [Restore-IP-Address](#restore-ip-address)
- [Generate-Parentheses](#generate-parentheses)
- [Remove-Invalid-Parentheses](#remove-invalid-parentheses)
- [Brace-Expansion](#brace-expansion)
- [Time-Needed-To-Inform-All-Employees](#time-needed-to-inform-all-employees)
- [Number-Of-Distinct-Islands](#number-of-distinct-islands)
- [Palindrome-Partitioning](#palindrome-partitioning)

<br/>

#### Weekly Contest

- [12252021](#weekly-contest-273)

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

### Brace Expansion
Leetcode 1087
```java
    public String[] expand(String s) {
        
        List<List<String>> list = new ArrayList<>();
        
        String[] removeLeftBracket = s.split("\\{");
        for (String leftRemoved : removeLeftBracket) {
            if (leftRemoved.length() > 0) {
                String[] removeRightBracket = leftRemoved.split("\\}");
                for (String rightRemoved : removeRightBracket) {
                    if (rightRemoved.length() > 0) {
                        String[] chars = rightRemoved.split(",");
                        List<String> subList = new ArrayList<>();
                        for (String ch : chars) {
                            subList.add(ch);
                        }
                        Collections.sort(subList);
                        list.add(subList);
                    }
                }
            }
        }
        
        List<String> ansList = new ArrayList<>();
        
        dfs(new StringBuilder(), 0, list, ansList);
        
        String[] ans = new String[ansList.size()];
        
        for (int i = 0; i < ans.length; i++) {
            ans[i] = ansList.get(i);
        }
        
        return ans;
    }
    
    private void dfs(StringBuilder sb, int idx, List<List<String>> list, List<String> ansList) {
        if (idx == list.size()) {
            ansList.add(sb.toString());
            return;
        }
        
        List<String> curSubList = list.get(idx);
        for (int i = 0; i < curSubList.size(); i++) {
            sb.append(curSubList.get(i));
            dfs(sb, idx + 1, list, ansList);
            sb.setLength(sb.length() - 1);
        }
        
        return;
    }
```

<br/>

### 12 23 2021
Leetcode 210 Course Schedule II
```java
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        Map<Integer, List<Integer>> order = new HashMap<>();
        for(int[] prereq : prerequisites)
            order.computeIfAbsent(prereq[0], v -> new ArrayList<>()).add(prereq[1]);

        List<Integer> sequence = new ArrayList<>();
        boolean[] visited = new boolean[numCourses];
        for(int i=0;i<numCourses;i++) {
            if (!createSequence(i, order, sequence, new HashSet<>(), visited)) return new int[0];
        }
        return sequence.stream().mapToInt(Integer::intValue).toArray();
    }

    private boolean createSequence(int i, Map<Integer, List<Integer>> order, List<Integer> sequence, Set<Integer> cycleCheck, boolean[] visited) {
        if (visited[i]) return true;
        visited[i]=true;

        cycleCheck.add(i);

        for(int dependent :  order.getOrDefault(i, Collections.emptyList())) {
            if (cycleCheck.contains(dependent)) return false;
            if (!createSequence(dependent, order, sequence, cycleCheck, visited)) return false;
        }

        sequence.add(i);
        cycleCheck.remove(i);
        return true;
    }
```

<br/>

### Time Needed To Inform All-Employees
Leetcode 1376 
```java
    // top-down
    private int ans = 0;
    
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        Map<Integer, List<Integer>> members = new HashMap<>();
        
        for (int i = 0; i < manager.length; i++) {
            int key = manager[i];
            
            if (key != -1) {
                members.putIfAbsent(key, new ArrayList<>());
                members.get(key).add(i);
            }
        }
        
        dfs(members, headID, 0, informTime);
        
        return ans;
    }
    
    private void dfs(Map<Integer, List<Integer>> members, int cur, int time, int[] informTime) {
        ans = Math.max(time, ans);
        
        if (!members.containsKey(cur)) {
            return;
        }
        
        for (int next : members.get(cur)) {
            dfs(members, next, time + informTime[cur], informTime);
        }
        
        return;
    }

    // bottom-up
    Map<Integer, List<Integer>> graph;
    int res = 0;
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        int res = 0;
        for(int i=0; i<n; i++){
            res = Math.max(res, dfs(i, manager, informTime));
        }
        return res;
    }
    
    private int dfs(int i, int[] manager, int[] informTime){
        if(manager[i] != -1){
            informTime[i] += dfs(manager[i], manager, informTime);
            manager[i] = -1;
        }
        return informTime[i];
    }
```

<br/>

### Number of Distinct Islands
Leetcode 694
```java
    private int[][] grid;
    private boolean[][] seen;
    private Set<Pair<Integer, Integer>> currentIsland;
    private int currRowOrigin;
    private int currColOrigin;
    
    private void dfs(int row, int col) {
        if (row < 0 || row >= grid.length || col < 0 || col >= grid[0].length) return;
        if (grid[row][col] == 0 || seen[row][col]) return;
        seen[row][col] = true;
        currentIsland.add(new Pair<>(row - currRowOrigin, col - currColOrigin));
        dfs(row + 1, col);
        dfs(row - 1, col);
        dfs(row, col + 1);
        dfs(row, col - 1);    
    }
    
    public int numDistinctIslands(int[][] grid) {
        this.grid = grid;
        this.seen = new boolean[grid.length][grid[0].length];   
        Set<Set<Pair<Integer, Integer>>> islands = new HashSet<>();
        for (int row = 0; row < grid.length; row++) {
            for (int col = 0; col < grid[0].length; col++) {
                this.currentIsland = new HashSet<>();
                this.currRowOrigin = row;
                this.currColOrigin = col;
                dfs(row, col);
                if (!currentIsland.isEmpty()) islands.add(currentIsland);
            }
        }         
        return islands.size();
    }
```

<br/>

### Palindrome Partitioning
Leetcode 131
```java
    public List<List<String>> partition(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (j + i >= n) {
                    continue;
                }
                
                if (i == 0) {
                    dp[j][j] = true;
                } else if (i == 1) {
                    if (s.charAt(j) == s.charAt(j + 1)) {
                        dp[j][j + 1] = true;       
                    }
                } else {
                    if (s.charAt(j) == s.charAt(j + i) && dp[j + 1][j + i - 1]) {
                        dp[j][j + i] = true;
                    }
                }
            }
        }
        
        List<List<String>> ans = new ArrayList<>();
        dfs(dp, s, 0, new ArrayList<>(), ans);
        
        return ans;
    }
    
    private void dfs(boolean[][] dp, String s, int cur, List<String> curList, List<List<String>> ans) {
        if (cur == s.length()) {
            ans.add(new ArrayList<>(curList));
            return;
        }
        
        for (int i = cur; i < s.length(); i++) {
            if (dp[cur][i]) {
                curList.add(s.substring(cur, i + 1));
                dfs(dp, s, i + 1, curList, ans);
                curList.remove(curList.size() - 1);
            }
        }
        
        return;
    }
```

<br/>

### 12 24 2021
Leetcode 56 Merge Intervals
```java
    public int[][] merge(int[][] intervals) {
        
        Arrays.sort(intervals, (x, y) -> {
            if (x[0] == y[0]) {
                return x[1] - y[1];
            }
            return x[0] - y[0];
        });
        
        Deque<int[]> stack = new ArrayDeque<>();
        
        stack.addLast(intervals[0]);
        
        for (int i = 1; i < intervals.length; i++) {
            int[] cur = intervals[i];
            int[] prev = stack.getLast();
            if (cur[0] <= prev[1]) {
                stack.removeLast();
                int[] merged = new int[] {prev[0], Math.max(cur[1], prev[1])};
                stack.addLast(merged);
            } else {
                stack.addLast(cur);
            }
        }
        
        int[][] ans = new int[stack.size()][2];
        
        for (int i = 0; i < ans.length; i++) {
            ans[i] = stack.pollFirst();
        }
        
        return ans;
    }
```

<br/>

### 12 25 2021
Leetcode 227 Basic Calculator II
```java
    public int calculate(String s) {
        if (s == null || s.isEmpty()) return 0;
        int len = s.length();
        Stack<Integer> stack = new Stack<Integer>();
        int currentNumber = 0;
        char operation = '+';
        for (int i = 0; i < len; i++) {
            char currentChar = s.charAt(i);
            if (Character.isDigit(currentChar)) {
                currentNumber = (currentNumber * 10) + (currentChar - '0');
            }
            if (!Character.isDigit(currentChar) && !Character.isWhitespace(currentChar) || i == len - 1) {
                if (operation == '-') {
                    stack.push(-currentNumber);
                }
                else if (operation == '+') {
                    stack.push(currentNumber);
                }
                else if (operation == '*') {
                    stack.push(stack.pop() * currentNumber);
                }
                else if (operation == '/') {
                    stack.push(stack.pop() / currentNumber);
                }
                operation = currentChar;
                currentNumber = 0;
            }
        }
        int result = 0;
        while (!stack.isEmpty()) {
            result += stack.pop();
        }
        return result;
    }        
```

### 12 26 2021
Leetcode 973 K Closest Points to Origin
```java
    public int[][] kClosest(int[][] points, int k) {
        int[][] ans = new int[k][2];
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((x, y) -> {
            int xDis = x[0] * x[0] + x[1] * x[1];
            int yDis = y[0] * y[0] + y[1] * y[1];
            
            return xDis - yDis;
        });
        
        for (int[] point : points) {
            pq.offer(point);
        }
        
        int idx = 0;
        while (idx < k) {
            int[] cur = pq.poll();
            ans[idx][0] = cur[0];
            ans[idx][1] = cur[1];
            idx++;
        }
        
        return ans;
    }
```

<br/>

### Weekly Contest 273
A Number After a Double Reversal
```java
    public boolean isSameAfterReversals(int num) {
        if (num == 0) {
            return true;
        }
        
        return num % 10 != 0;
    }
```

Execution of All Suffix Instructions Staying in a Grid
```java
    private Map<Character, int[]> map;
    private int[] ans;
    private int n;
    
    public int[] executeInstructions(int n, int[] startPos, String s) {
        this.n = n;
        
        map = new HashMap<>();
        map.put('U', new int[]{-1, 0});
        map.put('D', new int[]{1, 0});
        map.put('L', new int[]{0, -1});
        map.put('R', new int[]{0, 1});
        
        ans = new int[s.length()];
        for (int i = 0; i < s.length(); i++) {
            dfs(startPos[0], startPos[1], i, i, 0, s);
        }
        
        return ans;
    }
    
    private void dfs(int x, int y, int from, int cur, int count, String s) {
        if (x < 0 || x >= n || y < 0 || y >= n) {
            ans[from] = count - 1;
            return;
        }
        
        if (cur == s.length()) {
            ans[from] = count;
            return;
        }
        
        ans[from] = count;
        
        int[] move = map.get(s.charAt(cur));
        int xNext = x + move[0];
        int yNext = y + move[1];
        
        dfs(xNext, yNext, from, cur + 1, count + 1, s);
    }
```

Intervals Between Identical Elements
```java
    // Prefix Sum
```

Recover the Original Array
```java
    // Traverse all possible K
```

<br/>

### 12 27 2021
Leetcode 476 Number Complement
```java
    public int findComplement(int num) {
        // n is a length of num in binary representation
        int n = (int)( Math.log(num) / Math.log(2) ) + 1;
        // bitmask has the same length as num and contains only ones 1...1
        int bitmask = (1 << n) - 1;
        // flip all bits
        return bitmask ^ num;
    }
```