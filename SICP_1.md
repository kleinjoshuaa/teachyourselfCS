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
  - ~~9~~ 6 (got this incorrect from misreading)
  - 16
  
 1.2
  - (/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5))))) (* 3 (- 6 2) (- 2 7)))
  
 1.3
 (define (square x) (* x x))
  - (define (ss2l x y z) ( + (square(cond ((> x y) (> x z) (square()))
