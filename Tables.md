
# Part 1
Microsoft Outlook

outlook.live.com

Things being tested:
- Inbox Sorting
- Email Filtering (Rules)
- Email Flagging


# Part 2
Sort items so only unread populate in the inbox

| Partition  | Value | Expected Result|
| --- | --- | --- |
| Valid - matches filter | Unread | Shown in inbox |
| Invalid - excluded by filter | Read | Hidden from view |


## Tests
*assuming there is a function called "emailStatus" which accepts an email's unread/read status and the inbox filter is set to "unread"

```
test("T01: Email is marked as unread", () => {
    expect(emailStatus("unread"))
    .toBe("Shown in inbox");
});
test("T02: Email is marked as read", () => {
    expect(emailStatus("read"))
      .toBe("Hidden");
  });

```


# Part 3
Rule 1: If an email is from noreply@updates.com but has "error" in the subject, move to "Errors" folder

Rule 2: If an email is from noreply@updates.com without "error" in the subject, mark as read and move to the "no reply" folder

Otherwise, no action


| | | | | |
| --- | --- | --- | --- | --- |
| Sender is noreply@updates.com | F | T | T | F |
| Subject contains "Error" | F | F | T | T |
| mark as read | | X | | |
| move to no reply | | X | | |
| move to Errors | | | X | |
| no action | X | | | X |

## Tests
*assuming there is a function called "emailRule" with parameters `sender` and `subject includes`


```
test("T01: matching sender and subject", () => {
    expect(emailRule("noreply@updates.com", "Error"))
    .toBe("Errors");
});
test("T02: matching sender, incorrect subject", () => {
  expect(emailRule("noreply@updates.com", "Hello"))
    .toBe("No Reply and marked as read");
});
test("T03: similar sender, matching subject", () => {
    expect(emailRule("noreply@updates.org", "Error"))
      .toBe("No Action");
});
test("T04: incorrect sender and subject", () => {
  expect(emailRule("hello@email.com", "Welcome"))
    .toBe("No Action");
});
```

Since all senders that don't match do not have any action taken to them, could just have 3 tests
# Part 4

| Current State | Event | Expected Next State | Valid? |
| --- | --- | --- | --- |
| Not Flagged | Flag email | Flagged | Valid |
| Flagged | Mark complete | Completed | Valid |
| Flagged | Clear flag | Not Flagged | Valid |
| Completed | Clear flag | Not Flagged | Valid |
| Not Flagged | Mark complete | Completed | Valid |
| Not Flagged | Clear flag | Not Flagged | Invalid, no flag to clear |
| Completed | Mark complete | Completed | Invalid, already completed |
| Flagged | Flag email | Flagged | Invalid, already flagged |
| Completed | Flag email | Flagged | Valid |

## Tests
*assuming there is a function called "flagStatus" with parameters `currentState` and `event`

```
  test("T01: Flag an unflagged email", () => {
    expect(flagStatus("Not Flagged", "Flag"))
      .toBe("Flagged");
  });

  test("T02: Mark a flagged email as complete", () => {
    expect(flagStatus("Flagged", "Mark Complete"))
      .toBe("Completed");
  });

  test("T03: Clear the flag on a flagged email", () => {
    expect(flagStatus("Flagged", "Clear Flag"))
      .toBe("Not Flagged");
  });

  test("T04: Clear the flag on a completed email", () => {
    expect(flagStatus("Completed", "Clear Flag"))
      .toBe("Not Flagged");
  });

  test("T05: Mark an unflagged email as complete", () => {
    expect(flagStatus("Not Flagged", "Mark Complete"))
      .toBe("Completed");
  });

  test("T06: Clear flag on an unflagged email", () => {
    expect(flagStatus("Not Flagged", "Clear Flag"))
      .toBe("Not Flagged");
  });

  test("T07: Mark a completed email as complete again", () => {
    expect(flagStatus("Completed", "Mark Complete"))
      .toBe("Completed");
  });

  test("T08: Flag an already flagged email", () => {
    expect(flagStatus("Flagged", "Flag"))
      .toBe("Flagged");
  });

  test("T09: Flag a completed email", () => {
    expect(flagStatus("Completed", "Flag"))
      .toBe("Flagged");
  });
```