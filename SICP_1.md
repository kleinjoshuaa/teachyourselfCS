# Exercises from SICP Chapter 1

## 1.1
  - 10
  - 11
  - 8
  - 3
  - 6
  - ;Value: a
  - ;Value: b
  - 19
  - #f
  - 4
  - 16
  - ~~9~~ (incorrect) actually 6 (got this incorrect from misreading)
  - 16
  
## 1.2

````scheme
(/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5))))) (* 3 (- 6 2) (- 2 7)))
````

## 1.3
````scheme
; define a procedure to find the sum of the squares of the 2 largest of 3 numerical input parameters
(define (square x) (* x x))
(define (>= x y)
  (not (< x y)))

(define (biggest x y z)
  (cond ((and (>= x y) (>= x z)) x)
        ((and (>= y x) (>= y z)) y)
        ( z)))


(define (middle x y z)
  (cond ((and (>= x y) (<= x z)) x)
        ((and (>= y x) (<= y z)) y)
        ((and (>= x z) (<= x y)) x)
        ((and (>= y z) (<= y x)) y)
        (z)))

(define (ss2l x y z) (+ (square (biggest x y z)) (square (middle x y z))))
````

## 1.4
````scheme
(define (a-plus-abs-b a b)
  ((if (> b 0) + -) a b))
````
If b is greater than zero, the above procedure would add a and b. If b was less than zero, it would subtract b from a.

## 1.5


Question: Ben Bitdiddle has invented a test to determine whether the interpreter he is faced with is using applicative-order evaluation or normal-order evaluation. He defines the following two procedures:
````scheme
(define (p) (p))

(define (test x y)
  (if (= x 0)
      0
      y))
````

Then he evaluates the expression
````scheme
(test 0 (p))
````
1. normal order evaluation would evaluate with these steps
````scheme
(test 0 (p))
````
Here we first do one level of replacement of the variables x and y with 0 and (p)
````scheme
  (if (= 0 0)
      0
      (p))
````
0 = 0, so in this case we return zero without evaluating (p). In normal order we evaluate first and then substitute when needed.
````scheme
  0
````
2. applicable order evaluation would evaluate with these steps
````scheme
(test 0 (p))
````
Here we first do one level of replacement of the variables x and y with 0 and (p)
````scheme
  (if (= 0 0)
      0
      (p))
````
Then we try to replace (p) with (p) with (p) but it keeps going in an infinite loop because we cannot fully replace all variables.


## 1.6
The predicate expression is evaluated first, and the result determines whether to evaluate the consequent or the alternative expression during the evaluation of a normal `if` statement. Because lisp uses applicative order evaluation it will try to evaluate all of the arguments to the `cond` function, which, since it is calling itself recursively, will keep the function in an infinite loop.

## 1.7
> Question: The `good-enough?` test used in computing square roots will not be very effective for finding the square roots of very small numbers. Also, in real computers, arithmetic operations are almost always performed with limited precision. This makes our test inadequate for very large numbers. Explain these statements, with examples showing how the test fails for small and large numbers

Our good-enough function is as follows:
````scheme
(define (good-enough? guess x)
  (< (abs (- (square guess) x)) 0.001))
````
For very small numbers this test will perform poorly because any number less than zero, when squared, will actually be smaller than itself. 
For example:
````scheme
(good-enough? 0.0033 .0001)
````
returns true and as numbers get smaller and smaller the distance between them using this `good-enough?` function becomes kind of nonsensical. 

For very large numbers, we can see this example here:
````scheme
(good-enough? 1000000000 1000000000)
````
Returns false despite the fact that the numbers are identical. The good enough function we are using doesn't really handle how much bigger numbers become when squared. Of course, we could talk about bit representations of numbers in binary but the book hasn't gotten to that point yet.

>  An alternative strategy for implementing good-enough? is to watch how guess changes from one iteration to the next and to stop when the change is a very small fraction of the guess. Design a square-root procedure that uses this kind of end test.

Here we can use the below procedures instead
````scheme
(define (average x y)
  (/ (+ x y) 2))

(define (good-enough? guess lastguess)
  (< (abs (/ (- lastguess guess) guess)) 0.001))

(define (improve guess x)
  (average guess (/ x guess)))

(define (sqrt-iter guess lastguess x)
  (if (good-enough? guess lastguess)
      guess
      (sqrt-iter (improve guess x)
                 guess
                 x)))

(define (sqrt x)
  (sqrt-iter 1.0 0 x))
````
We can alter the `good-enough?` function to make the difference as small as we want to improve accuracy, barring the limits of  numerical representation in mit-scheme.
In any case, removing the squaring function helps, for example, now
````scheme
(good-enough? 1000000000 1000000000)
````
returns true

## 1.8
> Implement Newton's Cube Root
````scheme
(define (square x)
  (* x x))

(define (good-enough? guess lastguess)
  (< (abs (/ (- lastguess guess) guess)) 0.000001))

(define (improve guess x)
  (/ (+ (/ x (square guess)) (* 2 guess)) 3))

(define (cbrt-iter guess x)
  (if (good-enough? guess (improve guess x))
      guess
      (cbrt-iter (improve guess x)
                      x)))

(define (cbrt x)
  (cbrt-iter 1.0 x))
````

## 1.9
> evaluate `(+ 4 5)` using the substitution model 
````scheme
(define (+ a b)
  (if (= a 0)
      b
      (inc (+ (dec a) b))))
````
Expansion/resolution of `(+4 5)`
````scheme
(+ 4 5)
(inc (+ 3 5))
(inc (inc (+ 2 5)))
(inc (inc (inc (+ 1 5))))
(inc (inc (inc (inc (+ 0 5)))))
````
Resolution
````scheme
(inc (inc (inc (inc (+ 0 5)))))
(inc (inc (inc (inc 5))))
(inc (inc (inc 6)))
(inc (inc 7))
(inc 8)
9
````
This is recursive.

Now let's evaluate `(+ 4 5)`
````scheme
(define (+ a b)
  (if (= a 0)
      b
      (+ (dec a) (inc b))))
````
````scheme
(+ 4 5)
(+ 3 6)
(+ 2 7)
(+ 1 8)
(+ 0 9)
9
````
This is iterative.


## 1.10
````scheme
(A 1 10)
; Result: 1024
(A 2 4)
; Result: 65536
(A 3 3)
; Result: 65536
````

- f = 2n
- g = 2^n
- h = 2^^n (in other words, 2^2^...^2 n times) I found this particularly frustrating because to me this doesn't seem like a concise mathematical definition that was asked for. 

## 1.11
````scheme
; recursive method
(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        ((= n 2) 2)
        (else (+ (fib (- n 1))
                 (* 2 (fib (- n 2)))
                 (* 3 (fib (- n 3)))))))
                 
; iterative
(define (fib n)
  (fib-iter 2 1 0 n))

(define (fib-iter a b c count)
  (if (= count 0)
      c
      (fib-iter (+ a (* 2 b) (* 3 c)) a b (- count 1))))
````

## 1.12
````scheme
; recursive pascal's triangle
(define (pascal row element)
  (cond ((= row 1) 1)
        ((= row 2) 1)
        ((= element 1) 1)
        ((= element row) 1)
        (else (+ (pascal (- row 1) element)
                 (pascal (- row 1) (- element 1))))))
````

## 1.13

>Prove that Fib(n) is the closest integer to (&phi;^n )/&#8730;5, where &phi; = (1 + &#8730;5)/2
Hint: Let 	&psi; = (1 - &#8730;5)/2
Use induction and the definition of the Fibonacci numbers to prove that Fib(n) = (&phi;^n - &psi;^n)/&#8730;5.

- I solved this with some help on induction proofs. I also used Latex for this 
- [Solution](113.tex)

## 1.14
Tree diagram is [here](114.svg)
> What are the orders of growth of the space and number of steps used by this process as the amount to be changed increases?
As far as 'height' (number of steps / space) this will always be the number of cents, as the max height tree will be the one that subtracts individual pennies from the amount. 

As far as time complexity -  each new cent has to be evaluated against all 5 demonimations. Roughly this will mean a O(n^5) complexity 

## 1.15
 a. 6 (essentially this is 12.15/(3^(x-1)) < 0.1  )
 b.The complexity grows at 1/x, or logorithmic time. The number of steps increments at each whole complete power of 3 contained in the argument to the function

## 1.16
````scheme
(define (even? n)
  (= (remainder n 2) 0))

(define (fast-expt a b n)
  (cond ((= n 1) a)
        ((even? n) (fast-expt (* a (* b b)) b (/ n 2)))
        (else (fast-expt (* a b) b (- n 1)))))

(define (expt a n)
  (fast-expt 1 a n)) 
````
## 1.17
````scheme
(define (even? n)
  (= (remainder n 2) 0))

(define (double a) (+ a a))
(define (halve a) (/ a 2)) 
  
  (define (rmul a b)
  (cond ((= b 0) 0)
        ((= b 1) a)
        (( even? b) (rmul (double a) (halve b)))
        (else (+ a (rmul a (- b 1))))))
````

## 1.18
Basically 1.17, but iterative
````scheme
(define (rpm a b sum)
  (cond ((= 0 a)  sum)
        ((even? a) (rpm (halve a) (double b) sum))
        (else (rpm (halve (- a 1)) (double b) (+ sum b)))))

(define (mul a b) (rpm a b 0))
````

## 1.19
````scheme
(define (fib n)
  (fib-iter 1 0 0 1 n))
(define (fib-iter a b p q count)
  (cond ((= count 0) b)
        ((even? count)
         (fib-iter a
                   b
                   (+ (* p p) (* q q))      ; compute p'
                   (+ (* 2 q p) (* q q))      ; compute q'
                   (/ count 2)))
        (else (fib-iter (+ (* b q) (* a q) (* a p))
                        (+ (* b p) (* a q))
                        p
                        q
                        (- count 1)))))
````
## 1.20
Using the substitution method (for normal order), illustrate the process generated in evaluating (gcd 206 40) and indicate the remainder operations that are actually performed. 

How many remainder operations are actually performed in the normal-order evaluation of (gcd 206 40)? 
```scheme
(define (gcd a b)
  (if (= b 0)
      a
      (gcd b (remainder a b))))

(gcd 206 40)
;40 ==0? no
(gcd 40 (remainder 206 40))
;if(b == 0?) -> b is then evaluated to detemrine if we need to do an additional step
; so in this case

(remainder 206 40)
; 6 == 0? no


(gcd (remainder 206 40) (remainder 40 (remainder 206 40)))
(remainder 40 (remainder 206 40))
; 4 ==0? no


(gcd (remainder 40 (remainder 206 40)) (remainder (remainder 206 40) (remainder 40 (remainder 206 40))))
(remainder (remainder 206 40) (remainder 40 (remainder 206 40)))
; 2 == 0? no

(gcd (remainder (remainder 206 40) (remainder 40 (remainder 206 40))) (remainder (remainder 40 (remainder 206 40)) (remainder (remainder 206 40) (remainder 40 (remainder 206 40)))))
(remainder (remainder 40 (remainder 206 40)) (remainder (remainder 206 40) (remainder 40 (remainder 206 40))))
; 0 == 0 yes!


;==2 is the GCD


```
In the applicative-order evaluation?
```scheme
(define (gcd a b)
  (if (= b 0)
      a
      (gcd b (remainder a b))))
      
(gcd 206 40)
(gcd 40 (remainder 206 40))
(gcd 6 (remainder 40 6))
(gcd 4 (remainder 6 4))
(gcd 2 (remainder 4 2))

;== 2

;remainder is called 4 times

```

## 1.21
````scheme
199, 1999, 7
````


## 1.22
````scheme
 (define (display-error n error)
   (display n)
   (display error)
   )

(define (evaluate-next-prime start end)
  (timed-prime-test start)
  (search-for-primes (inc (inc start)) end)
  )

(define (search-for-primes start end)
  (if (= (remainder start 2) 0)
      ( display-error start " is not odd" )
      (if (<= start end)
          (evaluate-next-prime start end)      
          )
  )
  )
````

### smallest primes > 1000
````
1009 *** 3
1011
1013 *** 2
1015
1017
1019 *** 2
````
Average = 2.333

### smallest primes > 10000
````
10007 *** 5
10009 *** 5
....
10037 *** 5
````
Average = 5

### smallest primes > 100000
````
100003 *** 16
...
100019 *** 14
...
100043 *** 22
````
Average = 17.33

### smallest primes > 1000000
````
1000001
1000003 *** 42
...
1000033 *** 40
1000035
1000037 *** 44
````
Average = 42


Since the testing algorithm has order of growth of theta (sqrt (n)), you should expect that testing for primes around 10,000 should take about (sqrt 10) times as long as testing for primes around 1000
How well do the data for 100,000 and 1,000,000 support the (sqrt n) prediction? Is your result compatible with the notion that programs on your machine run in time proportional to the number of steps required for the computation?

sqrt 10 = 3.16 (approx)

1000 -> 10000
5/2.33 = 2.14

10000 -> 100000
17.33/5 = 3.46

100000 -> 1000000
42/17.33 = 2.42

They are definitely getting closer - it's almost like a converging sequence. The smaller numbers probably have more overhead so you don't see as much of the pure algorithm time math being dominant. 

Out of curiosity, 10,000,000 has an average of 124.66 sec to find the 3 smallest primes, and 124.66/42 = 2.96

## 1.23

````scheme
(define (next n)
  (if (= 2 n)
      3
      (+ n 2)
  ))


(define (smallest-divisor n)
  (find-divisor n 2))
(define (find-divisor n test-divisor)
  (cond ((> (square test-divisor) n) n)
        ((divides? test-divisor n) test-divisor)
        (else (find-divisor n (next test-divisor)))))
(define (divides? a b)
  (= (remainder b a) 0))
````
With the updated `next` function

Average time for 3 smallest primes > 1000 -> 1.67 / 2.33 = 0.71 times the amount of time as the old algorithm
for primes > 10000 -> 4 / 5 = 0.8 
for primes > 100000 -> 8.67 / 17.33 = 0.500
for primes > 1000000 -> 36 / 42 = 0.85

It's pretty close in some cases - still I think these numbers are probably too small for modern CPUs. Benchmarking is an imperfect science. 

## 1.24
Since the fermat test has O(log n) time complexity, it should take log (1000000/1000) or 3x the amount of time to find primes by 1000000 as opposed to 1000. 

````scheme
(define (fermat-test n)
  (define (try-it a)
    (= (expmod a n n) a))
  (try-it (+ 1 (random (- n 1)))))

(define (timed-prime-test n)
  (newline)
  (display n)
  (start-prime-test n (runtime)))
(define (start-prime-test n start-time)
  (if (fast-prime? n 1000)
      (report-prime (- (runtime) start-time))))
(define (report-prime elapsed-time)
  (display " *** ")
  (display elapsed-time))
````
Copied the code from the book above and just do the fermat test 10 times. 
Finding primes near 1000 -> 10.33
Finding primes near 1000000 -> 20.00
It seems like it's closer to 2x not 3x. 

## 1.25
Alyssa P. Hacker complains that we went to a lot of extra work in writing expmod. After all, she says, since we already know how to compute exponentials, we could have simply written

````scheme
(define (expmod base exp m)
  (remainder (fast-expt base exp) m))
````
Is she correct? Would this procedure serve as well for our fast prime tester? Explain.


It does appear to work but the function is significantly slower. This new expmod function averages 377 seconds for computing primes around 1000, which means it would not serve well for our prime tester. It calls the remainder function at the end, meaning it has to compute the remainder of the final computed exponental number. The `expmod` function we were using before returns the remainder with each recursion, which reduces the size of the number being evaluated. ([barry allison's SICP answers](https://wizardbook.wordpress.com/2010/11/29/exercise-1-25/) say this is x^n growth in time complexity vs the existing (n) growth of our given expmod function. I intuitively think his is probably correct)

## 1.26

````scheme
(define (expmod base exp m)
  (cond ((= exp 0) 1)
        ((even? exp)
         (remainder (* (expmod base (/ exp 2) m)
                       (expmod base (/ exp 2) m))
                    m))
        (else
         (remainder (* base (expmod base (- exp 1) m))
                    m))))
````

By writing the procedure like that, you have transformed the (log n) process into a (n) process. Explain.

So instead of squaring the single `expmod` argument, the computer now has to evaluate both `expmod` branches. It cannot rely on the fact that `expmod (expmod base (/ exp 2)` will return the same value with both successive calls. You lose that advantage. Instead of the number of steps growing (log n) it grows log n * log n, or log2^2 n, which is n. (I think)


## 1.27
Carmichael Numbers:
561, 1105, 1729, 2465, 2821, 6601

Using our existing `fermat-test` function they do seem to return true
````scheme
> (fermat-test 561)
#t
> (fermat-test 1105)
#t
> (fermat-test 1729)
#t
> (fermat-test 2465)
#t
> (fermat-test 2821)
#t
> (fermat-test 6601)
#t
````
Now let's write a procedure that takes an integer n and tests whether an is congruent to a modulo n for every a<n, and try your procedure on the given Carmichael numbers.

````scheme
(define (fermat-test-explicit n a)
    (= (expmod a n n) a))

(define (display-result n)
  (display " completed fermat tests for ") (display n)
  )

(define (carmichael-test n ctr)
  (if (not (fermat-test-explicit n ctr))
      ((display n) (display " appears to not pass the fermat test with a = ") (display ctr))
      (if (> ctr 0)
          (carmichael-test n (dec ctr))
          (display-result n)
      )
      )
  )
  
 (define (full-carmichael-test n)
   (carmichael-test n (dec n)))
      
  
````
and results
````
> (full-carmichael-test 561)
 completed fermat tests for 561
> (full-carmichael-test 1105)
 completed fermat tests for 1105
> (full-carmichael-test 1729)
 completed fermat tests for 1729
> (full-carmichael-test 2465)
 completed fermat tests for 2465
> (full-carmichael-test 2821)
 completed fermat tests for 2821
> (full-carmichael-test 6601)
 completed fermat tests for 6601
````

It seems they all do pass

## 1.28

Implement the miller-rabin sq root test

````scheme
(define (timed-prime-test-mr n)
  (newline)
  (display n)
  (start-prime-test-mr n (runtime)))
(define (start-prime-test-mr n start-time)
  (if (miller-rabin n 10)
      (report-prime (- (runtime) start-time))))


(define (miller-rabin n a)
  (define (try-it a)
    (= (expmod-miller a (- n 1) n) a))
  (try-it (+ 1 (random (- n 1)))))

(define (is-nontrivial-root m n)
  (cond ((and (not (= m 1)) (not (= m (- n 1))))
            (= (remainder (square m) n) 1 ))
        (else false)
       )
  )
        
  

(define (expmod-miller base exp m)
  (cond ((= exp 0) 1)
        ((even? exp)
         (if (is-nontrivial-root (expmod-miller base (/ exp 2) m) m) ; would really love to not need to evaluate this twice
             0
             (remainder (square (expmod base (/ exp 2) m)) m)
             )
         )
        (else
         (remainder (* base (expmod base (- exp 1) m))
                    m))))     
````

## 1.29
Simpson's Rule
````scheme

(define (integral-simpson f a b n)
  (define (arg-diff x) (+ x (/ (- b a) n)))
    (* (sum-simpson f a arg-diff n 0) (/ (/ (- b a) n) 3.0)))

(define (sum-simpson term a next b termno)
  (if (> termno b)
      0
      (+ (cond
           ((or (= termno b) (= a 0)) term a)
           ((= (remainder termno 2) 0) (* (term a) 2))
           ((= (remainder termno 2) 1) (* (term a) 4)))
         (sum-simpson term (next a) next b (inc termno)))))
````

integrating using SCIP integral at 0.01 step size: 0.24998750000000042
integrating using Simpson's Rule with 10 steps: 0.25
integrating using Simpson's Rule with 100 steps: 0.25
integrating using Simpson's Rule with 1000 steps: 0.25

Seems like I can't get it to be less accurate? Pretty good for an approximation. 


## 1.30 
````scheme
(define (sum-iter term a next b)
  (define (iter a result)
    (if (> a b)
        result
        (iter (next a) (+ (term a) result))))
  (iter a 0))
````

## 1.31
````scheme
; define a product procedure
(define (product term a next b)
  (if (> a b)
      1
      (* (term a)
         (product term (next a) next b))))

; define factorial in terms of 'product'
(define (factorial n)
  (define (identity x) x)
  (product identity 1 inc n))

;Also use product to compute approximations to pi (this is recursive)
(define (pi-product n)
  (define (pi-term n)
    (if (= (remainder n 2) 0)
        (/ (+ n 2.0) (+ n 1))
        (/ (+ n 1.0) (+ n 2)))
    )
  (* 4 (product pi-term 1 inc n))
  )


;Now iterative

(define (product-iter term a next b)
  (define (iter a result)
    (if (> a b)
        result
        (iter (next a) (* (term a) result))))
  (iter a 1))

(define (pi-product-iter n)
  (define (pi-term n)
    (if (= (remainder n 2) 0)
        (/ (+ n 2.0) (+ n 1))
        (/ (+ n 1.0) (+ n 2)))
    )
  (* 4 (product-iter pi-term 1 inc n))
  )
````

## 1.32
````scheme
; write accumulate
(define (accumulate combiner null-value term a next b)
  (if (> a b)
      null-value
      (combiner (term a)
                (accumulate combiner null-value term (next a) next b))))


; show how sum and product can be written as accumulate
; sum
(define (sum-acum term a next b)
  (define (add a b) (+ a b))
  (accumulate add 0 term a next b)
  )

; product
(define (prod-acum term a next b)
  (define (product a b) (* a b))
  (accumulate product 1 term a next b)
  )

; now write accumulate as iterative
(define (accumulate-iter combiner null-value term a next b)
  (define (iter a result)
    (if (> a b)
        result
        (iter (next a) (combiner (term a) result))))
  (iter a null-value))
  ````

## 1.33
; write filtered-accumulate
(define (filtered-accumulate filter combiner null-value term a next b)
  (if (> a b)
      null-value
      (combiner (if (filter a)
                    (term a)
                    null-value)
                (filtered-accumulate filter combiner null-value term (next a) next b))))

; sum of squares of prime numbers in the interval of a to b
(define (sum-squares a b)
  (define (add x y) (+ x y))
  (define (inc x) (+ 1 x))
  (define (square x) (* x x))
 (filtered-accumulate prime? add 0 square a inc b))
; (sum-squares 2 10)

;the product of all the positive integers less than n that are relatively prime to n (i.e., all positive integers i < n such that GCD(i,n) = 1).
  (define (identity x) (* 1 x))
  (define (gcd x y)
       (if (= 0 y)
          x
         (gcd y (remainder x y))
         )
    )
  (define (relative-prime? x y)
    (if (= (gcd x y) 1)
       #t
       #f
    ))

(define (rel-prime n)
 (filtered-accumulate prime? product 1 identity 1 inc n))
 
 ## 1.34
Seems like doing `(f f)` when `(define (f g) (g 2))` gives a syntax error. 

(f f) -> (f 2) -> (2 2) which leads to error

## 1.35
Show that the golden ratio (phi) is a fixed point of the transformation f(x) = 1+1/x
(phi)^2 = (phi) + 1
(phi) = 1 + 1/(phi)

compute (phi) by means of the fixed-point procedure.

````scheme
(fixed-point (lambda (x) (+ 1 (/ 1.0 x))) 1)
````
1.6180327868852458

## 1.36
Modify fixed-point so that it prints the sequence of approximations it generates, using the newline and display primitives
````scheme
(define tolerance 0.00001)
(define (fixed-point f first-guess)
  (newline)
  (display first-guess)
  (define (close-enough? v1 v2)
    (< (abs (- v1 v2)) tolerance))
  (define (try guess)
    (let ((next (f guess)))
      (if (close-enough? guess next)
          next
          (try next))))
  (try first-guess))
 
 ; find a solution to x^x = 1000 by finding a fixed point of x |->  log(1000)/log(x)

(fixed-point (lambda (x) (/ (log 1000) (log x))) 1.01)
; takes 39 guesses

(define (average x y) (/ (+ x y) 2))
(display "now with average damping")
(newline)
; now do average damping
(fixed-point (lambda (x) (average x (/ (log 1000) (log x)))) 1.01)
; only 15 guesses 
````

## 1.37
````scheme
(define (cont-frac n d k)
  (define (combine a b) (/ (n (- k a)) (+ (d (- k a)) b)))
  (define (identity x) x)
  (accum-iter combine (/ (n k) (d k)) identity 1 inc (- k 1)))

(cont-frac (lambda (i) 1.0)
           (lambda (i) 1.0)
           5)


(cont-frac (lambda (i) 1.0)
           (lambda (i) 1.0)
           10)


(cont-frac (lambda (i) 1.0)
           (lambda (i) 1.0)
           15)
````
0.625
0.6179775280898876
0.6180344478216819

15 iterations gets 4 decimal point accuracy.

Now as recursive 
````scheme
(define (accum-r combiner null-value term a next b)
  (display a)
  (newline)
  (if (< a b)
      null-value
      (combiner (term a)
                (accum-r combiner null-value term (next a) next b))))



(define (cont-frac n d k)
  (define (combine a b) (/ (n (- k a)) (+ (d (- k a)) b)))
  (define (identity x) x)
  (accum-r combine (/ (n k) (d k)) identity (- k 1) dec  1))
  ````
  
## 1.38
````scheme
; 1.38 
(define (euler-e n)
 (+ 2 (cont-frac (lambda (i) 1.0)
           (lambda (i) (if (= 2 (remainder i 3))
                           (+ 2 (* (/ (- i 2) 3) 2))
                           1.0))
           n))
  )

(euler-e 700)
````
2.71828182845

## 1.39
````scheme
; define (tan-cf x k)
(define (tan-cf x k)
 (cont-frac (lambda (i) (if (= 1 i)
                                 x
                                 (- 0 (* x x))))
           (lambda (i) (- (* 2.0 i) 1.0))
           k)
  )
````

## 1.40
Define a procedure cubic that can be used together with the newtons-method procedure in expressions of the form

(newtons-method (cubic a b c) 1)

to approximate zeros of the cubic x3 + ax2 + bx + c.

````scheme
(define (cubic a b c)
  (lambda (x) (+ (cube x) (* a (square x)) (* b x) c))
  )
````

## 1.41
Define a procedure double that takes a procedure of one argument as argument and returns a procedure that applies the original procedure twice. 
````scheme
(define (double f)
  (lambda (x) (f (f x)))
  )
; What value is returned by (((double (double double)) inc) 5)
21
````

## 1.42
; 1.42
; Let f and g be two one-argument functions. The composition f after g is defined to be the function x  f(g(x)).
; Define a procedure compose that implements composition. For example, if inc is a procedure that adds 1 to its argument,

````scheme
(define (compose f g)
  (lambda (x) (f (g x)))
  )
````
## 1.43
````scheme
(define (repeated f n)
    (define a 1)
    (define (next-compose f a n r)
      (if (= a n)
          r
          (next-compose f (inc a) n (compose f r))
      ))
    (next-compose f a n f))
````
    
## 1.44
 
