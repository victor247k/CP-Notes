# Disjoint Set Union

 [cp-algorithms](https://cp-algorithms.com/data_structures/disjoint_set_union.html)

### Operations:
- Creation ```make_set()```
-  Find ```find_set()```
-  Union ```union_sets()```

_O(1)_ Time Complexity

### Naive  Implementation:
```cpp
void make_set(int v) {
	parent[v] = v;
}

int find_set(int v) {
	if (v == parent[v])
		return v;
	return find_set(parent[v]);
}

void union_sets(int a, int b) {
	a = find_set(a);
	b = find_set(b);
	if (a != b)
		parent[b] = a;
}
```
- ```find_set()``` is in _O(n)_

### Optimizations
##### Path compression
```cpp
int find_set(int v) {
    if (v == parent[v])
        return v;
    return parent[v] = find_set(parent[v]);
}
```
- From _O(n)_ -> _O(log n)_.

#### Union by size/rank

###### Union by size:
```cpp
void make_set(int v) {
    parent[v] = v;
    size[v] = 1;
}

void union_sets(int a, int b) {
    a = find_set(a);
    b = find_set(b);
    if (a != b) {
        if (size[a] < size[b])
            swap(a, b);
        parent[b] = a;
        size[a] += size[b];
    }
}
```
###### Union by rank:
```cpp
void make_set(int v) {
    parent[v] = v;
    rank[v] = 0;
}

void union_sets(int a, int b) {
    a = find_set(a);
    b = find_set(b);
    if (a != b) {
        if (rank[a] < rank[b])
            swap(a, b);
        parent[b] = a;
        if (rank[a] == rank[b])
            rank[a]++;
    }
}
```

- Both optimizations are equal.

### Time Complexity
- If we combine all optimizations the time complexity is _O(α(n))_, where _α(n)_ is the inverse Ackermann function (its very fast).

### Applications
...

### Problems provided by the site
...

### Extra Problems

<hr style="height: 2px; width: 100%; background-color: white;">
##### Learning Languages
- Tags:
>[[DSU]], [[DFS and Similar]]

- Statement
```txt
The "BerCorp" company has got _n_ employees. These employees can use _m_ approved official languages for the formal correspondence. The languages are numbered with integers from 1 to _m_. For each employee we have the list of languages, which he knows. This list could be empty, i. e. an employee may know no official languages. But the employees are willing to learn any number of official languages, as long as the company pays their lessons. A study course in one language for one employee costs 1 berdollar.

Find the minimum sum of money the company needs to spend so as any employee could correspond to any other one (their correspondence can be indirect, i. e. other employees can help out translating).

Input

The first line contains two integers _n_ and _m_ (2 ≤ _n_, _m_ ≤ 100) — the number of employees and the number of languages.

Then _n_ lines follow — each employee's language list. At the beginning of the _i_-th line is integer _k__i_ (0 ≤ _k__i_ ≤ _m_) — the number of languages the _i_-th employee knows. Next, the _i_-th line contains _k__i_ integers — _a__ij_ (1 ≤ _a__ij_ ≤ _m_) — the identifiers of languages the _i_-th employee knows. It is guaranteed that all the identifiers in one list are distinct. Note that an employee may know zero languages.

The numbers in the lines are separated by single spaces.

Output

Print a single integer — the minimum amount of money to pay so that in the end every employee could write a letter to every other one (other employees can help out translating).

Examples

Input
5 5  
1 2  
2 2 3  
2 3 4  
2 4 5  
1 5  

Output
0  

Input
8 7  
0  
3 1 2 3  
1 1  
2 5 4  
2 6 7  
1 3  
2 7 4  
1 1  

Output
2  

Input
2 2  
1 2  
0  

Output
1
```

- Solution
```cpp
#include <bits/stdc++.h>
#include <unordered_set>
#include <vector>
using namespace std;

// dsu
// make_set
// find_set
// union_set

void make_set(int v, vector<int> &parent, vector<int> &size) {
  if (parent[v] == -1) {
    parent[v] = v;
    size[v] = 1;
  }
}

int find_set(int v, vector<int> &parent) {
  if (v == parent[v])
    return v;
  return parent[v] = find_set(parent[v], parent);
}

void union_sets(int a, int b, vector<int> &parent, vector<int> &size) {
  a = find_set(a, parent);
  b = find_set(b, parent);
  if (a != b) {
    if (size[a] < size[b])
      swap(a, b);
    parent[b] = a;
    size[a] += size[b];
  }
}

int main() {
  int n, m;
  cin >> n >> m;
  vector<int> parent(m + 1, -1);
  vector<int> size(m + 1, 0);

  int delta = 0;

  while (n--) {
    int k;
    cin >> k;
    if (k == 0) {
      delta++;
      continue;
    }
    vector<int> a(k);
    for (int &e : a)
      cin >> e;
    for (int e : a)
      make_set(e, parent, size);
    for (int i = 0; i < k - 1; i++)
      union_sets(a[i], a[i + 1], parent, size);
  }

  unordered_set<int> parents;
  for (int i = 1; i <= m; i++) {
    if (parent[i] != -1)
      parents.insert(find_set(parent[i], parent));
  }
  int result = parents.size() - 1;
  cout << max(result, 0) + delta << endl;
}
```

- Notes
> First of all implement the DSU structure and the helper functions: make_set(), find_set(), union_sets(). After that in the main function get the size of the vector of languages and initialize the vector with the languages, and a vector of the size, that will help optimize the solution _[[DSU#Optimizations]]_. Than in the while loop get all of the input, when a person doesn't know any languages we skip and add +1 to _delta_, otherwise and create a new set for each language that person knows and union them with each other. After we've processed all of the input its time to count the number of unions. We do that by counting the number of unique parents or leaders using a set.

<hr style="height: 2px; width: 100%; background-color: white;">


##### XOUR
- Tags:
> [[Dara Structures]], [[DSU]], [[Sorting]]
- Statement
```txt
You are given an array a consisting of n nonnegative integers.

You can swap the elements at positions i and j if ai XOR aj<4, where XOR is the [bitwise XOR operation](https://en.wikipedia.org/wiki/Bitwise_operation#XOR).

Find the lexicographically smallest array that can be made with any number of swaps.

An array x is lexicographically smaller than an array y if in the first position where x and y differ, xi<yi

Input

The first line contains a single integer t (1≤t≤10^4) — the number of test cases.

The first line of each test case contains a single integer n (1≤n≤2⋅10^5) — the length of the array. 

The second line of each test case contains n integers ai (0≤ai≤10^9) — the elements of the array.

It is guaranteed that the sum of n over all test cases does not exceed 2⋅10^5

Output

For each test case, output n integers — the lexicographically smallest array that can be made with any number of swaps.

Example

Input
4
4
1 0 3 2
5
2 7 1 5 6
8
1 2 1 2 1 2 1 2
4
16 4 1 64

Output
0 1 2 3 
1 5 2 6 7 
1 1 1 1 2 2 2 2 
16 4 1 64
```
- Solution
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    map<int, priority_queue<int>> groups;
    for (int &e : a) {
        cin >> e;
        groups[e>>2].push(-e);
    }

    for (int e : a) {
        int key = e>>2;
        cout << -groups[key].top() << ' ';
        groups[key].pop();
    }
    cout << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    // cout << (a ^ b) << endl; {0 1 2 3}, {4 5 6 7}, {8 9 10 11}, ...
    // multiples of 4   ->       0=(4 * 0)  4=(4 * 1)  8=(4 * 2)
    // floor(n / 4)

    int t = 1;
    cin >> t;
    while (t--)
        solve();

    return 0;
}
```
- Notes
> So basically from the statement I got that we can group elements based on n / 4, so for example `{0, 1, 2 , 3}` for all of those `n / 4 == 0`. The hardest part about this problem was coming out with a way to sort and print the numbers. Like mentioned above we grouped them bases on `n / 4` in an `map<int>` it's important to use a `map` instead of `unordered_map` since if we use the latter, our solution will be too slow to pass. Also it's important to note that we use this trick where we push negative numbers and then print the negative of that negative number, this is used to print the smallest numbers first, since `priority_queue` use the `greater` comparator by default, but we can change that in the declaration and not use this trick.but we can change that in the declaration and not use this trick.but we can change that in the declaration and not use this trick.but we can change that in the declaration and not use this trick. 
> `map` is used for: when you need ordered data, or you have to print access the data, or you need predecessor/successor of elements, etc. 
> `unordered_map` is used for when you need to keep count of some data (and no ordering is required), you need single element access, i.e. no traversal
>  `n >> 2 == floor(n / 4)` - these 2 are the same

<hr style="height: 2px; width: 100%; background-color: white;">

##### News Distribution
- Tags:
> [[DFS and Similar]], [[DSU]], [[Graphs]]
- Statement
```txt
In some social network, there are n users communicating with each other in m groups of friends. Let's analyze the process of distributing some news between users.

Initially, some user x receives the news from some source. Then he or she sends the news to his or her friends (two users are friends if there is at least one group such that both of them belong to this group). Friends continue sending the news to their friends, and so on. The process ends when there is no pair of friends such that one of them knows the news, and another one doesn't know.

For each user x you have to determine what is the number of users that will know the news if initially only user x starts distributing it.

Input

The first line contains two integers n and m (1≤n,m≤5⋅10^5) — the number of users and the number of groups of friends, respectively.

Then m lines follow, each describing a group of friends. The i-th line begins with integer ki (0≤ki≤n) — the number of users in the i-th group. Then ki distinct integers follow, denoting the users belonging to the i-th group.

It is guaranteed that ∑i=1mki≤5⋅10^5

Output

Print n integers. The i-th integer should be equal to the number of users that will know the news if user i starts distributing it.

Example

Input
7 5
3 2 5 4
0
2 1 2
1 1
2 6 7

Output
4 4 1 4 4 2 2
```
- Solution
```cpp
#include <bits/stdc++.h>
using namespace std;

void make_set(int v, vector<int> & parent, vector<int> & size) {
    parent[v] = v;
    size[v] = 1;
}

int find_set(int v, vector<int> & parent, vector<int> & size) {
    if (parent[v] == v)
        return v;
    return parent[v] = find_set(parent[v], parent, size);
}

void union_sets(int a, int b, vector<int> &parent, vector<int> &size) {
    a = find_set(a, parent, size);
    b = find_set(b, parent, size);
    if (a != b) {
        if (size[a] < size[b])
            swap(a, b);
        parent[b] = a;
        size[a] += size[b];
    }
}


int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m;
    vector<int> parent(n+1);
    vector<int> size(n+1);

    for (int i = 1; i <= n; i++)
        make_set(i, parent, size);

    while (m--) {
        int k;
        cin >> k;
        vector<int> a(k);
        for (int &e : a)
            cin >> e;
        for (int i = 0; i < k-1; i++)
            union_sets(a[i], a[i+1], parent, size);
    }

    for (int i = 1; i <= n; i++) {
        cout << size[find_set(i, parent, size)] << ' ';
    }
    cout << endl;

    return 0;
}
```
- Notes
> Standard [[DSU]] problem, however to get the size of the union, we go to the parent and check it's size. The functions are really helpful, I think I understood how to implement them on my own.