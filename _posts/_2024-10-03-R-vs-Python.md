---
layout: post
title: R vs. Python: Finding Prime Numbers
image: "/posts/prime_poster.jpg"
tags: [Python, R, Primes, Programming]
---

## Summary

*Write a function that returns prime numbers, 2 through n...* is a level two programming problem. Beyond simple looping and conditional functions, it requires some understanding of control flow, basic algorithms, and optimization. 

Although I'm not the first to demonstrate this problem (you can google it), the demo here serves as a benchmark in my beginning programming journey. 

Honestly, this problem gave me a headache for weeks, and when I did figure it out, I learned that my "simple" method was inefficient in terms of computing time. 
The optimal method - the Sieve of Eratosthenes - was introduced over 2000 years ago. In this demo, we'll take a look at both. 

Finally, as a long-time R user who is now getting used to Python, I thought it would be fun to demo this problem in both languages to compare code and processing speed. 
---

### Start simple 

A prime number is any number divisible (wholly) only by itself and one. The number one does not count; a prime must have two unique divisors. A list of prime numbers between 2 and 20 includes: [2, 3, 5, 7, 11, 13, 17, 19]
Each number in the returned set is proven to be a prime by dividing every number, *i*, in that set by every number lower than it(*i*) and returning a whole number. 
Actually, not by *every* number lower than it: We already know that all numbers are divisible by 1, and we only need to check divisors up to the square root of the n, because any larger divisor would already have a corresponding lower divisor. 
So, we can test if a number *i* is a prime by dividing it by every whole number in the range 2:sqrt(*i*). Great, and we have to do that for every number in the range 2 through n.  
Then, we just have to return a list of each prime in the set. Sounds simple?! 

This means our function has to: 
1. loop through each i in 2 through n, 
2. divide each i by 2 through the square root of i, 
3. until either a) a whole number is found (not a prime), or b) a whole number is not found (prime)
4. and populate a new list when condition 3b is met using the i for which that condition was met. 

Ok, lets go... 

```R
# First, a function that will test if a number is a prime by dividing it from the set [i in 2:sqrt(i)]

is_prime <- function(n) {
	if (n==2) return(TRUE)
	for (i in 2:sqrt(n)) {
		if (n %% i == 0) return(FALSE) # In R, %% returns a remainder after dividing. So basically were asking it rule out non-whole numbers after dividing each i through the sequence 2 through sqrt(n) 
	}
	return(TRUE)
}

r_primes<-function(n) {
	primes<-NULL
	for (i in 2:n) {
		if(is_prime(i)) {
			primes<-c(primes,i)
		}
	}
	print(paste("There are", length(primes), "prime numbers between 2 and", n))
	
	if (length(primes) < 170) {print(primes)}
	
}

r_primes(1000)

```
In Python, the same code structure looks like: 

```Python
def is_prime(n): 
    
    if n < 2: 
        return False # This ensures that one and zero return False
    
    if n == 2: 
        return True 
    
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0: 
            return False 
        
    return True # i.e., if there are no divisors other than 1 and n. 

def py_primes(n):
    
    p_set = set()
    
    for i in range(2, int(n + 1)):
        if is_prime(i) == True:
            p_set.add(i)
    
    print(f"There are {len(p_set)} prime numbers between 2 and {n}.")
    
    if len(p_set) < 170:
        print(f"Primes:", sorted(p_set)) # This is the only way to I know to print the primes in a legible way

py_primes(1000)

```
### Sieve Method

The code above works, it appears efficient, it is legible in both R and Python, and as a beginner programmer I'm happy I solved the problem. 
In hindsight, however, the code is not optimal. We're asking it to loop through every number in our original set (2 through n) and divide each by another set of numbers (2:sqrt(n)). That's easy for small sets like 1-20, but when n gets large, it takes considerable computing time. 
A human looking at the set 1-20 does not need to go through and test every number. We know, for example, that even numbers are not prime numbers because they are divisible by 2 (in addition to 1 and n itself) by definition, so we don't bother to test them. We also know that multiples of 5 (except five itself) are not primes either, because we learn to count by fives as kids, and so we can eliminate those from our task as well. 
In other words, instead of dividing, we simplify the problem by eliminating multiples. It's easy for a human to return primes 1-20 because we minimize the number of tests that we need to make. We can turn the problem requiring about 60 calculations into one that requires about 8. In fact, for any given set 1:n, we don't have to loop n times to figure out which numbers are primes. We can use the fact that our divisor becomes redunant by the time we get to the square root of n. We only need to eliminate multiples up to the square root of n! 
For example in the set 1-100, we can eliminate all even numbers, all multiples of 3s, 5s, 7s, and... thats its, we're done! 4, 6, 8, 9, and 10 have already been eliminated. 

As a beginning programmer, it was a cool realization that, while computers are super fast, they are only as efficient as they are programmed to be. 

In R, the Sieve of Eratosthenes looks like:

```R

sieve_primes <- function(n) {
	primes <- rep(TRUE, n) # A boolean vector in which the index of the true values will represent the prime numbers!
	primes[1] <- FALSE  # 1 (which is also primes index 1) is not prime. 
	
	for (i in 2:sqrt(n)) { # We only have to loop through up to the square root
		if (primes[i] == TRUE) {  # If i is prime
			primes[seq(i^2, n, i)] <- FALSE  # start eliminating multiples with i^2 because smaller multiples of i have already been eliminated by smaller primes. 
		}
	}
	
	print(paste("There are", sum(primes), "prime numbers between 2 and", n))
	
	if (sum(primes) < 170) {print(which(primes))}
}

sieve_primes(1000)

```

In Python: 

```python

def sieve_of_eratosthenes(n):
    if n <= 1: 
        return [] 
    primes_list = [True] * (n+1)
    primes_list[0] = primes_list[1] = False
    
    for i in range(2, int(n**0.5) + 1): 
        if primes_list[i]: 
            primes_list[i**2:n+1:i] = [False] * len(primes_list[i**2:n+1:i])
    
    prime_numbers = [i for i in range(n+1) if primes_list[i]]
    
    print(f"There are {len(prime_numbers)} prime numbers between 2 and {n}.")
    
    if len(prime_numbers) < 170:
        print(f"Primes:", sorted(prime_numbers))
    
sieve_of_eratosthenes(1000)

```

### Time test
But how much better is one method than the other, and which language is better for the task? When finding Primes within 1000, both methods in both languages are performed in the blink of an eye.
But when n gets large, we start to see real differences. We can use the timer functions in each langauge to compare. 

#### Simple R Method
```r
system.time(r_primes(1000000))
```

#### Simple Python Method
```python
t = timeit.Timer(lambda: py_primes(1000000))
execution_time = t.timeit(number = 1)
print(f"Execution time: {execution_time} seconds")
```

#### Sieve in R
```r
system.time(sieve_primes(1000000))
```

#### Sieve in Python
``` python
t = timeit.Timer(lambda: sieve_of_eratosthenes(1000000))
execution_time = t.timeit(number = 1)
print(f"Execution time: {execution_time} seconds")
````

### Conclusion
While Python performs faster than R in both cases, the differences are negligible for practical purposes, especially from this beginner programmer's perspective. 
The real takeaways are that optimal code, especially for complex tasks, is always the goal, and that while computers are fast can only think as efficiently as the code we write. 





