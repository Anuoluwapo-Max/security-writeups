# Eligibility Checker Script

## Context
A Python script I wrote to practice core fundamentals: input handling, 
conditionals, loops, and input validation. Revisited it today to 
explain every line back to front — a good gut-check for whether I 
actually understood it, or just got it working by trial and error.

## What It Does
Takes a user's name and age, checks eligibility for cybersecurity 
training based on age, runs a countdown, then asks a yes/no question 
that keeps repeating until it gets a valid answer.

## The Code

```python
name = input("Enter your name: ")
age = int(input("Enter your age: ")) #Age of User

print(f"\nHello {name}! You are {age} years old.")

if age >= 18:
    print("Status: Eligible for Cyber Security Training ✅")
else:
    print("Status: Keep learning! You'll get there 💪")

print("\nCountdown to Day 2:")
for i in range(5, 0, -1):
    print(i)
print("Let's go!")

answer = input("Would You Like To Proceed (yes/no): ")
answer = answer.lower()

while answer != "yes" and answer != "no":
    print("Please type yes or no")
    answer = input("Would You Like To Proceed (yes/no): ")
    answer = answer.lower()

if answer == "yes":
    print("Great, let's continue!")
else:
    print("No worries, come back anytime.")
```

## Key Concepts

**`int(input(...))`** — `input()` always returns text, even if the 
user types a number. Wrapping it in `int()` converts it so it can be 
used in numeric comparisons like `age >= 18`.

**f-strings** — `f"Hello {name}!"` inserts variable values directly 
into a string, cleaner than manually concatenating with `+`.

**`.lower()` before comparing input** — normalizes user input so 
"YES", "Yes", and "yes" are all treated the same. Small habit, 
prevents a surprisingly common bug.

**Validation loop vs decision logic — two separate jobs:**
- The `while` loop's only job is to keep asking until the answer is 
  valid (`"yes"` or `"no"`, nothing else)
- The `if/else` that follows only runs once the input is already 
  guaranteed clean — it decides what happens *next*, not whether the 
  input is valid

Splitting these into two separate blocks keeps the logic clean — the 
loop doesn't need to know what happens after, and the if/else doesn't 
need to worry about garbage input reaching it.

## Result
Script runs correctly, handles both age branches and invalid input 
gracefully.
