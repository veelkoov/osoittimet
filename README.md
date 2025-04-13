Memory is a block of bytes, numbered from zero. When a program starts, we have no guarantees what the memory contains (uninitialized garbage). We also cannot predict which memory addresses will be allocated for the program. Typically, when working with pointers, you will be seeing stuff similar to this example pointer value: `0x7fff06ba29d4`. For simplifications, we will pretend our program is working in the initial block of memory.

    | Address  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|-----|
    | Value    | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ?  | ?  | ?  | ?  | ?  | ?  | ... |

---

When you declare a variable, it will be placed somewhere in the memory (we cannot predict where). Assuming we declare the following:

```c++
int a = 20;
```

our memory could look like this:

    | Address  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|-----|
    | Value    | ? | ? | ? | ? |      20       | ? | ? | ?  | ?  | ?  | ?  | ?  | ?  | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|-----|
    | Variable |   |   |   |   |       a       |   |   |    |    |    |    |    |    | ... |

As seen above `a` occupies 4 bytes starting from byte 4 to byte 7. How can we tell `int` uses 4 bytes? Well,

```c++
std::cout << sizeof(int) << std::endl;
```

outputs: `4`. Out of curiosity, let's check how much bytes take `long int` and `byte`:

```c++
std::cout << sizeof(long int) << std::endl;
std::cout << sizeof(char) << std::endl;
```

The above outputs `8` and `1`.

---

A pointer is a specific variable, storing a number - address in the memory. You declare pointers like this:

```c++
int* a;
```

The `a` above should be used to work with an `int` variable. Let's check how much memory this `a` pointer variable takes:

```c++
std::cout << sizeof(int*) << std::endl;
```

The above outputs `8`. Out of curiosity, let's see how much bytes are used by `long int*` and `byte*`:

```c++
std::cout << sizeof(long int*) << std::endl;
std::cout << sizeof(char*) << std::endl;
```

The above outputs `8` and `8` as well. That's because size of the address in the memory does not depend on how big the thing pointed to is.

---

Let's now imagine the following code:

```c++
int a = 20;
int* p;
```

This is how the memory could look like:

    | Address  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|-----|
    | Value    | ? | ? | ? | ? |      20       | ? | ? | ?  | ?  | ?  | ?  | ?  | ?  | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|-----|
    | Variable |   |   |   |   |       a       |                    p                | ... |

As can see above, we do not know the value of `p`, because we did not assign a value to it. So the initial state is: `p` points on some garbage somewhere. Operating systems and/or programs do not deal well with garbage pointers. At best, the program will crash when `p` on use attempt. At worst, it will produce invalid results.

Let's modify our code to look like this, and check the memory:

```c++
int a = 5;
int* p = &a;
```

    | Address  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|-----|
    | Value    | ? | ? | ? | ? |      20       |                    4                | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|-----|
    | Variable |   |   |   |   |       a       |                    p                | ... |
                                 ^----------------------------------'

Now the value of `p` is `4` which is the _address_ of `a`. `p` "points at" `a`.

Let's now check the following:

```c++
std::cout << a << std::endl;
std::cout << *p << std::endl;
std::cout << p << std::endl;
```

Example output:

```
20
20
4 // SIMPLIFICATION based on above example; in real-life scenarios expect something similar to 0x7fff06omgwtfbbq
```

To sum up:
- `a` is our "normal" `int` variable with the value of `20`
- `p` is a pointer. It is currently "pointing at" `a` - which means the value of `p` is the address of `a` in memory
- `*p` means we want "the value which is pointed at by `p`"

---

Let's use another example. When working with arrays, you typically use pointers. For simplification, we will omit the `delete` part (memory leak):

```c++
short int* p = new short int[5]{1, 2, 3, 4, 5}; // sizeof(short int) == 2
```

This is how our memory could look like:

    | Address  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|----|----|-----|
    | Value    |               8               |   1   |    2    |    3    |    4    |    5    | ... |
    |----------|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|----|----|-----|
    | Variable |               p               |                                               | ... |
                               '-----------------^

Now, we have `p` pointer in memory, pointing to some region in the memory, consisting of 5 `short int`s, total length of 10 bytes. There is no variable which has the value; `p` is the only thing we have to refer to. Having the pointer we can do the following:

```c++
std::cout << a[0] << std::endl;     // Outputs `1`
std::cout << a[1] << std::endl;     // Outputs `2`
std::cout << a << std::endl;        // Outputs `8` (SIMPLIFICATION, remember 0x...)
std::cout << a + 1 << std::endl;    // Outputs `10` (SIMPLIFICATION, remember 0x...)
std::cout << *a << std::endl;       // Outputs `1`
std::cout << *(a + 1) << std::endl; // Outputs `2`
```

What requires explanation is the `a + 1` part, and its result - 10 instead of 9. This is because the compiler understands that pointer of type `short int` points at element(s) having `sizeof(short int)`, so it will increase the result by `1` times the `sizeof`.

---

Let's imagine this weird scenario (it's also awful due to the lact of `delete`s and destructors):

```c++
#include <iostream>

class Inside {
public:
    int theVariable = 20;
};

class Middle {
public:
    Inside* inside;
    Middle() { inside = new Inside(); }
};

class Outside {
public:
    Middle* middle;
    Outside() { middle = new Middle(); }
};

int main() {
    Outside* outside = new Outside();

    std::cout << (*(*(*outside).middle).inside).theVariable << std::endl;

    return 0;
}
```

The example obviously outputs `20`. Doesn't the `std::cout` line look amazing? That's why we can do this instead:

```c++
std::cout << outside->middle->inside->theVariable << std::endl;
```

`p->X` literally translates to "use the `X` of the object/structure referenced by `p`".
