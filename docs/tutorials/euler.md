---
icon: material/math-compass
---

# Project Euler Problems solved in osl!

Project Euler is a well known set of questions, available here: [Link to the problemset](https://projecteuler.net/archives).

## Q1

```py
def F(x, s) {
    while (x < 1000) {
        if (x % 3 = 0 || x % 5 = 0) {
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
def fib(a, b, s) {
    while (a < 4000000) {
        if (a % 2 = 0) {
            s := s + a;
        }
        var temp := a;
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
def prime(n, i) {
    while (i * i <= n) {
        if (n % i = 0) {
            n := n / i;
        } else {
            i := i + 1;
        }
    }
    return n;
}
var n := 600851475143;
log prime(n, 2);
```

```bash
6857
```

## Q4

```py
def isPal(n) {
    var rev := 0;
    var nn := n;
    while (n > 0)
    {
        rev := rev * 10 + n % 10;
        n := n / 10;
    }
    return rev=nn;
}

var maxPal := 0;
var i := 999;
var j;
var prod;
while (i >= 1) {
    j := i;
    while (j >= 1) {
        prod := i * j;
        if ((prod > maxPal) && (isPal(prod))) {
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
def gcd(a, b) {
    while (b != 0) {
        var temp := b;
        b := a % b;
        a := temp;
    }
    return a;
}
def lcm(a, b) {
    return a * b / gcd(a, b);
}
def F(n, i) {
    while (i > 1) {
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
def F(n, sum, sumSq) {
    while (n > 0) {
        sum := sum + n;
        sumSq := sumSq + n * n;
        n := n - 1;
    }
    return sum * sum - sumSq;
}
log F(100, 0, 0);
```

```bash
25164150
```

## Q7

```py
def find_nth_prime(n)
{
    var limit := 150000;
    var is_prime[limit + 1];

    var i := 0;
    var j;

    while (i < limit + 1)
    {
        is_prime[i] := 1;
        i := i + 1;
    }

    is_prime[0] := 0;
    is_prime[1] := 0;

    i := 2;
    while (i * i <= limit)
    {
        if (is_prime[i])
        {
            j := i * i;
            while (j <= limit)
            {
                is_prime[j] := 0;
                j := j + i;
            }
        }
        i := i + 1;
    }

    var count := 0;
    var num := 2;
    while (num <= limit)
    {
        if (is_prime[num])
        {
            count := count + 1;
            if (count = n) return num;
        }
        num := num + 1;
    }
    return -1;
}

var n := 10001;
log find_nth_prime(n);
```

```bash
104743
```

## Q9

```py
def find_pythagorean_triplet()
{
    var a := 1;
    var denom;
    var b;
    var c;
    while (a < 333)
    {
        denom := 1000 - a;
        if ((500000 % denom) = 0)
        {
            b := 1000 - (500000/denom);
            if (a < b)
            {
                c := 1000 - a - b;
                if ((b < c) && ((a * a) + (b * b) = (c * c)))
                    return a * b * c;
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
def find_nth_prime()
{
    var limit := 2000000;
    var is_prime[limit];

    var i := 0;
    var j;

    while (i < limit)
    {
        is_prime[i] := 1;
        i := i + 1;
    }

    is_prime[0] := 0;
    is_prime[1] := 0;

    i := 2;
    while (i * i < limit)
    {
        if (is_prime[i])
        {
            j := i * i;
            while (j < limit)
            {
                is_prime[j] := 0;
                j := j + i;
            }
        }
        i := i + 1;
    }

    var sum := 0;
    var num := 2;
    while (num < limit)
    {
        if (is_prime[num]) sum := sum + num;
        num := num + 1;
    }
    return sum;
}

log find_nth_prime();
```

```bash
142913828922
```
