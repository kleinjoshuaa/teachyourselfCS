I will be breaking up chapters 2 and 3 by subsection since they are about twice the size of chapter 1. 

## 2.1

Define a better version of make-rat that handles both positive and negative arguments. 
Make-rat should normalize the sign so that if the rational number is positive, both the numerator and denominator are positive, 
and if the rational number is negative, only the numerator is negative.

```scheme
  (define (is-correct-sign? x sign)
    (if (or (and (> x 0) sign)  (and (< x 0) (not sign)))
       #t
       #f) 
    )
  (define (set-sign x sign)
    (if (is-correct-sign? x sign)
        x
        (* -1 x)))
(define (make-rat-improved n d)
   (define (pos?)
    (if(and (> n 0) (> d 0))
       #t
       #f))
  (let ((g (gcd n d)))
    (cons (set-sign (/ n g) (pos?)) (set-sign (/ d g) #t))))
  ````
  
## 2.2
````scheme
; 2.2
; Exercise 2.2.  Consider the problem of representing line segments in a plane. Each segment is represented as a pair of points: a starting point and an ending point.
; Define a constructor make-segment

(define (make-segment start end)
  (cons start end)
  )

; and selectors start-segment and end-segment that define the representation of segments in terms of points.

(define (start-segment s)
  (car s))

(define (end-segment s)
  (cdr s))

; Furthermore, a point can be represented as a pair of numbers: the x coordinate and the y coordinate.
; Accordingly,
; specify a constructor make-point

(define (make-point x y)
 (cons x y)
 )

; and selectors x-point and y-point that define this representation.

(define (x-point p) (car p))
(define (y-point p) (cdr p))

; Finally, using your selectors and constructors, define a procedure midpoint-segment that takes a line segment as argument and returns its midpoint (the point whose coordinates are the average of the coordinates of the endpoints).;

(define (midpoint-segment s)
  (make-point (average (x-point (start-segment s)) (x-point (end-segment s)) ) (average (y-point (start-segment s)) (y-point (end-segment s)) ))
  )
  


; To try your procedures, you'll need a way to print points:

(define (print-point p)
  (newline)
  (display "(")
  (display (x-point p))
  (display ",")
  (display (y-point p))
  (display ")"))
  ````
## 2.3
````scheme
; 2.3  Implement a representation for rectangles in a plane.
; In terms of your constructors and selectors, create procedures that compute the perimeter and the area of a given rectangle.

(define (make-rectangle p1 p2)
  (cons p1 p2)
  )

(define (get-x1 r)
  (car (car r))
  )
(define (get-y2 r)
  (cdr (cdr r))
  )

(define (get-x2 r)
  (car (cdr r))
  )

(define (get-y1 r)
  (cdr (car r))
  )

(define (get-height r)
      (abs (- (get-y1 r) (get-y2 r)))
  )

(define (get-width r)
  (abs (- (get-x1 r) (get-x2 r)))
  )

(define (rectangle-area r)
  (* (get-width r) (get-height r))
  )

(define (rectangle-perimiter r)
  (+ (* 2 (get-width r) ) (* 2 (get-height r)))
  )

(rectangle-perimiter (make-rectangle (make-point 0 0) (make-point 3.0 3.0)))
(rectangle-area (make-rectangle (make-point 0 0) (make-point 3.0 3.0)))

; Now implement a different representation for rectangles. Can you design your system with suitable abstraction barriers, so that the same perimeter and area procedures will work using either representation?

; The perimiter and area procedures will work because they depend on get-width and get-height functions.  The getter functions would be the only ones needing to change

; for example:

(define (make-rectange-2 x x y y)
  (cons (cons x x) (cons y y))
  )
````

## 2.4
````scheme
; 2.4

(define (cons-2 x y)
  (lambda (m) (m x y)))

(define (car-2 z)
  (z (lambda (p q) p)))

; verify that (car (cons x y)) yields x for any objects x and y.
; (cons x y) -> λ(m) (m x y)
; (λ(m) (m x y) (λ (p q) p))
; λ(λ(p q) p) (λ(p q) p) x y)
; (λ(x y) x) QED

; What is the corresponding definition of cdr?
(define (cdr-2 z)
  (z (lambda (p q) q)))
(cdr-2 (cons-2 0 1))
````

## 2.5
My first pass I probably made it more complicated than it needed to be. We don't have to use `log`. I re-wrote it using divisors which is what I think what the intent was



````scheme
(define (car-3 z)
   (define (rem x a)
    (if (= (remainder x 2) 0)
        (rem (/ x 2) (inc a))
        a)
  )
  (rem z 0))

  
(car-3 (cons-3 5 7))

(define (cdr-3 z)
   (define (rem x a)
    (if (= (remainder x 3) 0)
        (rem (/ x 3) (inc a))
        a)
  )
  (rem z 0))

````

## 2.6
````scheme
; 2.6
; In case representing pairs as procedures wasn't mind-boggling enough, consider that, in a language that can manipulate procedures, we can get by without numbers
; (at least insofar as nonnegative integers are concerned) by implementing 0 and the operation of adding 1 as

(define zero (lambda (f) (lambda (x) x)))

(define (add-1 n)
  (lambda (f) (
               lambda (x) (f ((n f) x))
                )
    ))

;This representation is known as Church numerals, after its inventor, Alonzo Church, the logician who invented the  calculus.
;Define one and two directly (not in terms of zero and add-1). (Hint: Use substitution to evaluate (add-1 zero)).

; (add-1 (zero))
; λ(f) (λ(x) (f (( λ(f) λ(x) x) f) x ))
; λ(f) (λ(x) (f (λ(x) x) f ) x))
; λ(f) (λ(x) (f (f x))

(define one (lambda (f) (lambda (x) (f (f x)))))


; (add-1 (one))
; λ(f) (λ(x) (f (( λ(f) λ(x) (f (f x) f) x ))
; λ(f) (λ(x) (f (λ(x) (f (f x)) f ) x))
; λ(f) (λ(x) (f (f (f x))
(define two (lambda (f) (lambda (x) (f (f (f x)))))))


; Give a direct definition of the addition procedure + (not in terms of repeated application of add-1)
; I really had no idea how to do this - I had to search for other people's sicp answers
;something to maybe come back to.
(define (add n a)
  (lambda (f) (
               lambda (x) (( n f )((a f) x))
                )
    ))

````
## 2.7
````scheme
(define (upper-bound interval) (cdr interval))
(define (lower-bound interval) (car interval))
````

## 2.8
Interval subtraction is the opposite of addition. In this case we are trying to get the min and max of the subtraction
````scheme
(define (sub-interval x y)
  (make-interval (- (lower-bound y) (upper-bound x))
                 (- (upper-bound y) (lower-bound x))))
````

## 2.9
````scheme
;The width of an interval is half of the difference between its upper and lower bounds. The width is a measure of the uncertainty of the number specified by the interval.
; For some arithmetic operations the width of the result of combining two intervals is a function only of the widths of the argument intervals,
; whereas for others the width of the combination is not a function of the widths of the argument intervals.

(define (width-interval i)
  (/ ( - (upper-bound i) (lower-bound)) 2)
  )

; Show that the width of the sum (or difference) of two intervals is a function only of the widths of the intervals being added (or subtracted).

; (width-interval(add-interval ((make-interval l1 u1) (make-interval l2 u2))) - from definition of procedures
; (width-interval( l1+l2 u1+u2) - substitution method
; ((u1+u2) - (l1+l2))/2 - from definition of width interval
; (u1+u2 + -l1 + - l2) /2
; ((u1 - l1) + ( u2 - l2 )) /2
; Now let w1 = u1-l1 and w2=u2-l2 for the interval widths
; width = (w1 + w2) /2 QED


; Give examples to show that this is not true for multiplication or division.

(mul-interval (make-interval 1 5) (make-interval 2 6))
; result: (2 . 30)
(mul-interval (make-interval 0 4) (make-interval 3 7))
; result (0 . 28)
(div-interval (make-interval 1 5) (make-interval 2 6))
; result (0.167 . 2.5)
(div-interval (make-interval 0 4) (make-interval 3 7))
; result (0 . 1.333)
````
## 2.10
````scheme
(define (div-interval-improved x y)
  (define (check-span-zero interval)
    (and ( > (upper-bound interval) 0) (< (lower-bound interval) 0)))
   
  (if (check-span-zero y)
      (error "Second Interval Argument spans zero")
  (mul-interval x 
                (make-interval (/ 1.0 (upper-bound y))
                               (/ 1.0 (lower-bound y)))))
  )
````
## 2.11
````scheme
; 2.11
; In passing, Ben also cryptically comments: ``By testing the signs of the endpoints of the intervals, it is possible to break mul-interval into nine cases, only one of which requires more than two multiplications.''
; Rewrite this procedure using Ben's suggestion.

; I wasn't able to quite figure out the trouble case was without a bit of help, I figured it had something to do with zeros. 

(define (mul-interval-improved x y)
  (define (pos? x)
    (>= x 0)
    )
  (define (neg? x)
    (not (pos? x)))

    (let ((x1 (lower-bound x))
        (y1  (lower-bound y) )
        (x2  (upper-bound x) )
        (y2  (upper-bound y) )) 
  (if (and (pos? x1 ) (pos? y1))
      (make-interval (* x1 y1)
                   (* x2 y2))
  (if (and (neg? x2) (neg? y2))
      (make-interval (* x2 y2)
                   (* x1 y1))         
  (if (and (neg? x2) (pos? y1))
      (make-interval (* x1 y2)
                   (* x2 y1))
 (if (and (neg? y2) (pos? x1))
      (make-interval (* y1 x2)
                   (* y2 x1))
  (if (and (neg? x1) (pos? x2) (pos? y1))
      (make-interval (* x1 y2)
                   (* x2 y2))
  (if (and (neg? y1) (pos? y2) (pos? x1))
      (make-interval (* x2 y1)
                   (* x2 y2))
  (if (and (neg? x2) (neg? y1) (pos? y2))
      (make-interval (* x1 y2)
                   (* x2 y1))
      
  (if (and (neg? y2) (neg? x1) (pos? x2))
      (make-interval (* x2 y1)
                   (* x1 y1))
  (if (and (pos? y2) (pos? x2) (neg? x1) (neg? x2))
      (let ((p1 (* x1 y1))
        (p2 (* x1 y2))
        (p3 (* x2 y1))
        (p4 (* x2 y2)))
        (make-interval (min p1 p2 p3 p4)
                   (max p1 p2 p3 p4))
        )
  )))))))
  ))
  ))
````
## 2.12
(define (make-center-width c w)
  (make-interval (- c w) (+ c w)))
(define (center i)
  (/ (+ (lower-bound i) (upper-bound i)) 2))
(define (width i)
  (/ (- (upper-bound i) (lower-bound i)) 2))

(define (make-center-percent c p)
  (make-interval (* c (- 1 p)) (* c (+ 1 p)) ))

(define (percent interval)
  (- (/ (upper-bound interval) (center interval)) 1 ))

## 2.13
````scheme
; 2.13
; Show that under the assumption of small percentage tolerances there is a simple formula for the approximate percentage tolerance of the product of two intervals in terms of the tolerances of the factors.
; You may simplify the problem by assuming that all numbers are positive.

;  interval x - (centerx * (1-0.5*tolx)) (centerx * (1+0.5*tolx))
; interval y - (centery * (1-0.5*toly)) (centerx * (1+0.5*toly))
; product - [ (CenterX * (1-1/2 * tol x) * CenterY * (1- 1/2 * tol y), (CenterX * (1+1/2 * tol x) * CenterY * (1+ 1/2 * tol y)]
; [ (CenterX* CenterY * (1-1/2 *  (tol x + tol y ) + 0.25*(tolX*tolY)), (CenterX* CenterY * (1+1/2 *  (tol x + tol y ) + 0.25*(tolX*tolY))]

; Theoretically, then the tolerance of the product is the sum of the component tolerances
````
## 2.14
````scheme
; 2.14
(define (par1 r1 r2)
  (div-interval (mul-interval r1 r2)
                (add-interval r1 r2)))
(define (par2 r1 r2)
  (let ((one (make-interval 1 1))) 
    (div-interval one
                  (add-interval (div-interval one r1)
                                (div-interval one r2)))))

; Investigate the behavior of the system on a variety of arithmetic expressions.
; Make some intervals A and B, and use them in computing the expressions A/A and A/B.
; You will get the most insight by using intervals whose width is a small percentage of the center value.

(par1 (make-center-percent 5 .01) (make-center-percent 10 0.001))
; (3.2835657370517937 . 3.3835676037483258)
(percent (par1 (make-center-percent 5 .01) (make-center-percent 10 0.001)))
; 0.01499923004157755
(center (par1 (make-center-percent 5 .01) (make-center-percent 10 0.001)))
; 3.3335666704000597
(par2 (make-center-percent 5 .01) (make-center-percent 10 0.001))
; (3.3099397590361446 . 3.3566069057104913)
(percent (par2 (make-center-percent 5 .01) (make-center-percent 10 0.001)))
; 0.007000198006732239
(center (par2 (make-center-percent 5 .01) (make-center-percent 10 0.001)))
; 3.333273332373318

; Center and percent are different for each dispite the mathematically equivalent formula
````
## 2.15
````scheme
; 2.15
; Eva Lu Ator, another user, has also noticed the different intervals computed by different but algebraically equivalent expressions.
; She says that a formula to compute with intervals using Alyssa's system will produce tighter error bounds if it can be written in such a form that no variable that represents an uncertain number is repeated.
; Thus, she says, par2 is a ``better'' program for parallel resistances than par1. Is she right? Why?

; Yes - she is correct. Reducing the number of independant variables in the computation does reduce the total error of the system.
````
## 2.16
````scheme
; Not even gonna try this one - but safe to say I think the solution has to do with interval division as it is defined here not having an identity relation and therefore isn't actually a full definition of all of the algebraic operations.
; for example:

(div-interval (make-center-percent 5 3)  (make-center-percent 5 3)) ; (-2, 1)
(div-interval (make-center-percent 10 3)  (make-center-percent 10 3)); (-2 1)
(div-interval (make-center-percent -5 3)  (make-center-percent -5 3)); (-2 1)
(div-interval (make-center-percent 5 .003)  (make-center-percent 5 .003)); (0.99, 1.006)

; This is not identity - it's not a complete algebraic system
````
