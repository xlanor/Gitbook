# Minimum Number of flips to make a string monoincreasing

I received this question as part of a OA. The thing about OAs however, is that they are usually disguised as a custom problem. However, more often that not, it is usually a variation of a similar question on leetcode.

I was lucky in this aspect because I'd just did this question recently on leetcode and quickly spotted the question. However, what really interested me was the intuitive approach that was posted in the Solutions section, which (I admit, I braindead memorised it the night before for this interview as it was fairly simple). It was also one of the top 100 questions that I'd practiced before.

The question (copy pasted from LC here), is as follows:

> A binary string is monotone increasing if it consists of some number of `0`'s (possibly none), followed by some number of `1`'s (also possibly none).
>
> You are given a binary string `s`. You can flip `s[i]` changing it from `0` to `1` or from `1` to `0`.
>
> Return _the minimum number of flips to make_ `s` _monotone increasing_.

This was my final solution in the OA:

```python
def minimumByteFlips(str): 
    res = len(str) 
    right = str.count('0') 
    left = 0 
    for i in range(len(str)): 
        if str[i] == '0': 
            right -= 1 
        else: 
            left += 1
        res = min(res, left+right if i != 0 else 0 + right) 
    return res
test = "00101" # Flips to 00001
test2 = "1111000" # Flips to 111111
test3 = "111111" # No flip 
test4 = "000000" # No flip 
print(minimumByteFlips(test)) 
print(minimumByteFlips(test2)) 
print(minimumByteFlips(test3))
print(minimumByteFlips(test4))
```

I found this solution interesting and did not understand it at first. However, after understanding the gist of the solution, this is my attempt to explain it.

First, we add some print statements here to allow us to better see what's going on inside the solution.

```python
def minimumByteFlips(str): 
    res = len(str) 
    right = str.count('0') 
    left = 0 
    for i in range(len(str)): 
        str2 = str[:i] + '|' + str[i:]
        print(f"Zeros:{right} One {left} Res {res} str {str2}")
        if str[i] == '0': 
            right -= 1 
        else: 
            left += 1
        if i == 0:
          res = min(res, 0+right)
          print(f"Res {res} compare {0+right}")
        else:
          res = min(res, left+right)
          print(f"Res {res} compare {left+right}")
        print(f"Minimum Swaps: {res}")
    return res
test = "00101" # Flips to 00001
test2 = "11111000" # Flips to 111111
test3 = "111111" # No flip 
test4 = "000000" # No flip 
test5 = "100000" # Flips to 000000
test6 = "011111" # Already monotonic
print(minimumByteFlips(test)) 
print(minimumByteFlips(test2)) 
print(minimumByteFlips(test3))
print(minimumByteFlips(test4))
print(minimumByteFlips(test5))
print(minimumByteFlips(test6))
```

Let's look at the output for the first test case.

```
Zeros:3 One 0 Res 5 str |00101
Res 2 compare 2
Minimum Swaps: 2
Zeros:2 One 0 Res 2 str 0|0101
Res 1 compare 1
Minimum Swaps: 1
Zeros:1 One 0 Res 1 str 00|101
Res 1 compare 2
Minimum Swaps: 1
Zeros:1 One 1 Res 1 str 001|01
Res 1 compare 1
Minimum Swaps: 1
Zeros:0 One 1 Res 1 str 0010|1
Res 1 compare 2
Minimum Swaps: 1

1
```

This should make it incredibly clear as to what we're attempting to do here - I don't think this is a standardised algo per se, but leans more towards operating on intuition

```
00101
```

First, we count the number of `0`s that we can see in the string. This is an `O(n)` operation.

Now, we can proceed swith the actual algorithm. What we are doing here is a little different: I'd describe it as a sliding window variation

```
 |00101
```

We partition the string like this, because we are starting "off" the edge of the string in this case.

```
At i = 0,right = 3 left = 0 res = 5
```

We see a 0.&#x20;

This means that we can now move 0 to the "seen" portion of the string that is seperated by the index.

At the start, we first calculated the number of 0s present in our string. This count is recorded in `right`.

Given that we've seen a `0` here, we decrement the number of zeros that we've have on the right side of the index.&#x20;

```
At i = 0,right = 2 left = 0 res = 5
```

```
min(res, left+right if i != 0 else 0 + right) 
```

At this point, we know that **res** has been initalized to the case whereby we have to flip the entire array. (This is not strictly necessary, you can put it as float.inf).

Given that right here is keeping track of the number of zeros that we have yet to meet, and left is keeping track of the number of ones that we've met, by taking these two values and adding them together and then getting the minimum of these values, we are able to tell immediately how many flips we have to perform on the **right** side of the index.

By using the minimal value here, we ensure that we can always keep track of the minimum















