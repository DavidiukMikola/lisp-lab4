<p align="center"><b>МОНУ НТУУ КПІ ім. Ігоря Сікорського ФПМ СПіСКС</b></p>
<p align="center">
<b>Звіт з лабораторної роботи 4</b><br/>
"Функції вищого порядку та замикання"<br/>
дисципліни "Вступ до функціонального програмування"
</p>
<p align="right"><b>Студентка</b>: Давидюк Микола Юрійович КВ-12</p>
<p align="right"><b>Рік</b>: 2024</p>

## Загальне завдання
1. Переписати функціональну реалізацію алгоритму сортування з лабораторної
роботи 3 з такими змінами:
використати функції вищого порядку для роботи з послідовностями (де це
доречно);
додати до інтерфейсу функції (та використання в реалізації) два ключових
параметра: key та test , що працюють аналогічно до того, як працюють
параметри з такими назвами в функціях, що працюють з послідовностями. При
цьому key має виконатись мінімальну кількість разів.
2. Реалізувати функцію, що створює замикання, яке працює згідно із завданням за
варіантом (див. п 4.1.2). Використання псевдо-функцій не забороняється, але, за
можливості, має бути мінімізоване.

## Варіант першої частини - 7
Алгоритм сортування Шелла за незменшенням.
## Лістинг реалізації першої частини завдання
```lisp
(defun shell-sort (lst n gap i key test)
  (if (>= gap 1)
      (if (< i n)
          (let ((j i))
            (if (and (>= j gap)
                     (funcall test (funcall key (nth j lst))
                              (funcall key (nth (- j gap) lst))))
                (progn
                  (rotatef (nth j lst) (nth (- j gap) lst))
                  (shell-sort lst n gap (- j gap) key test))
                (shell-sort lst n gap (+ i 1) key test)))
          (shell-sort lst n (floor (/ gap 2)) 0 key test))
      lst))

(defun shell-sort-wrapper (lst &key (key #'identity) (test #'<))
  (let ((new-lst (copy-list lst))) 
    (let ((n (length lst)))
      (shell-sort new-lst n (floor (/ n 2)) 0 key test))))
```

### Тестові набори та утиліти першої частини
```lisp
(defun check-shell-sort (test-name input expected &key (key #'identity) (test #'<))
  "Helper function to run individual test cases."
  (let ((result (shell-sort-wrapper input :key key :test test)))
    (if (equal result expected)
        (format t "~a passed. Result: ~a~%" test-name result)
        (format t "~a failed.~%  Input: ~a~%  Expected: ~a~%  Got: ~a~%" test-name input expected result))))

 (defun test-shell-sort ()
  "Run a series of tests for shell-sort-wrapper."
  (format t "Start testing shell-sort-wrapper function~%")
  (check-shell-sort "Test 1" '(5 3 8 6 2 7 4 1) '(1 2 3 4 5 6 7 8))
  (check-shell-sort "Test 2" '(10 9 8 7 6 5 4 3 2 1) '(1 2 3 4 5 6 7 8 9 10))
  (check-shell-sort "Test 3" '(1 1 1 1 1) '(1 1 1 1 1))
  (check-shell-sort "Test 4" '(100 -10 0 50 -50) '(-50 -10 0 50 100))
  (check-shell-sort "Test 5" '(-1 -2 -3 -4 -5) '(-5 -4 -3 -2 -1))
  (check-shell-sort "Test 6" '(2 2 2 2 1 1 1 1 3 3 3 3) '(1 1 1 1 2 2 2 2 3 3 3 3))
  (check-shell-sort "Test 7" '((1 . "a") (3 . "b") (2 . "c")) '((1 . "a") (2 . "c") (3 . "b"))
                    :key #'car :test #'<)
  (check-shell-sort "Test 8" '((3 . 9) (1 . 2) (2 . 5)) '((1 . 2) (2 . 5) (3 . 9))
                    :key #'cdr :test #'<)
  (check-shell-sort "Test 9" '(5 4 3 2 1) '(5 4 3 2 1)
                    :test #'=)
  (format t "Testing completed~%"))
```
### Тестування першої частини
```lisp
CL-USER>  (test-shell-sort)
Start testing shell-sort-wrapper function
Test 1 passed. Result: (1 2 3 4 5 6 7 8)
Test 2 passed. Result: (1 2 3 4 5 6 7 8 9 10)
Test 3 passed. Result: (1 1 1 1 1)
Test 4 passed. Result: (-50 -10 0 50 100)
Test 5 passed. Result: (-5 -4 -3 -2 -1)
Test 6 passed. Result: (1 1 1 1 2 2 2 2 3 3 3 3)
Test 7 passed. Result: ((1 . a) (2 . c) (3 . b))
Test 8 passed. Result: ((1 . 2) (2 . 5) (3 . 9))
Test 9 passed. Result: (5 4 3 2 1)
Testing completed
```
## Варіант другої частини - 7
Написати функцію remove-each-nth-fn , яка має один основний параметр n та один
ключовий параметр — функцію key . remove-each-nth-fn має повернути функцію, яка
при застосуванні в якості першого аргументу remove-if робить наступне: кожен n -ний
елемент списку-аргумента remove-if , для якого функція key повертає значення t
(або не nil ), видаляється зі списку. Якщо користувач не передав функцію key у
remove-each-nth-fn , тоді зі списку видаляється просто кожен n -ний елемент.
```lisp
CL-USER> (remove-if (remove-each-nth-fn 2) '(1 2 3 4 5))
(1 3 5)
CL-USER> (remove-if (remove-each-nth-fn 2 :key #'evenp) '(1 2 2 2 3 4 4 4 5))
(1 2 2 3 4 5)
```

## Лістинг реалізації другої частини завдання
```lisp
(defun remove-each-nth-fn (n &key key)
  (let ((count 0))  
    (lambda (element)
      (incf count)  
      (if key
          (and (zerop (mod count n)) (funcall key element))  
          (zerop (mod count n))))))
```
### Тестові набори та утиліти другої частини 
```lisp
 (defun check-remove-each-nth (test-name n input expected &key key)
  (let* ((predicate (remove-each-nth-fn n :key key))
         (result (remove-if predicate input)))
    (if (equal result expected)
        (format t "~a passed. Result: ~a~%" test-name result)
        (format t "~a failed.~%  Input: ~a~%  Expected: ~a~%  Got: ~a~%"
                test-name input expected result))))

(defun test-remove-each-nth-fn ()
  (format t "Start testing remove-each-nth-fn function~%")
  (check-remove-each-nth "Test 1" 2 '(1 2 3 4 5) '(1 3 5))
  (check-remove-each-nth "Test 2" 3 '(1 2 3 4 5 6 7 8 9) '(1 2 4 5 7 8))
  (check-remove-each-nth "Test 3" 1 '(1 2 3) '())
  (check-remove-each-nth "Test 4" 2 '(1 2 2 2 3 4 4 4 5) '(1 2 3 4 5) :key #'evenp)
  (check-remove-each-nth "Test 5" 3 '((1 . "a") (2 . "b") (3 . "c") (4 . "d") (5 . "e"))
                         '((1 . "a") (2 . "b") (4 . "d") (5 . "e"))
                         :key #'car)
  (check-remove-each-nth "Test 6" 2 '(10 20 30 40 50) '(10 30 40 50) :key (lambda (x) (< x 35)))
  (format t "Testing completed~%"))
```
### Тестування другої частини 
```lisp
(test-remove-each-nth-fn)
Start testing remove-each-nth-fn function
Test 1 passed. Result: (1 3 5)
Test 2 passed. Result: (1 2 4 5 7 8)
Test 3 passed. Result: NIL
Test 4 passed. Result: (1 2 3 4 5)
Test 5 passed. Result: ((1 . a) (2 . b) (4 . d) (5 . e))
Test 6 passed. Result: (10 30 40 50)
Testing completed
```
