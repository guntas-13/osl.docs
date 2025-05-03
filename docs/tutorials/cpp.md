---
icon: material/language-cpp
---

# Array Declaration

```py
def i32 a(i32[][] b, i32 c){
    var #i32 a := 3;
    while (a < b)
    {
        log b;
        b := b - 4;
    }
}

const i32[2][3] arr := {{2, 3, 4}, {5, 6, 7}};
var i8 x := 3;
var i8 y := 4;
var i32[x][y] brr := a();
```

# Functions

## Example 1

```py
def i32 a(i32 b, i32 c, fn(i32[][], f64)->nav f){
    var i32 v := 3;
}

var fn(i32, i32, fn(i32[][], f64)->nav)->i32 b := a;
```

## Example 2

```py
def i32 F(i32 b, i32 c, u8[][] bts, fn(i32)->nav p, fn(u8[][], fn(i32)->nav)->c8 Q)
{
    var c8 chr := Q(bts, p);
    var c8 chr1 := bts[b][c];
    p();
    var i32 a := b + c + chr;
    return a;
}
```

# Function Calls

## Example 1

```py
def i32 a(i32 b, i32 c){
    var i32 a := 3;
}

a();
a()();
```

## Example 2

```py
def i32 F(i32 b, i32 c, u8[][] bts, fn(i32)->nav p, fn(u8[][], fn(i32)->nav)->c8 Q)
{
    var c8 chr := Q(bts, p);
    var c8 chr1 := bts[b][c];
    p();
    var i32 a := b + c + chr;
    return a;
}

var i32 a := 5;

def nav A(i32 x)
{
    a := x;
}

def c8 B(u8[][] bt, fn(i32)->nav g)
{
    g(a);
    var c8 x := 'F' + bt[1][1];
    return x;
}

var fn(i32, i32, u8[][], fn(i32)->nav, fn(u8[][], fn(i32)->nav)->c8)->i32 G := F;
var i32 gt := G(54, 54, {{5, 254},{24, 6}}, A, B);
```

# If-Else

## Example 1

```py
var i32 a := 98;
var i32 b := 78;
if a = 98 {
    log a;
}
else {
    log b;
}
```

# Pointers

## Example 1

```c
var u32 x := 47680678;
var #u32 ptr := #x;
const c8 k := 'a';
var #u32 y := #x;
var ##u32 ptrr := #y;
var u32 copy := @(@ptrr);
```

# Pointers

## Example 1

```py
var i32 a := 4;
```

## Example 2

```py
var i64 m := 54;
var bool x;
var i32 n := x + m - x;
```

# While Loop

## Example 1

```py
def i32 a(i32 b, i32 c){
    var i32 a := 3;
    while a<b {
        log b;
        b := b - 4;
    }
}
```

# Euler Problems

## Q1

```py
def i32 F(i32 x, i32 s) {
    while x < 1000
    {
        if ((x % 3) = 0) | ((x % 5) = 0)
        {
            s := s + x;
        }
        x := x + 1;
    }
    return s;
}
log F(0, 0);
```

```bash
233168
```

## Q2

```py
def i32 fib(i32 a, i32 b, i32 s) {
    while a < 4000000
    {
        if (a % 2) = 0
        {
            s := s + a;
        }
        var i32 temp := a;
        a := b;
        b := temp + b;
    }
    return s;
}
log fib(0, 1, 0);
```

```bash
4613732
```

## Q3

```py
def i64 prime(i64 n, i64 i)
{
    while (i * i) <= n
    {
        if (n % i) = 0
        {
            n := n / i;
        }
        else
        {
            i := i + 1;
        }
    }
    return n;
}
var i64 n := 600851475143;
log prime(n, 2);
```

```bash
6857
```

## Q4

```py
def bool isPal(i32 n)
{
    var i32 rev := 0;
    var i32 nn := n;
    while n > 0
    {
        rev := (rev * 10) + (n % 10);
        n := n / 10;
    }
    return rev=nn;
}

var i32 maxPal := 0;
var i32 i := 999;
var i32 j;
var i32 prod;
while i >= 1
{
    j := i;
    while j >= 1
    {
        prod := i * j;
        if (prod > maxPal) & (isPal(prod))
        {
            maxPal := prod;
        }
        j := j - 1;
    }
    i := i - 1;
}
log maxPal;
```

```bash
906609
```

## Q5

```py
def i32 gcd(i32 a, i32 b)
{
    var i32 temp;
    while b ~= 0
    {
        temp := b;
        b := a % b;
        a := temp;
    }
    return a;
}
def i32 lcm(i32 a, i32 b)
{
    return (a * b) / (gcd(a, b));
}
def i32 F(i32 n, i32 i) {
    while i > 1
    {
        n := lcm(n, i - 1);
        i := i - 1;
    }
    return n;
}
log F(1, 20);
```

```bash
232792560
```

## Q6

```py
def i32 F(i32 n, i32 sum, i64 sumSq)
{
    while n > 0
    {
        sum := sum + n;
        sumSq := sumSq + (n * n);
        n := n - 1;
    }
    return (sum * sum) - sumSq;
}
log F(100, 0, 0);
```

```bash
25164150
```

## Q7

```py
def i32 find_nth_prime(i32 n)
{
    var i32 limit := 150000;
    var bool[limit + 1] is_prime;

    var i32 i := 0;
    var i32 j;

    while i < (limit + 1)
    {
        is_prime[i] := true;
        i := i + 1;
    }

    is_prime[0] := false;
    is_prime[1] := false;

    i := 2;
    while (i * i) <= limit
    {
        if is_prime[i]
        {
            j := i * i;
            while j <= limit
            {
                is_prime[j] := false;
                j := j + i;
            }
        }
        i := i + 1;
    }

    var i32 count := 0;
    var i32 num := 2;
    while num <= limit
    {
        if is_prime[num]
        {
            count := count + 1;
            if count = n
            {
                return num;
            }
        }
        num := num + 1;
    }
    return -1;
}

var i32 n := 10001;
log find_nth_prime(n);
```

```bash
104743
```

## Q9

```py
def i32 find_pythagorean_triplet()
{
    var i32 a := 1;
    var i32 denom;
    var i32 b;
    var i32 c;
    while a < 333
    {
        denom := 1000 - a;
        if (500000 % denom) = 0
        {
            b := 1000 - (500000/denom);
            if a < b
            {
                c := (1000 - a) - b;
                if (b < c) & (((a * a) + (b * b)) = (c * c))
                {
                    return a * b * c;
                }

            }
        }
        a := a + 1;
    }
    return -1;
}
log find_pythagorean_triplet();
```

```bash
31875000
```

## Q10

```py
def i64 find_nth_prime()
{
    var i32 limit := 2000000;
    var bool[limit] is_prime;

    var i32 i := 0;
    var i32 j;

    while i < limit
    {
        is_prime[i] := true;
        i := i + 1;
    }

    is_prime[0] := false;
    is_prime[1] := false;

    i := 2;
    while (i * i) < limit
    {
        if is_prime[i]
        {
            j := i * i;
            while j < limit
            {
                is_prime[j] := false;
                j := j + i;
            }
        }
        i := i + 1;
    }

    var i64 sum := 0;
    var i32 num := 2;
    while num < limit
    {
        if is_prime[num]
        {
            sum := sum + num;
        }
        num := num + 1;
    }
    return sum;
}

log find_nth_prime();
```

```bash
142913828922
```
