# Exercises from SICP Chapter 1

1.1
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
  
1.2

````scheme
(/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5))))) (* 3 (- 6 2) (- 2 7)))
````

1.3
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

1.4
````scheme
(define (a-plus-abs-b a b)
  ((if (> b 0) + -) a b))
````
If b is greater than zero, the above procedure would add a and b. If b was less than zero, it would subtract b from a.

1.5


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


1.6
- The predicate expression is evaluated first, and the result determines whether to evaluate the consequent or the alternative expression during the evaluation of a normal `if` statement. Because lisp uses applicative order evaluation it will try to evaluate all of the arguments to the `cond` function, which, since it is calling itself recursively, will keep the function in an infinite loop.

1.7
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

1.8
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

1.9
-- evaluate `(+ 4 5)` using the substitution model 
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
(+ (+ 3 6))
(+ (+ (+ 2 7)))
(+ (+ (+ (+ 1 8))))
(+ (+ (+ (+ (+ 0 9)))))
````
Resolution:
````scheme
(+ (+ (+ (+ (+ 0 9)))))
9
````
This is iterative.


1.10
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
