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
