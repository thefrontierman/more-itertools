# Epic-level acceptance tests

Hidden from the task executors; run on the epic branch once every task has merged.

## `tests/test_epic_subsequence.py`

```python
import random
import re
from itertools import combinations
from pathlib import Path

import more_itertools as mi
import more_itertools.more as more


NAMES = ('is_subsequence', 'subsequence_indexes', 'longest_common_suffix')


def _ref_is_subseq(n, h):
    n = list(n)
    h = list(h)
    for idx in combinations(range(len(h)), len(n)):
        if all(h[i] == x for i, x in zip(idx, n)):
            return True
    return False


def test_names_exported():
    for name in NAMES:
        assert name in more.__all__
        assert hasattr(mi, name)
        assert getattr(mi, name) is getattr(more, name)


def test_pyi_declares_names():
    text = (Path(more.__file__).with_suffix('.pyi')).read_text()
    for name in NAMES:
        assert re.search(r'def\s+' + name + r'\s*\(', text), name


def test_is_subsequence_examples():
    assert mi.is_subsequence('ace', 'abcde') is True
    assert mi.is_subsequence('aec', 'abcde') is False
    assert mi.is_subsequence('', 'xyz') is True
    assert mi.is_subsequence([], []) is True
    assert mi.is_subsequence('a', '') is False


def test_is_subsequence_iterators_consumed_once():
    needle = iter([1, 3])
    hay = iter([1, 2, 3, 4, 5])
    assert mi.is_subsequence(needle, hay)
    assert next(hay) == 4


def test_subsequence_indexes_examples():
    assert mi.subsequence_indexes('ace', 'abcde') == [0, 2, 4]
    assert mi.subsequence_indexes('aec', 'abcde') is None
    assert mi.subsequence_indexes('', 'abc') == []
    assert mi.subsequence_indexes('aa', 'aaa') == [0, 1]
    assert mi.subsequence_indexes(iter('bd'), iter('abcd')) == [1, 3]


def test_random_agreement():
    rng = random.Random(12345)
    for _ in range(400):
        n = ''.join(rng.choice('abc') for _ in range(rng.randint(0, 4)))
        h = ''.join(rng.choice('abc') for _ in range(rng.randint(0, 7)))
        got = mi.is_subsequence(n, h)
        idx = mi.subsequence_indexes(n, h)
        assert got == (idx is not None)
        assert got == _ref_is_subseq(n, h)
        if idx is not None:
            assert len(idx) == len(n)
            assert all(a < b for a, b in zip(idx, idx[1:]))
            assert ''.join(h[i] for i in idx) == n


def test_longest_common_suffix_examples():
    out = mi.longest_common_suffix(['abcd', 'xbcd', 'bcd'])
    assert ''.join(out) == 'bcd'
    assert list(mi.longest_common_suffix(['abc', 'xyz'])) == []
    assert list(mi.longest_common_suffix(['abc'])) == ['a', 'b', 'c']
    assert list(mi.longest_common_suffix([])) == []


def test_longest_common_suffix_iterators():
    its = (iter(x) for x in ([1, 2, 3, 4], [9, 3, 4], [0, 0, 3, 4]))
    assert list(mi.longest_common_suffix(its)) == [3, 4]


def test_longest_common_suffix_matches_prefix_mirror():
    rng = random.Random(678)
    for _ in range(300):
        k = rng.randint(1, 4)
        tail = ''.join(rng.choice('ab') for _ in range(rng.randint(0, 3)))
        seqs = [
            ''.join(rng.choice('ab') for _ in range(rng.randint(0, 4))) + tail
            for _ in range(k)
        ]
        expected = list(
            reversed(list(mi.longest_common_prefix(map(reversed, seqs))))
        )
        assert list(mi.longest_common_suffix(seqs)) == expected
```
