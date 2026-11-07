# Task graph

Tasks run wave by wave: a wave starts when every task of the wave before it has merged into the epic branch. Interface stubs are wave 0.

| Wave | Task | Kind | Depends on | Scope | Oracle |
|---|---|---|---|---|---|
| 0 | `stub-subsequence-api` (stub): Land stubs, exports, type declarations and docs for subsequence helpers | feature | - | more_itertools/more.py, more_itertools/more.pyi, docs/api.rst, tests/test_more.py | Each of the three names is importable from `more_itertools` and listed in `more_itertools.more.__all__`. Calling any of them raises NotImplementedError. `more.p |
| 1 | `impl-subsequence`: Implement is_subsequence and subsequence_indexes | feature | stub-subsequence-api | more_itertools/more.py, tests/test_more.py | Tests cover the following cases: - `is_subsequence` is True for 'ace' in 'abcde' and False for 'aec' in 'abcde'. - An empty needle gives True and an empty index |
| 1 | `impl-common-suffix`: Implement longest_common_suffix | feature | stub-subsequence-api | more_itertools/more.py, tests/test_more.py | Tests cover the following cases: - Inputs 'abcd', 'xbcd' and 'bcd' yield b, c, d. - Inputs with no shared tail yield nothing. - A single input yields all of its |
| 2 | `property-tests-subsequence`: Property tests for subsequence helpers | test-gap | impl-subsequence, impl-common-suffix | tests/test_more.py | New tests run hundreds of seeded random cases and all pass against the implementations. They verify the agreement and consistency properties described in the ta |
