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
