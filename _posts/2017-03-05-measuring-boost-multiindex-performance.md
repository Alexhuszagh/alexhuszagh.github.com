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

<table width="400">
  <colgroup>
    <col width="30%">
    <col width="70%">
  </colgroup>
  <thead>
    <tr>
      <th align="center"><span style="font-weight:bold">Parameter</span></th>
      <th align="center"><span style="font-weight:bold">Value</span></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th align="center">CPU</th>
      <th align="center">i7-6560U, 2.20GHz</th>
    </tr>
    <tr>
      <th align="center">OS</th>
      <th align="center">Fedora 25</th>
    </tr>
    <tr>
      <th align="center">RAM</th>
      <th align="center">16 GB</th>
    </tr>
    <tr>
      <th align="center">Flags</th>
      <th align="center">-O3</th>
    </tr>
    <tr>
      <th align="center">Wine</th>
      <th align="center">Wine-2.2 (Staging)</th>
    </tr>
  </tbody>
</table>

# Results

<table width="600">
  <colgroup>
    <col width="90%">
  </colgroup>
  <tbody>
    <tr>
      <td valign="top" align="center"><img src="{{ site.url }}/attachments/2017-03-05-1_ordered.png" alt="1-Ordered Index"></td>
    </tr>
    <tr>
      <td align="center">--------------------------------</td>
    </tr>
    <tr>
      <td valign="top" align="center"><img src="{{ site.url }}/attachments/2017-03-05-2_ordered_indices.png" alt="2-Ordered Indexes"></td>
    </tr>
    <tr>
      <td align="center">--------------------------------</td>
    </tr>
    <tr>
      <td valign="top" align="center"><img src="{{ site.url }}/attachments/2017-03-05-3_ordered.png" alt="3-Ordered Indexes"></td>
    </tr>
    <tr>
      <td align="center">--------------------------------</td>
    </tr>
    <tr>
      <td valign="top" align="center"><img src="{{ site.url }}/attachments/2017-03-05-1_sequenced_index.png" alt="1-Sequenced Index"></td>
    </tr>
    <tr>
      <td align="center">--------------------------------</td>
    </tr>
    <tr>
      <td valign="top" align="center"><img src="{{ site.url }}/attachments/2017-03-05-1_ordered_1_sequenced.png" alt="1-Ordered, 1-Sequenced Index"></td>
    </tr>
    <tr>
      <td align="center">--------------------------------</td>
    </tr>
    <tr>
      <td valign="top" align="center"><img src="{{ site.url }}/attachments/2017-03-05-2_ordered_1_sequenced.png" alt="2-Ordered, 1-Sequenced Indexes"></td>
    </tr>
  </tbody>
</table>

# Conclusion

In general, the hashmap implementations (sequenced) of Boost MultiIndex are superior to the STL containers, both in memory (data not shown) and lookup time. Similarly, using MinGW, the ordered MultiIndex containers outperform their STL counterparts for both memory and lookup speed. However, with Clang and GCC, although the MultiIndex containers use less memory than their ordered STL counterparts, they have worse lookup times, and this performance gap increases with container size. Overall, MultiIndex is an excellent drop-in replacement for unordered containers, but suffers severe performance defects for ordered indexes.

[multiindex-performance]:   http://www.boost.org/doc/libs/1_50_0/libs/multi_index/doc/performance.html#tests
[szborows]:                 https://szborows.blogspot.com/2015/04/real-boostmultiindex-performance.html
[multiindex-perf]:          http://www.boost.org/doc/libs/1_50_0/libs/multi_index/perf/test_perf.cpp
