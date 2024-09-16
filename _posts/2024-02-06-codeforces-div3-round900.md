---
title: Codeforces Div 3 Round 900 Problem F
date: 2024-02-06
categories: [Algorithms, Codeforces]
tags: [Codeforces Div3 Round 900, Math, Proof, Number Theory]
description: Solution & proof
math: true
---

## Introduction

I highly suggest trying out the [Problem](https://codeforces.com/contest/1878/problem/F) first before reading the solution.

Concepts: Number Theory, GCD, Prime Factorization, Fundamental Theorem
of Arithmetic

## Key insight

The key insight here is the fact that:

$$\exists n, a \in \mathbb{Z}^+ \colon gcd(n, a) = 1 \land d(n\cdot a) = n \Longleftrightarrow d(n) \mid n$$

> i.e. There exist positive integers n and a, such that gcd(n, a) = 1 and d(n * a) = n, if and only if, d(n) divides n.
{: .prompt-info }

## Proof

Let's start by defining a property $P(x, a):$

$$P(x, a) \equiv GCD(x, a) = 1 \land d(x\cdot a) = x$$ 

$$x, a\in \mathbb{Z}^+$$

We are given $n$ such that $n \in \mathbb{Z}^+$

What $a$ should we pick to satisfy $P(n, a)$ or show that no such $a$ exists?

First, let us check how to pick $a$ such that $GCD(n, a) = 1$

The fundamental theorem of arithmetic states that each integer $x > 1$
can be represented uniquely as a product of prime numbers:

$$
\begin{equation}
\forall n \in \mathbb{Z}^+, x = p_{1}^{e_{1}} \cdot p_{2}^{e_{2}} \cdot p_{3}^{e_{3}} \cdot ...
\label{eq:1}
\end{equation}
$$

or equivalently: 

$$n = \prod_{i=1}^\infty p_ie^i$$ 

Where $p_i$ denotes the $i_{th}$ prime number.

With this in mind, we can say that:

$$x = p_{1}^{e_{1}} \cdot p_{2}^{e_{2}} \cdot p_{3}^{e_{3}} \cdot ...$$

$$y = p_{1}^{f_{1}} \cdot p_{2}^{f_{2}} \cdot p_{3}^{f_{3}} \cdot ...$$

$$GCD(x, y) = p_{1}^{min(e_{1}, f_{1})} \cdot p_{2}^{min(e_{2}, f_{2})} \cdot p_{3}^{min(e_{3}, f_{3})} \cdot ...$$

Let $P_n$ be the set of all prime factors of $n$. Given the definition
of GCD above, we can see that picking an $a$ such that
$P_a \cap P_n \neq \varnothing$ will give a $GCD(x,y) > 1$. So we must
pick an $a$ such that: $$\forall p \in P_a, p \not\in P_n$$ This will
guarantee that $GCD(n, a) = 1$

Now, how do we make sure that $d(n\cdot a) = n$ or that $a$ doesn't exist?

Again, by the fundamental theorem of arithmetic, we can define d(n) like this:

$$n = p_{1}^{e_{1}} \cdot p_{2}^{e_{2}} \cdot p_{3}^{e_{3}} \cdot ... \tag{by $\eqref{eq:1}$}$$ 

$$d(n) = max(1, e_{1}+1) \cdot max(1, e_{2}+1) \cdot max(1, e_{3}+1) \cdot ...$$

$$d(n)\in \mathbb{Z}^+$$

Since we must satisfy $GCD(n, a) = 1$, $a$ must not have any prime factor that is in $P_n$:

$$a = q_{1}^{f_{1}} \cdot q_{2}^{f_{2}} \cdot q_{3}^{f_{3}} \cdot ... \tag{by $\eqref{eq:1}$}$$

$$d(a) = max(1, f_{1}+1)\cdot max(1, f_{2}+1) \cdot max(1, f_{3}+1) \cdot ...$$ 

$$
\begin{equation}
d(n\cdot a) = d(n)\cdot d(a)
\label{eq:2}
\end{equation}
$$

Notice that we can always form any integers we want for $d(a)$ (as there are infinitely many prime numbers)

Finally, in order for $d(n \cdot a)$ to equal $n$:

$$ n = d(n) \cdot d(a) \tag{by $\eqref{eq:2}$}$$

$$ 
d(n) \mid n\tag*{$\blacksquare$}
$$

## Code
```c++
#include <iomanip>
#include <iostream>
#include <vector>
#include <string>
#include <cstring>
#include <algorithm>
#include <numeric>
#include <map>
#include <unordered_map>
#include <set>
#include <unordered_set>
#include <cmath>
#include <stack>
#include <queue>

using namespace std;

typedef long long ll;
typedef unsigned long long ull;

void get_pfs(int n, map<int, int>& pFactorByCount)
{
    if (n % 2 == 0 && pFactorByCount.find(2) == pFactorByCount.end())
        pFactorByCount[2] = 0;
   
    while (n % 2 == 0)
    {
        pFactorByCount[2]++;
        n = n/2;
    }
 
    for (int i = 3; i <= sqrt(n); i = i + 2)
    {
        if (pFactorByCount.find(i) == pFactorByCount.end())
                pFactorByCount[i] = 0;
        while (n % i == 0)
        {
            pFactorByCount[i]++;
            n = n/i;
        }
    }
   
    if (n > 2){
        if (pFactorByCount.find(n) == pFactorByCount.end())
            pFactorByCount[n] = 0;
        pFactorByCount[n]++;
    }
}


int countDivisors(map<int, int>& pFactorByCount) {
    int ans = 1;
    for (auto& kvp : pFactorByCount)
        ans *= (kvp.second + 1);
    return ans;
}

bool answer(map<int, int>& pFactorByCount){
    int cnt = countDivisors(pFactorByCount);
    for (auto kvp : pFactorByCount){
        if (cnt == 1) break;
        while (kvp.second--){
            if (cnt % kvp.first != 0){
                break;
            }
            cnt /= kvp.first;
            if (cnt == 1) break;
        }
    }
    return cnt == 1;
}

void solve(){
    int N, q;
    cin >> N >> q;
    ull n = N;
    map<int, int> pFactorByCountOrig;
    get_pfs(N, pFactorByCountOrig);
    map<int, int> pFactorByCount = pFactorByCountOrig;
    while (q--){
        int k;
        cin >> k;
        if (k == 1){
            int x;
            cin >> x;
            n *= x;
            get_pfs(x, pFactorByCount);
            if (answer(pFactorByCount)) cout << "YES\n";
            else cout << "NO\n";
        }
        else {
            n = N;
            pFactorByCount = pFactorByCountOrig;
        }
    }
}

int main(){
    ios_base::sync_with_stdio(false);cin.tie(0);cout.tie(0);
    //std::cout << std::setprecision(9); // use it for output that requires some precision
    int t=1;
    cin >> t;
    while (t--){
        solve();
    }
}
```