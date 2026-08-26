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
