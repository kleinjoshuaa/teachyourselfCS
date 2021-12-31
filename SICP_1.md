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
      (p)))
````
0 = 0, so in this case we return zero without evaluating (p).
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
      (p)))
````
Then we try to replace (p) with (p) with (p) but it keeps going in an infinite loop because we cannot fully replace all variables.
