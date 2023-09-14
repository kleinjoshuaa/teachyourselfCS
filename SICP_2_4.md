 ## 2.73
````scheme
; Section 2.3.2 described a program that performs symbolic differentiation:
#|
(define (deriv exp var)
  (cond ((number? exp) 0)
        ((variable? exp) (if (same-variable? exp var) 1 0))
        ((sum? exp)
         (make-sum (deriv (addend exp) var)
                   (deriv (augend exp) var)))
        ((product? exp)
         (make-sum
           (make-product (multiplier exp)
                         (deriv (multiplicand exp) var))
           (make-product (deriv (multiplier exp) var)
                         (multiplicand exp))))
        <more rules can be added here>
        (else (error "unknown expression type -- DERIV" exp))))


We can regard this program as performing a dispatch on the type of the expression to be differentiated.
In this situation the ``type tag'' of the datum is the algebraic operator symbol (such as +) and the operation being performed is deriv.
We can transform this program into data-directed style by rewriting the basic derivative procedure as

(define (deriv exp var)
   (cond ((number? exp) 0)
         ((variable? exp) (if (same-variable? exp var) 1 0))
         (else ((get 'deriv (operator exp)) (operands exp)
                                            var))))
(define (operator exp) (car exp))
(define (operands exp) (cdr exp))


A. Sum and product have been turned into operators that work on the operand. They have been generalized out of the function.
    Number and same-variable are not operators/operand setup

B.
        (define (deriv-sum exp var)
         (make-sum (deriv (addend exp) var)
                   (deriv (augend exp) var)))
        (put 'deriv '(+) deriv-sum)
        
        (define (deriv-product exp var)
         (make-sum
           (make-product (multiplier exp)
                         (deriv (multiplicand exp) var))
           (make-product (deriv (multiplier exp) var)
                         (multiplicand exp))))
           )
        (put 'deriv '(*) deriv-product)
        ; interface to rest of system
        (


C.
        (put 'deriv '(**) deriv-exp)
	((define deriv-exp exp var)
         (make-product
          (exponent exp)
          (make-product 
           (make-exponentiation (base exp)
                             (make-sum (exponent exp) -1))
           (deriv2 (base exp) var)
           )
          )
         )

D. We would have to swap the order of function arguments

````
## 2.74
````scheme

#|
A. (define (get-record eeId file)
   ((get (get-record '(division)) eeName file))
    Each personell file should be tagged with a division to allow the get-record function for each division to be appropriately called for it.

B.(define (get-salary record)
   ((get (get-salary '(division)) record))
   Each record should be tagged with a division to allow the get-salary function for each division to be appropriately called for it.

C. (define (find-employee recordFiles eeName)
      (if (not (pair? recordFiles))
               error 'Employee Not Found: ' eeName)
      (let (recordFile (car recordFiles) (foundRecord ((get (get-record '(division)) eeName recordFile))
       ( if (pair? foundRecord)
             foundRecord
             (find-employee (cdr recordFiles) eeName)
            )
    )

D. Incorporating new personnel information into the central system - requires any new file of records tagged with a tag that that company along with
   methods for get-record and get-salary for the tag

|#
````
 ## 2.75
````scheme
(define (square x)
  (* x x))

(define (make-from-mag-ang x y)
  (define (dispatch op)
    (cond ((eq? op 'magnitude) x)
          ((eq? op 'angle) y)
          ((eq? op 'real-part)
           (* x (cos y)))
          ((eq? op 'imag-part) (* x (sin y)))
          (else
           (error "Unknown op -- MAKE-FROM-REAL-IMAG" op))))
  dispatch)

````

## 2.76

````scheme
#|
Explicit Dispatch - adding a new types and new operations means adding new functions for each with explicit names that help understand their types
Data-directed-style - lets you add a tag to the objects and functions and lets those operators work successfully on those data types
Message Passing - pass a message to the function to let it know which method to use.

It really depends how you want to handle your codebase when it gets updated. With explicit dispatch new data types need all new functions and datatypes.
Data-directed style means the functions themselves stay the same but you need to add the tags and methods to operate on the tagged data.
For message passing all of the methods are within the individual functions. 

|#
