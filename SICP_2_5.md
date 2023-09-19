## 2.77
This works because it allows the real/imag/mag/and angle functions to be defined for complex numbers. 

`(magnitude (complex rectangular 3 4))`

-> `(apply-generic 'magnitude (complex rectangular 3 4))`

-> `(apply-generic 'magnitude (complex rectangular 3 4))`

-> `(apply-generic 'magnitude (rectangular 3 4))`

-> `(magnitude (rectangular 3 4))`

-> `(sqrt (+ (square  (real-part (3 4)))
             (square  (imag-part (3 4)))))`

-> `(sqrt (+ 9 16))`

-> `5`

## 2.78
````scheme
 ( if (eq? type-tag  'scheme-number)
     contents
     (cons type-tag contents)))

(define (type-tag datum)
  (cond ((pair? datum)
            (car datum))
        ((number? datum)
             'scheme-number)
        (else
          (error "Bad tagged datum -- TYPE-TAG" datum)))
  )

(define (contents datum)
  (cond ((pair? datum)
            (cdr datum))
        ((number? datum)
             datum)
        (else
          (error "Bad tagged datum -- CONTENTS" datum))))
````

## 2.79
````scheme
; generic
(define (equ? x y) (apply-generic 'equ? x y))

;regular numbers
   (put 'equ? '(scheme-number scheme-number)
       (lambda (x y) (= x y))


;rational
  (define (equ-rat x y)
    (if (and (= (numer x) (numer y)) (= (denom x) (denom y)))
                  #t
                  #f))
  (put 'equ? '(rational rational)
       (lambda (x y) (equ-rat x y)))

;complex
    (define (equ-complex z1 z2)
    (if (and (= (real-part z1) (real-part z2)) (= (imag-part z1) (imag-part z2)))
                  #t
                  #f))
  (put 'equ? '(complex complex)
       (lambda (z1 z2)  (equ-complex z1 z2)))
````

## 2.80
````scheme
; generic
(define (=zero? x) (apply-generic '=zero? x))

;regular numbers
  (put '=zero? '(scheme-number)
       (lambda (x) (= x 0)))

;rational
  (define (=zero? x)
    (if (= 0 (numer x))
                  #t
                  #f))
  (put '=zero? '(rational)
       (lambda (x) (=zero? x)))


;complex
      (define (=zero? z1)
    (if (and (= (real-part z1) 0) (= (imag-part z1) 0))
                  #t
                  #f))
  (put '=zero? '(complex)
       (lambda (z1)  (=zero? z1)))
````

## 2.81
````scheme

a. the code goes into an infinite loop

b. Yes - it works as is.

c. 
(define (apply-generic op . args)
  (let ((type-tags (map type-tag args)))
    (let ((proc (get op type-tags)))
      (if proc
          (apply proc (map contents args))
          (if (= (length args) 2)
              (let ((type1 (car type-tags))
                    (type2 (cadr type-tags))
                    (a1 (car args))
                    (a2 (cadr args)))
                (cond ((eq? type1 type2)
                     ( error "No method for this type" type1))
                      (else
                (let ((t1->t2 (get-coercion type1 type2))
                      (t2->t1 (get-coercion type2 type1)))
                  (cond (t1->t2
                         (apply-generic op (t1->t2 a1) a2))
                        (t2->t1
                         (apply-generic op a1 (t2->t1 a2)))
                        (else
                         (error "No method for these types"
                                (list op type-tags))))))))
              (error "No method for these types"
                     (list op type-tags)))))))
````

## 2.82
````scheme
(define (apply-generic op . args)
  (let ((type-tags (map type-tag args)))
    (let ((proc (get op type-tags)))
      (if proc
          (apply proc (map contents args))
          (if (= (length args) 2)
              (let ((type1 (car type-tags))
                    (type2 (cadr type-tags))
                    (a1 (car args))
                    (a2 (cadr args)))
                (cond ((eq? type1 type2)
                     ( error "No method for this type" type1))
                      (else
                (let ((t1->t2 (get-coercion type1 type2))
                      (t2->t1 (get-coercion type2 type1)))
                  (cond (t1->t2
                         (apply-generic op (t1->t2 a1) a2))
                        (t2->t1
                         (apply-generic op a1 (t2->t1 a2)))
                        (else
                         (error "No method for these types"
                                (list op type-tags))))))))
              (error "No method for these types"
                     (list op type-tags)))))))
````
