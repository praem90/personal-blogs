#Memory exausted with Recursion
#Memory leak with Recursion

## Recursice vs Do..While

I finally got an real world use case to use the Do..While and I understand the power of Do..While.

## The problem with the Recursion
    Recursions are great. Especially when we study DSA or solving Leet Code problems.

    It is great if you work with the an object mostly (self) or when you traverse an existing data.
    When the recursive is creating heavy new/temp data then you will endup memory leak or limit exception.

    Because the variables which are created inside the recursive method would not get cleared from the memory
    until the recursion completes.

    Example:
        Consider that you are about to0 call an api in a recursive method and it might get called at least 10 times.
        Each time the API returns 1MB of data.
        So at the end of the recursive method, the memory usage would be 10*1MB = 10MB.

    ```php
    protected function getData(int $page_no = 1) {
        $data = Http::get('users', ['page' => $page_no]);

        // Process the data

        if ($data['hasMore']) {
            return $this->getDate($page_no+1);
        }
    }

    ```
    In the above code on each time a new data variable is created and memory allocated. It will be freed up on the 11th call or when we get the hasMore falsy.

## The Solution
    Here the Do..While is the saviour. Unlike recursion, Do.While can override the existing variable if any of then exist already.
    So that it wont allocate new memory each time.

    So for the above example, on each call the allocated momory for the variable data has been cleared and then created again and allocate a new memory.
    Lets rewrite the abolve example like this,

    ```php
        protected function getData(int $page_no = 1) {
            do {
                $data = Http::get('users', ['page' => $page_no]);
                // Process the data
            } while ($data['hasMore']);
        }
    ```

## Conclusion
    Yes, we can use recursive in any situation and you can clear/free the data allocated using `unset` function too.
    But I feel this is one of the use cases for Do..While, more readable and suitable.

