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
- You think we can build better software

+++
@title[Glossary]

## Definitions
- Integration != _continuous integration_
- _Development_ = creation, modification and bugfixin'
- _Support_ = operation, configuration and bugfixin'

---
@title[Why?]

# Why does our program grow?

+++
@title[Maybe]

## Program grows because
- Program needs to do some new tricks
- Program needs to comply with other program
- Project team grows and more code is produced

+++
@title[Because]

## Why it's a problem to me?
- Program isn't quite ready to embrace some changes
- Team may have no idea where to put new changes
- There is **no time to explain**!

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
Given a string, return a new string that has transformed based on the input:
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
All lower-cased to upper

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
  'iNPUT eXAMPLE'
  """
  ...

if __name__ == '__main__':
  import doctest
  doctest.testmod()
```

+++

### WASTED

```shell
$ python test_transformer.py
**********************************************************************
File "transformer.py", line 3, in __main__.string_transformer
Failed example:
    string_transformer('Example Input')
Expected:
    'iNPUT eXAMPLE'
Got:
    'Input InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample InputExample Example'
**********************************************************************
1 items had failures:
   1 of   1 in __main__.string_transformer
***Test Failed*** 1 failures.
```

+++

# Can we do better?
Rewind <<

---

## Yes, we can

```python
def switch_case(s):
  """
  >>> switch_case('fOo bAr')
  'FoO BaR'
  """
  ...
```

+++

## Step by step

```python
def reverse_words(s):
  """
  >>> reverse_words('Hello, world!')
  'world! Hello,'
  """
  ...
```

+++

## Simple. Tested.

```python
def string_transformer(s):
  """
  >>> string_transformer('Example Input')
  'iNPUT eXAMPLE'
  """
  return reverse_words(switch_case(s))
```

---
@title[Append driven development]

### Questions in mind
- How to build a unit test to it?
- How to build an integration test to it?
- How to build a functional test to it?

+++
@title[Watch my hands now]

> We want to give access to the app to the registered users only

```python
def login(username, password):
  blackbox = BlackBox(os.environ['BLACKBOX_URL'])
  is_valid, user = blackbox.check(username, password)
  if not is_valid:
    raise Unauthorized()
  return user
```

+++
@title[Keep watching]

> We need to allow access to mobile version to paid users only

```python
def login(username, password):
  blackbox = BlackBox(os.environ['BLACKBOX_URL'])
  is_valid, user = blackbox.check(username, password)
  if not is_valid:
    raise Unauthorized()
  if 'mobile' in request.user_agent.string.lower():
    is_paid = PaymentService.check_mobile_subscription(username)
    if not is_paid:
      raise Conflict()
  return user
```

+++
@title[Have you noticed the trick?]

> We have to prevent any fraud attempts as early as possible

```python
def login(username, password):
  blackbox = BlackBox(os.environ['BLACKBOX_URL'])
  is_valid, user = blackbox.check(username, password)
  if not is_valid:
    raise Unauthorized()
  if 'mobile' in request.user_agent.string.lower():
    is_paid = PaymentService.check_subscription(username)
    if not is_paid:
      raise Conflict()
  elif user.fraud_factor >= 0.5:
    is_verified = CaptchaService.check(username)
    if not is_verified:
      raise UnprocessableEntity()
  return user
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
  if not is_valid:
    raise Unauthorized()
  return user
```

+++
@title[Break it into pieces]

```python
def block_unpaid_mobile(username):
  is_paid = PaymentService.check_subscription(username)
  if not is_paid:
    raise Conflict()
```

+++
@title[Small enough pieces]

```python
def redirect_potential_fraud_to_captcha(user, threshold=0.5):
  if user.fraud_factor < threshold:
    return

  is_verified = CaptchaService.check(user.username)
  if not is_verified:
    return redirect(CaptchaService.redirect_url())
```

+++
@title[Combine them]

```python
def process_auth(request):
  user = login(request.form.username, request.form.password)

  if 'mobile' not in request.user_agent.string.lower():
    block_unpaid_mobile(user.username)

  captcha_url = redirect_potential_fraud_to_captcha(user)
  if captcha_url is not None:
    return captcha_url

  return 'Welcome back', 200
```

---
@title[Second way]

## Composition
- New feature as a new program with tests
- Functional tests are still required
- More indirection is introduced

---
@title[References]

## Further reading & watching
- [Integrated Tests Are A Scam](http://blog.thecodewhisperer.com/permalink/integrated-tests-are-a-scam)
- [One more level of indirection](http://wiki.c2.com/?OneMoreLevelOfIndirection)
- Kent Beck, Test Driven Development: By Example

---
@title[Thank you]

## Thank you

Spread the word
`gitpitch.com/inesusvet/complexity-talk`

ivan.styazhkin@datarobot.com

fb.com/ivan.nesusvet
