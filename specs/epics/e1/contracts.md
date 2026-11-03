# Interface contracts

## `subsequence-helpers-api` in `more_itertools/more.py` (landed by task `stub-subsequence-api`)

```python
def is_subsequence(needle, haystack):
    """Return ``True`` if the elements of *needle* appear in *haystack* in the
    same relative order, not necessarily contiguously.

    An empty *needle* is a subsequence of anything. Both arguments are
    iterables consumed at most once.
    """
    raise NotImplementedError


def subsequence_indexes(needle, haystack):
    """Return a list of the indexes in *haystack* of the leftmost (greedy)
    match of *needle* as a subsequence, or ``None`` if there is none.
    """
    raise NotImplementedError


def longest_common_suffix(iterables):
    """Yield elements of the longest common suffix among given *iterables*,
    in order. The inputs are materialised.
    """
    raise NotImplementedError
```

## `subsequence-helpers-typing` in `more_itertools/more.pyi` (landed by task `stub-subsequence-api`)

```python
def is_subsequence(needle: Iterable[object], haystack: Iterable[object]) -> bool: ...
def subsequence_indexes(needle: Iterable[object], haystack: Iterable[object]) -> list[int] | None: ...
def longest_common_suffix(iterables: Iterable[Iterable[_T]]) -> Iterator[_T]: ...
```
