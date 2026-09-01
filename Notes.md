# 2. Add Two Numbers

## Core Idea

Add digit by digit while maintaining `carry`:

```cpp
sum = d1 + d2 + carry;
carry = sum / 10;
digit = sum % 10;
```

Use a `dummy head` + `tail`, and handle different list lengths and the final carry in one loop:

```cpp
ListNode dummy(0);
ListNode* tail = &dummy;

while (l1 || l2 || carry) {
    int d1 = l1 ? l1->val : 0;
    int d2 = l2 ? l2->val : 0;

    int sum = d1 + d2 + carry;
    carry = sum / 10;

    tail->next = new ListNode(sum % 10);
    tail = tail->next;

    if (l1) l1 = l1->next;
    if (l2) l2 = l2->next;
}

return dummy.next;
```

## Mistakes to Remember

- `new ListNode(...)` returns a `ListNode*`
- Move the tail after adding a node: `tail = tail->next`
- Do not overwrite a state variable before finishing calculations that depend on its old value
- If one list ends early, treat the missing digit as `0`
- A `dummy head` avoids special handling for the first node

## Takeaway

> Dummy head + tail pointer + one unified loop.

---

# 3. Longest Substring Without Repeating Characters

## Core Idea

Use **Sliding Window + Last Seen Index**.

Maintain a valid window `[left, right]` with no duplicate characters:

```cpp
int seen[128] = {};
int left = 0;
int longest = 0;

for (int right = 0; right < s.size(); right++) {
    char c = s[right];

    left = max(left, seen[c]);
    seen[c] = right + 1;

    longest = max(longest, right - left + 1);
}
```

`seen[c]` stores the last index of `c` **plus 1**.  
Using `max()` ensures `left` never moves backward.

## Mistakes to Remember

- Always update the loop variable; otherwise it causes an infinite loop / TLE
- Scanning and copying `substring` repeatedly gives `O(n²)`
- When the character set is small and fixed, an array is faster and simpler than `unordered_map`
- `int seen[128] = {};` initializes all entries to `0`

## Complexity

- Time: `O(n)`
- Space: `O(1)`

## Takeaway

> Sliding window + last seen position; move `left` forward instead of rebuilding the substring.

# 5. Longest Palindromic Substring

## Core Idea

Use **Expand Around Center**.

Every palindrome is symmetric around its center.

For each index `i`, check:

```cpp
expand(i, i);       // odd-length palindrome
expand(i, i + 1);   // even-length palindrome
```

Expand while both sides are equal:

```cpp
while (left >= 0 && right < s.length() && s[left] == s[right]) {
    left--;
    right++;
}
```

After expansion stops, the valid palindrome is:

```cpp
left + 1 ... right - 1
```

with length:

```cpp
right - left - 1
```

## Recommended Pattern

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int start = 0;
        int maxLength = 1;

        for (int i = 0; i < s.length(); i++) {
            expand(s, i, i, start, maxLength);
            expand(s, i, i + 1, start, maxLength);
        }

        return s.substr(start, maxLength);
    }

private:
    void expand(const string& s, int left, int right,
                int& start, int& maxLength) {
        while (left >= 0 &&
               right < s.length() &&
               s[left] == s[right]) {
            left--;
            right++;
        }

        int length = right - left - 1;

        if (length > maxLength) {
            maxLength = length;
            start = left + 1;
        }
    }
};
```

## Mistakes to Remember

- Do not assume every longest substring problem uses sliding window
- Palindrome does not have the monotonic property needed for sliding window
- Need to check both odd and even centers
- After expansion stops, `left` and `right` have already moved one step too far

## Complexity

- Time: `O(n²)`
- Space: `O(1)`

## Takeaway

> Palindrome → think symmetry → expand around every possible center.

# 6. Zigzag Conversion

## Core Idea

The Zigzag pattern repeats with cycle length:

```cpp
cycle = 2 * numRows - 2;
```

Instead of building a matrix, directly read characters row by row.

For each row:

```cpp
for (int j = row; j < s.length(); j += cycle)
```

Middle rows also contain a diagonal character:

```cpp
second = j + cycle - 2 * row;
```

---

## Recommended Pattern

```cpp
class Solution {
public:
    string convert(string s, int numRows) {
        if (numRows == 1 || numRows >= s.length()) {
            return s;
        }

        string result;
        int cycle = 2 * numRows - 2;

        for (int row = 0; row < numRows; row++) {
            for (int j = row; j < s.length(); j += cycle) {
                result += s[j];

                int second = j + cycle - 2 * row;

                if (row != 0 &&
                    row != numRows - 1 &&
                    second < s.length()) {
                    result += s[second];
                }
            }
        }

        return result;
    }
};
```

## Mistakes to Remember

- `new int[n]` does not initialize values; use `new int[n]()` if zero initialization is needed
- Avoid building a full 2D matrix when most cells are unused
- Watch for out-of-bounds indices
- Repeating traversal patterns often have a useful cycle

## Complexity

- Time: `O(n)`
- Extra Space: `O(1)`

## Takeaway

> Find the repeating cycle and compute indices directly instead of simulating the whole Zigzag matrix.

# 7. Reverse Integer

## Core Idea

Extract digits from the end and rebuild the reversed number:

```cpp
int digit = x % 10;
x /= 10;

result = result * 10 + digit;
```

C++ keeps the sign during `%` and integer division:

```cpp
-123 % 10 == -3
-123 / 10 == -12
```

So no separate negative-sign handling is needed.

## Recommended Pattern

```cpp
class Solution {
public:
    int reverse(int x) {
        int result = 0;

        while (x != 0) {
            int digit = x % 10;
            x /= 10;

            if (result > INT_MAX / 10 ||
                (result == INT_MAX / 10 && digit > 7)) {
                return 0;
            }

            if (result < INT_MIN / 10 ||
                (result == INT_MIN / 10 && digit < -8)) {
                return 0;
            }

            result = result * 10 + digit;
        }

        return result;
    }
};
```

## Mistakes to Remember

- Negative digits are already handled by `%` and `/`
- Do not negate the result again for negative input
- Check overflow **before** `result * 10 + digit`
- Prefer `INT_MAX` / `INT_MIN` over `pow(2, 31)`

## Complexity

- Time: `O(log |x|)`
- Space: `O(1)`

## Takeaway

> Extract digits with `% 10`, rebuild with `* 10`, and check overflow before updating the result.

# 8. String to Integer (atoi)

## Core Idea

Parse the string in this order:

```text
leading spaces → optional sign → consecutive digits → stop
```

Build the number digit by digit:

```cpp
result = result * 10 + digit;
```

Stop immediately when the first non-digit character is reached.

## Recommended Pattern

```cpp
class Solution {
public:
    int myAtoi(string s) {
        int i = 0;
        int n = s.length();

        while (i < n && s[i] == ' ') {
            i++;
        }

        bool negative = false;

        if (i < n && (s[i] == '+' || s[i] == '-')) {
            negative = (s[i] == '-');
            i++;
        }

        int result = 0;

        while (i < n && s[i] >= '0' && s[i] <= '9') {
            int digit = s[i] - '0';

            if (!negative &&
                (result > INT_MAX / 10 ||
                 (result == INT_MAX / 10 && digit > 7))) {
                return INT_MAX;
            }

            if (negative &&
                (result > INT_MAX / 10 ||
                 (result == INT_MAX / 10 && digit >= 8))) {
                return INT_MIN;
            }

            result = result * 10 + digit;
            i++;
        }

        return negative ? -result : result;
    }
};
```

## Mistakes to Remember

- `std::string` has no built-in `.trim()`
- Invalid characters should `break`, not `continue`
- Parse the sign only before the digits
- Check overflow **before** `result * 10 + digit`
- `INT_MAX = 2147483647`, but `INT_MIN = -2147483648`
- For negative input, `2147483648` cannot be stored as a positive `int`

## Complexity

- Time: `O(n)`
- Space: `O(1)`

## Takeaway

> Parse in order, stop at the first invalid character, and check integer bounds before updating the result.

# 11. Container With Most Water

## Core Idea

Use **Two Pointers** starting from both ends.

Area:

```cpp
area = (right - left) * min(height[left], height[right]);
```

Since moving either pointer decreases the width, move the **shorter side** because only replacing the bottleneck can possibly increase the area.

```cpp
if (height[left] < height[right]) {
    left++;
} else {
    right--;
}
```

## Recommended Pattern

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int left = 0;
        int right = height.size() - 1;
        int result = 0;

        while (left < right) {
            int area = (right - left) *
                       min(height[left], height[right]);

            result = max(result, area);

            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }

        return result;
    }
};
```

## Mistakes to Remember

- Checking many `left` values for every `right` can still become `O(n²)`
- The shorter wall determines the current height
- Moving the taller wall cannot improve the current bottleneck
- Two pointers can safely eliminate impossible candidates

## Complexity

- Time: `O(n)`
- Space: `O(1)`

## Takeaway

> When width must decrease, discard the shorter side because only replacing the bottleneck can improve the area.

# 12. Integer to Roman

## Core Idea

Use **Greedy**.

Store all valid Roman numeral values from largest to smallest, including subtractive cases:

```cpp
vector<pair<int, string>> roman = {
    {1000, "M"}, {900, "CM"},
    {500, "D"},  {400, "CD"},
    {100, "C"},  {90, "XC"},
    {50, "L"},   {40, "XL"},
    {10, "X"},   {9, "IX"},
    {5, "V"},    {4, "IV"},
    {1, "I"}
};
```

Repeatedly use the largest value that fits:

```cpp
for (const auto& [value, symbol] : roman) {
    while (num >= value) {
        num -= value;
        result += symbol;
    }
}
```

## Recommended Pattern

```cpp
class Solution {
public:
    string intToRoman(int num) {
        vector<pair<int, string>> roman = {
            {1000, "M"}, {900, "CM"},
            {500, "D"},  {400, "CD"},
            {100, "C"},  {90, "XC"},
            {50, "L"},   {40, "XL"},
            {10, "X"},   {9, "IX"},
            {5, "V"},    {4, "IV"},
            {1, "I"}
        };

        string result;

        for (const auto& [value, symbol] : roman) {
            while (num >= value) {
                num -= value;
                result += symbol;
            }
        }

        return result;
    }
};
```

## Mistakes to Remember

- Use `"IV"` for strings, not `'IV'`
- Treat `IV`, `IX`, `XL`, `XC`, `CD`, `CM` as normal mappings instead of special cases
- `const auto&` avoids copying and prevents modification

## Complexity

- Time: `O(1)` for the fixed Roman numeral range
- Space: `O(1)`

## Takeaway

> Greedy: always use the largest valid Roman numeral value first.

# 15. 3Sum

## Core Idea

Sort the array, then fix one number and use **Two Pointers** to find the other two.

```cpp
target = -nums[i];
left = i + 1;
right = n - 1;
```

Move pointers based on the sum:

```cpp
if (nums[left] + nums[right] < target) {
    left++;
}
else if (nums[left] + nums[right] > target) {
    right--;
}
else {
    // found a triplet
}
```

## Recommended Pattern

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());

        vector<vector<int>> result;
        int n = nums.size();

        for (int i = 0; i < n - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            int left = i + 1;
            int right = n - 1;

            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];

                if (sum < 0) {
                    left++;
                }
                else if (sum > 0) {
                    right--;
                }
                else {
                    result.push_back({nums[i], nums[left], nums[right]});

                    int leftValue = nums[left];
                    int rightValue = nums[right];

                    while (left < right && nums[left] == leftValue) left++;
                    while (left < right && nums[right] == rightValue) right--;
                }
            }
        }

        return result;
    }
};
```

## Mistakes to Remember

- `vector` uses `.size()`, not `.length()`
- Sort with `sort(nums.begin(), nums.end())`
- After finding a valid triplet, move the pointers or the loop may never progress
- Skip duplicate `i`, `left`, and `right` values
- Start `left` from `i + 1`

## Complexity

- Time: `O(n²)`
- Space: `O(1)` excluding output

## Takeaway

> Sort + fix one number + two pointers + skip duplicates.
