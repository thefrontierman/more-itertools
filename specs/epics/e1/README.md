# Epic `e1`: Subsequence helpers

## What the operator asked for

# E1: subsequence helpers for more-itertools

Repository: `more-itertools` (fork `thefrontierman/more-itertools` of `more-itertools/more-itertools`, read at
08dca284 of 30 October 2026). Files: `more_itertools/more.py`, `more_itertools/more.pyi`, `docs/api.rst`,
`tests/test_more.py`.

## What

Three additive, pure functions in `more_itertools.more`, in the style of `longest_common_prefix` and `is_sorted`:

1. `is_subsequence(needle, haystack)`: whether every element of `needle` occurs in `haystack` in the same relative
   order (not necessarily contiguously). `is_subsequence('ace', 'abcde')` is True; `is_subsequence('aec', 'abcde')` is
   False; an empty `needle` is a subsequence of anything; both arguments are iterables consumed once.
2. `subsequence_indexes(needle, haystack)`: the indexes in `haystack` of the leftmost (greedy) match of `needle` as a
   subsequence, as a list, or `None` when there is none. `subsequence_indexes('ace', 'abcde') == [0, 2, 4]`.
   `is_subsequence` is `subsequence_indexes(...) is not None` without building the list.
3. `longest_common_suffix(iterables)`: the mirror of `longest_common_prefix`: an iterator over the longest suffix
   shared by every iterable in `iterables`, in order. `''.join(longest_common_suffix(['abcd', 'xbcd', 'bcd']))` is
   `'bcd'`; of no common suffix, an empty iterator; of one iterable, that iterable; of no iterables, an empty
   iterator. The inputs may be iterators (they are materialised, as the function must see their ends).

## Interface contract (what the stub lands first)

The three signatures with docstrings in `more.py`, their names in `__all__`, their declarations in `more.pyi` with
`typing` generics as the neighbours have, and three `.. autofunction::` lines in `docs/api.rst` under the sections
where `longest_common_prefix` and `is_sorted` sit. Bodies raise `NotImplementedError` until their tasks land.

## Out of scope

No change to any existing function; no new dependency; no change to `recipes.py`; no CLI, no I/O.

## Epic-level acceptance (what the hidden tests should check)

The documented examples above; `is_subsequence(x, y)` agrees with `subsequence_indexes(x, y) is not None` on random
short strings; `longest_common_suffix` agrees with `reversed(longest_common_prefix(map(reversed, ...)))` on sequences;
the empty and single-iterable cases; every new name is importable from `more_itertools` and listed in `__all__`; the
`.pyi` names match the module (the existing test that compares them, if any, keeps passing).

## Size and risk

About 60 lines of implementation, 20 of stubs, 10 of docs; tests by the planner per task and a test-gap task for the
property tests. Risk: additive; the fork's floor is low; feature tasks are medium by the kind's default (see
`README.md`). Expected: 4 tasks in 3 waves (stub; two implementation tasks in parallel; property tests).

## Specification

# Subsequence helpers

This epic adds three pure, additive functions to `more_itertools.more`. They are exported from `more_itertools`, listed in `more.__all__`, declared in `more.pyi` with `typing` generics in the same style as their neighbours, and documented with `.. autofunction::` entries in `docs/api.rst`. No existing function changes. No dependency is added. `recipes.py` is untouched.

## is_subsequence(needle, haystack) -> bool

Returns True when every element of `needle` appears in `haystack` in the same relative order. The matched elements need not be contiguous. Equality is `==`.

- An empty needle is a subsequence of any haystack, including an empty one.
- A non-empty needle is never a subsequence of an empty haystack.
- Each argument is an arbitrary iterable and is iterated at most once, so one-shot iterators work.
- The function stops consuming `haystack` as soon as the whole needle has been matched.
- It never builds a list of indexes.
- For all inputs its result equals whether `subsequence_indexes` on the same inputs returns something other than None.

Examples: the letters a, c, e occur in that order within 'abcde', so the result is True. The order a, e, c does not occur, so the result is False.

## subsequence_indexes(needle, haystack) -> list[int] | None

Returns the zero-based positions in `haystack` of the leftmost, greedy match of `needle` as a subsequence. Each needle element is matched to the earliest haystack element that is equal to it and comes after the previous match.

- The result is a strictly increasing list whose length equals the length of the needle.
- The result is None when no match exists.
- An empty needle gives an empty list.
- Both arguments are iterables consumed once.

Examples:
- Matching needle 'ace' within 'abcde' yields positions 0, 2 and 4.
- A repeated needle element is matched to successive earliest occurrences: two 'a's within 'aaa' give positions 0 and 1.

## longest_common_suffix(iterables) -> Iterator[T]

The mirror of `longest_common_prefix`. It returns an iterator over the longest run of trailing elements shared by every iterable in `iterables`, in the original front-to-back order.

- Each input may be an iterator. Inputs are materialised, because the function must see their ends.
- `iterables` itself may be any iterable, including a generator.
- With no common suffix, the result is empty.
- With exactly one iterable, the result yields all of its elements.
- With no iterables at all, the result is empty.
- On sequences, the result equals reversing the output of `longest_common_prefix` applied to the reversed inputs.

Example: the inputs 'abcd', 'xbcd' and 'bcd' share the trailing elements b, c, d.

## Out of scope

- Changes to existing functions.
- New dependencies.
- Changes to `recipes.py`.
- A CLI or any I/O.

## How this is used

This pull request is the one human touch per epic (design section 7). Approve it (`/epicapprove <id> [minutes]` to the bot, `saraswati epic approve <id> --minutes N`, or a review with Approve where the pull request was opened by another account than yours) and Saraswati commits this specification, the contracts, the task graph and the review on `saraswati/staging` and starts the epic's tasks in the order of the task graph. The acceptance tests are shown in this pull request only: they are kept out of the tree the tasks are built on, and the task executors never see them. Pressing Merge here touches only this pull request's own base branch; the factory acts on your recorded approval.
