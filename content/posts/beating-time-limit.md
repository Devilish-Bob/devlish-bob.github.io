---
title: "Beating the Time Limit"
date: 2023-01-25T01:30:50Z
description: "Predicting if your competitive programming solution will run under the time limit"
type: "post"
tags: ["competitive programming", "c++", "python", "algorithms", "tips", "performance"]
---


```cpp
#pragma GCC optimize("O3,unroll-loops")
#pragma GCC target("avx2,bmi,bmi2,lzcnt,popcnt")
#include <bits/stdc++.h>
using namespace std;

int main() {
	int N = 1e5;
	long long sum = 0;
	for (int i = 0; i < N; ++i) {
		for (int j = 0; j < N; ++j) {
			sum += i^j;
		}
	}
	cout << sum;
}
```

Hey you! We have here an O(N^2) algorithm with N = 10^5, doing a ridiculous number of operations. How long do you think it takes for this code to run on my laptop?

What do you think? A minute? Two minutes?

Well, I compiled and [hyperfined](https://github.com/sharkdp/hyperfine) it and here are the results:
```
Benchmark 1: ./main
  Time (mean ± σ):      1.033 s ±  0.189 s    [User: 1.029 s, System: 0.002 s]
  Range (min … max):    0.910 s …  1.355 s    10 runs
```

That's right, it takes *around a second* to run.

If you've ever done any competitive programming, you're probably going "Wha????" right now.

A couple of days, I saw some competitive programming folks discussing the maximum N for which you can cram an O(N^2) solution under the time limit, typically 2 seconds. Is it 5000? 2000? USACO Guide says it's 7500?

100000?

The answer, of course, is it depends. It depends on a lot of things. What programming language are you using? What's the constant factor of your solution? Are you reading a blog post that's intentionally trying to trick you?

This particular code has an insanely low constant factor. Why? Because the compiler is smart, or at least because we told it to be smart. Those weird lines at the very top tell the compiler to grind hard for its best optimizations, [unroll loops](https://en.wikipedia.org/wiki/Loop_unrolling), and use AVX2 instructions.

AVX2 is the insane black magic here. They're a special set of overpowered instructions on your CPU that lets you do an operation on *8 values at the same time* (with many caveats). Without those two lines of special sauce, this program instead takes 30 seconds to run on my laptop.

However, for other problems, like those annoying dynamic programming problems with tons of states, your O(N^2) C++ solution might only work for N at most 2000, and maybe even lower for other languages.

A good rule of thumb is to count the number of basic operations, like additions, assignments, and loop iterations, that your solution is doing, and check if that's under 2*10^8 for C++. Java? 10^8. Python? Ugh, competitive programming is one of those things where Python becomes your worst enemy. You might not even be able to do 10^6 operations with Python.

Of course, these are all just tips for estimating your runtime. The only way to know for sure is to submit it and see what happens. Still, there might not always be time in a contest to spend an hour implementing a borderline slow solution just to watch it get time limit exceeded for half the test cases. Keep in mind though that contest writers usually don't want their problems to boil down to tedious constant factor optimization. In USACO, the best strategy is probably to spend a bit more time thinking of better solutions, and then if you're still stuck, just implement the borderline slow solution to get as many points as you can. For Codeforces, where the scoring is all or nothing, it might not be worth it spending the time at all implementing something that probably won't pass every test case.

If you thought that was fun, check out this [performance estimation game](https://computers-are-fast.github.io/)!

