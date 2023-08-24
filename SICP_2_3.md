
 ## 2.53
````scheme
; (list 'a 'b 'c)
;(a b c)


;(list (list 'george))
; ((george))

;(cdr '((x1 x2) (y1 y2)))
; ((y1 y2))


;(cadr '((x1 x2) (y1 y2)))
; (y1 y2)

;(pair? (car '(a short list)))
; #f

;(memq 'red '((red shoes) (blue socks)))
; #f

;(memq 'red '(red shoes blue socks))
; (red shoes blue socks)
````
## 2.54
````scheme
; define equal?
(define (xor a b)
  (or (and b (not a))
      (and a (not b))
      )
  )

(define (equal2? a b)
  (cond (
         (xor (null? a) (null? b))
         false)
        ((and (pair? a) (pair? b) (eq? (car a) (car b)))
         (equal2? (cdr a) (cdr b))
         )
        (
         else (eq?  a  b))))
````


## 2.55
````scheme
; (car ''abracadabra) returns just '
; This is because it's the literal literal - this is a way for the interpreter to signify that the object is a symbol.
; Pair? returns true for ''quote. It's an object/pair where the quote signifies that it's a symbol - the single quote sign is a synonym for the (quote) operator.
; so (quote (quote a)) is the same as ''a -quote is the first item in that list.
````



 ## 2.56
````scheme
(define (exponentiation? x)
  (and (pair? x) (eq? (car x) '**)))

(define (base x)
  (cadr x))

(define (exponent x)
  (caddr x))

(define (make-exponentiation b e)
  (cond ((=number? e 0) 1)
    	((=number? e 1) b)
    	((and (number? b) (number? e)) (expt b e))
    	(else (list '** b e))))  


(define (deriv2 exp var)
  (cond ((number? exp) 0)
    	((variable? exp)
     	(if (same-variable? exp var) 1 0))
    	((sum? exp)
     	(make-sum (deriv2 (addend exp) var)
               	(deriv2 (augend exp) var)))
    	((product? exp)
     	(make-sum
       	(make-product (multiplier exp)
                     	(deriv2 (multiplicand exp) var))
       	(make-product (deriv2 (multiplier exp) var)
                     	(multiplicand exp))))
      	((exponentiation? exp)
         (make-product
          (exponent exp)
          (make-product 
           (make-exponentiation (base exp)
                             (make-sum (exponent exp) -1))
           (deriv2 (base exp) var)
           )
          )
         )
    	(else
     	(error "unknown expression type -- DERIV" exp)))
  )
````
## 2.57 Extend the differentiation program to handle sums and products of arbitrary numbers of (two or more) terms.
````scheme
;The augend is the third item of the sum list:
(define (augend2 s)
                          ; recursive sum of list
                          (define (recur-sum l sum)
                            (if (> (length l) 1)
                                (recur-sum (cdr l) (make-sum (car l) sum))
                                 (if (pair? l)
                                    (make-sum sum (car l))
                                    (make-sum sum l))
                                )
                                )
                            
  (
                    if (> (length s) 3)
                       
                        (recur-sum (cdr (cdr s)) 0)
                        (caddr s))
                    
  )

;The multiplicand is the third item of the product list:
(define (multiplicand2 p)
                          ; recursive product of list
                          (define (recur-product l prod)
                            (if (> (length l) 1)
                                (recur-product (cdr l) (make-product (car l) prod))
                                (if (pair? l)
                                    (make-product prod (car l))
                                    (make-product prod l))
                                )
                            )
  (if (> (length p) 3)
                        (recur-product (cdr (cdr p)) 1)
                        (caddr p))            
  )
````

## 2.58
````scheme
;a.Yes - these procedures need to be modified:
;(define (sum? x)
;  (and (pair? x) (eq? (cadr x) '+)))
;
;(define (product? x)
;  (and (pair? x) (eq? (cadr x) '*)))
;
;(define (addend s) (car s))
;
;(define (multiplier s) (car s))
;
;b. I tried a few different things before eventually looking up how other people were solving the same problem.
; My attempted solution was to somehow find and break up the algebraic notation and then run deriv on each component part recursively.
; The problem with this is that the deriv function does not recurse on both sides of the operator, just on the multiplicand and aguend sides. As such this solution was unworkable without changes to the deriv function
; Looking up answers to what other people had done it seems I was missing the key introduced concept, `memq` that would help resolve some of the challenges that had me building this on my own. This is the answer from meteorgan on the scip community wiki which I believe is what was expected to be used as the answer

 (define (operation expr) 
   (if (memq '+ expr) 
       '+ 
       '*))

 (define (sum? expr) 
   (eq? '+ (operation expr)))

 (define (addend expr) 
   (define (iter expr result) 
         (if (eq? (car expr) '+) 
           result 
           (iter (cdr expr) (append result (list (car expr)))))) 
   (let ((result (iter expr '()))) 
     (if (= (length result) 1) 
         (car result) 
         result)))

(define (augend expr) 
   (let ((result (cdr (memq '+ expr)))) 
     (if (= (length result) 1) 
         (car result) 
         result))) 
  
 (define (product? expr) 
   (eq? '* (operation expr))) 
 (define (multiplier expr) 
   (define (iter expr result) 
         (if (eq? (car expr) '*) 
           result 
           (iter (cdr expr) (append result (list (car expr)))))) 
   (let ((result (iter expr '()))) 
     (if (= (length result) 1) 
         (car result) 
         result))) 
 (define (multiplicand expr) 
   (let ((result (cdr (memq '* expr)))) 
     (if (= (length result) 1) 
         (car result) 
         result))) 
````
## 2.59
````scheme
(define (element-of-set? x set)
  (cond ((null? set) false)
        ((equal? x (car set)) true)
        (else (element-of-set? x (cdr set)))))


(define (adjoin-set x set)
  (if (element-of-set? x set)
      set
      (cons x set)))


(define (intersection-set set1 set2)
  (cond ((or (null? set1) (null? set2)) '())
        ((element-of-set? (car set1) set2)        
         (cons (car set1)
               (intersection-set (cdr set1) set2)))
        (else (intersection-set (cdr set1) set2))))


(define (union-set set1 set2)
  (cond 
        ((null? set1) set2)
        ((not (element-of-set? (car set1) set2))
         (union-set (cdr set1) (cons (car set1) set2)))
        (else
         (union-set (cdr set1) set2)
        )
  )
  )
````
## 2.60
````scheme
; sets with duplicates
  
; this is the same
(define (element-of-set-dupes? x set)
  (cond ((null? set) false)
        ((equal? x (car set)) true)
        (else (element-of-set? x (cdr set)))))

; just adding to the set
(define (adjoin-set-dupes x set)
  (cons x set))

; can't really be improved
(define (intersection-set-dupes set1 set2)
  (cond ((or (null? set1) (null? set2)) '())
        ((element-of-set? (car set1) set2)        
         (cons (car set1)
               (intersection-set (cdr set1) set2)))
        (else (intersection-set (cdr set1) set2))))

; trivial
(define (union-set-dupes set1 set2)
  (append set1 set2)
  )
````
