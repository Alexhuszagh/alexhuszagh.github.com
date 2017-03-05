---
title:  "Measuring Boost.MultiIndex Performance"
date:   2017-03-05 0:00:00
categories:
 - c++
tags:
 - c++
 - performance
 - boost
---

Although I generally view most of Boost as bloatware that suffers from feature-creep, Boost MultiIndex provides a simple, STL-like container with different views, allowing fast data acess with multiple indexers.

Although the Boost [documentation][multiindex-performance] claims dramatic performance improvements for the multi_index container, relative to manually-maintaining multiple views, the tests were run with archaic compilers (GCC 3.4.5) and operating systems (Windows 2000). Inspired by this [post][szborows], I decided to benchmark MultiIndex's performance using Boost's [performance tests][multiindex-perf].

# Methods

All tests were run with the following parameters, for GCC 6.3, Clang 3.8, and Fedora MinGW.

| Parameter    | Value              | 
|:------------:|:------------------:|
| CPU          | i7-6560U, 2.20GHz  |
| OS           | Fedora 25          |
| RAM          | 16 GB              |
| Flags        | -O3                |
| Wine         | Wine-2.2 (Staging) |

# Results

**1 Ordered Index**
![1 Ordered Index][1-ordered]

**2 Ordered Indexes**
![2 Ordered Index][2-ordered]

# Conclusion

[multiindex-performance]:   http://www.boost.org/doc/libs/1_50_0/libs/multi_index/doc/performance.html#tests
[szborows]:                 https://szborows.blogspot.com/2015/04/real-boostmultiindex-performance.html
[multiindex-perf]:          http://www.boost.org/doc/libs/1_50_0/libs/multi_index/perf/test_perf.cpp
[1-ordered-1-sequenced]:    {{ site.url }}/attachments/2017-03-05-1_ordered_1_sequenced.png
[2-ordered-1-sequenced]:    {{ site.url }}/attachments/2017-03-05-2_ordered_1_sequenced.png
[1-ordered]:                {{ site.url }}/attachments/2017-03-05-1_ordered_index.png
[2-ordered]:                {{ site.url }}/attachments/2017-03-05-2_ordered_indices.png
[3-ordered]:                {{ site.url }}/attachments/2017-03-05-3_ordered.png
[1-sequenced]:              {{ site.url }}/attachments/2017-03-05-1_sequenced_index.png
