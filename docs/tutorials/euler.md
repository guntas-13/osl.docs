# Project Euler Problems solved in OSL!

Project Euler is a well known set of questions, available here: [Link to the problemset](https://projecteuler.net/archives).

## Q1

```py
letFunc F(x, s) {
    if (x = 1000) return s;
    if (x % 3 = 0 || x % 5 = 0) 
        return F(x + 1, s + x);
    return F(x + 1, s);
}
F(0, 0);
```

## Q2

```py
letFunc fib(a, b, s) {
    if (a >= 4000000) return s;
    if (a % 2 = 0) 
        return fib(b, a + b, s + a);
    return fib(b, a + b, s);
}
fib(0, 1, 0);
```

## Q3
```py
letFunc prime(n, i) {
    if (i * i > n) return n;
    if (n % i = 0)
        return prime(n / i, i);
    return prime(n, i + 1);
}
var n := 600851475143;
prime(n, 2);
```

## Q4

You might have to increase the ulimit of your machine to run this (should be resolved once bytecode is done).

```py
letFunc isPal(n, rev, org) {
    if (n = 0) return rev = org;
    return isPal(n/10, rev*10 + n%10, org);
}
letFunc F(i, j, maxPal) {
    if (i < 100) return maxPal;
    if (j < 100) return F(i - 1, i - 1, maxPal);
    var prod := i * j;
    if ((prod > maxPal) && (isPal(prod, 0, prod)))
        maxPal := prod;
    return F(i, j - 1, maxPal);
}
F(999, 999, 0);
```