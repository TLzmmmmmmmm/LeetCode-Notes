# 17. Letter Combinations of a Phone Number

## Core Idea

Use **Backtracking (DFS)** to generate all possible letter combinations.

For each digit, choose one letter, recursively process the next digit, and save the combination when all digits have been processed.

```python
def backtrack(index, path):
    if index == len(available):
        output.append(path)
        return

    for letter in available[index]:
        backtrack(index + 1, path + letter)
```

- `index`: current digit position
- `path`: letters selected so far
- **Base case:** `index == len(available)` means a complete combination has been formed
- `path + letter` creates a new string without modifying the original path

## Recommended Pattern

```python
class Solution:
    def letterCombinations(self, digits: str) -> list[str]:
        if not digits:
            return []

        letters = [
            "abc", "def", "ghi", "jkl",
            "mno", "pqrs", "tuv", "wxyz"
        ]

        available = [
            letters[int(number) - 2]
            for number in digits
        ]

        output = []

        def backtrack(index, path):
            if index == len(available):
                output.append(path)
                return

            for letter in available[index]:
                backtrack(index + 1, path + letter)

        backtrack(0, "")
        return output
```

## Mistakes to Remember

- Python uses `len(array)`, not `array.length()`
- `digits.split()` splits by whitespace, not individual characters; directly iterate over the string
- `"abc".split()` returns `["abc"]`, not `["a", "b", "c"]`
- Use `int(number)`, not `(int)number`
- The base case is `index == len(available)`, not `>`
- Use `path + letter` instead of `path += letter` to avoid accumulating letters across sibling branches
- Nested functions can access enclosing variables (`available`, `output`) through Python's **LEGB scope rule**
- `output.append(path)` modifies the existing list and does not require `nonlocal`
- In the iterative approach, initialize `output = [""]`, not `[]`, to provide an initial empty combination

## Complexity

Let `n` be the number of digits. Each digit has at most 4 possible letters.

- Time: `O(n * 4^n)` — generate up to `4^n` combinations, each of length `n`
- Space: `O(n * 4^n)` including the output

## Takeaway

> Backtracking = Choose → Recurse → Return → Try the next choice.
>
> Use DFS to explore all combinations, stop when the path is complete, and keep recursive branches independent.
