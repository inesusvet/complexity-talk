@title[Introduction]

## Integration vs Composition

#### by Ivan Styazhkin @ DataRobot

+++
@title[Disclaimer]

# Your milage may vary
Let's discuss it after the talk

+++
@title[Epigraph]

## Software development
> Is it hard?'

> Not if you have the right attitudes.
Its having the right attitudes thats hard.

+++
@title[Expectations]

## Target audience
- You build software on daily basis
- You have experience of supporting software of others
- You think we can do better software

+++
@title[Glossary]

## Definitions
- _Development_ = creation, modification and bugfixin'
- _Support_ = operation, configuration management & bugfixin'

---
@title[Why?]

# Why does our program grow?

+++
@title[Maybe]

## Program grows because
- Program needs to do some new tricks
- Program needs to be compatible to some other program
- Project team grows and more code is produced sooner

+++
@title[Because]

## Why it's a problem to me?
- Program isn't quite ready to embrace some changes
- Team may have no idea/compliance where to put new changes
- There is no time to explain!

---
@title[What is it]

# Software architecture

+++
@title[Uncle Bob says]

# Software architecture
> Practices and methods to enable changes in program's behavior

---
@title[Simple]

## A simple [case](https://www.codewars.com/kata/string-transformer)
> Given a string, return a new string that has transformed based on the input:
1. Change case of every character, ie. lower case to upper case, upper case to lower case.
2. Reverse the order of words from the input.

+++
Start it very simple

```python
def string_transformer(s):
  new_string = ''
  return new_string
```

+++
All lower cased to upper

```python
def string_transformer(s):
  new_string = ''
  for l in s:
    if s.islower():
      new_string += s.upper()
  return new_string
```

+++
And upper case to lower

```python
def string_transformer(s):
  new_string = ''
  for l in s:
    if s.islower():
      new_string += s.upper()
    elif s.isupper():
      new_string += s.lower()
  return new_string
```

+++
And process non-letters as is

```python
def string_transformer(s):
  new_string = ''
  for l in s:
    if s.islower():
      new_string += s.upper()
    elif s.isupper():
      new_string += s.lower()
    else:
      new_string += s
  return new_string
```

+++
Then split the string into words sequence

```python
def string_transformer(s):
  new_string = ''
  for l in s:
    if s.islower():
      new_string += s.upper()
    elif s.isupper():
      new_string += s.lower()
    else:
      new_string += s
  words = new_string.split()
  return new_string
```

+++
And reverse the words sequence and concatenate

```python
def string_transformer(s):
  new_string = ''
  for l in s:
    if s.islower():
      new_string += s.upper()
    elif s.isupper():
      new_string += s.lower()
    else:
      new_string += s
  words = new_string.split()
  words = words[::-1]
  new_string = ' '.join(words)
  return new_string
```

+++
Let's test it now!

```python
def string_transformer(s):
  """
  >>> string_transformer('Example Input')
  'iNDEX eXAMPLE'
  """
  ...

if __name__ == '__main__':
  import doctest
  doctest.testmod()
```

+++

```shell
$ python test_transformer.py
**********************************************************************
File "transformer.py", line 3, in __main__.string_transformer
Failed example:
    string_transformer('Example Input')
Expected:
    'iNDEX eXAMPLE'
Got:
    'Input InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample Example'
**********************************************************************
1 items had failures:
   1 of   1 in __main__.string_transformer
***Test Failed*** 1 failures.
```

+++

# WASTED

+++

# Can we do better?
Rewind <<

---
@title[Append driven development]

### Questions in mind
- How to build a unit test to it?
- How to build a functional test to it?
- How to build an integration test to it?

+++
@title[Watch my hands now]

```python
def login(username, password):
  blackbox = BlackBox(os.environ['BLACKBOX_URL'])
  is_valid, user = blackbox.check(username, password)
  return is_valid
```

+++
@title[Keep watching]

```python
def login(username, password):
  blackbox = BlackBox(os.environ['BLACKBOX_URL'])
  is_valid, user = blackbox.check(username, password)
  if user.subscription_type == 'mobile':
    is_paid = blackbox.check_mobile_subscription(username)
    return is_valid and is_paid
  return is_valid
```

+++
@title[Have you noticed the trick?]

```python
def login(username, password):
  blackbox = BlackBox(os.environ['BLACKBOX_URL'])
  is_valid, user = blackbox.check(username, password)
  if user.subscription_type == 'mobile':
    is_paid = .check_subscription(username)
    return is_valid and is_paid
  elif user.fraud_factor >= 0.5:
    is_verified = captcha.check(username)
    return is_valid and is_verified
  return is_valid
```

+++
@title[Append driven development]

## Append driven development
> When you just keep adding new statements to the end of your function

(c) Max Schepelin, 2017

---
@title[First summary]

## Integration
- New feature by _yet another if statement_
- Heavy integration tests required
- Increasing cost of development & support

---
@title[Single responsibility]

```python
def login(username, password):
  blackbox = BlackBox(os.environ['BLACKBOX_URL'])
  is_valid, user = blackbox.check(username, password)
  return is_valid
```

+++
@title[Break it into pieces]

TBD

---
@title[Second way]

## Composition
- New feature as a new program with tests
- Functional tests _and_ unit tests are required

---
@title[References]

## Further reading & watching
- [Integrated Tests Are A Scam](http://blog.thecodewhisperer.com/permalink/integrated-tests-are-a-scam)

---
@title[Thank you]

## Thank you

Spread the word
`gitpitch.com/inesusvet/complexity-talk`

ivan.styazhkin@datarobot.com

fb.com/ivan.nesusvet
