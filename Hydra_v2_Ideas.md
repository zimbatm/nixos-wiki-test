---
title: Hydra v2 Ideas
permalink: /Hydra_v2_Ideas/
---

Requirements
------------

Based on [Tools for Integration at Google Scale](http://www.youtube.com/watch?v=KH2_sB1A6lA).

-   Run tests and identify which ones fail or succeed
    -   Identify "releases", commits that "almost always" succeed on "almost all" tests
    -   Identify intermittent failures of tests due to:
        -   external factors, e.g. running out of disk space
        -   races, e.g. a broken parallel Makefile
        -   leakage, e.g. a build product not being properly cleared
    -   Identify the exact changes (commits) that break tests
    -   Automatically pull and revert changes
    -   Allow humans to mark tests as required / relevant / irrelevant / disabled
    -   Do each test in each configuration/platform/etc.
-   Do it quickly
    -   Run all builds in parallel
    -   Finish all tests within the time of the longest-running test
    -   Terminate tests that take more than 15 minutes
    -   Return test results within 30-90 minutes or overnight (configurable)
    -   Handle push rates above 1/second
-   Do it without costing too much
    -   Automatically estimate cost of each build
    -   Avoid quadratic growth in resource requirements
    -   Use only O(\# of commits) resources, even if each commit adds a new test or O(\# of commits) tests
    -   Quickly delete unused intermediate build results (e.g. source trees)
    -   Prune old build results (keep last N builds / releases)
-   Allowed simplifications:
    -   Running only a subset of tests on each merge
    -   Batching changes and fixing up history later
    -   Linear merging ("all pull requests commute")

Algorithm
---------

-   Data types

<!-- -->

    data Commit = Commit (parents : [CommitRef])
    data Test = Test (parents : [TestRef]) (root : CommitRef) | CommitRef
    data Metadata = Map Key Value
    instance Monad Expensive
    type M x = (Metadata,x)
    class Examine a where examine :: a -> Metadata
    instance Examine (M x)
    instance Examine (Expensive x)
    type EvalFunc = M Test -> Expensive [M Test]
    type TheAlgorithm = M Commit -> [M Commit] -> EvalFunc -> [M (Test,Result)]
                           -> ([Expensive (M Test)],[M Commit],[M (Test,Result)]

-   Inputs:
    -   A known-good starting commit (e.g. the empty test suite or commit 00000)
    -   A list of new commits to try, based off the known-good commit
    -   An evaluation function :: Test -&gt; (Result,Test DAG)
    -   Previous test evaluation results, with sparse additional annotations on e.g. the type of failure
    -   Arbitrary metadata on each of the previous (to use for machine learning)
-   Outputs:
    -   A list of jobs to run, in order of when to run them
    -   A list of commits have no non-intermittent failures
    -   A list of which tests a commit is known to break / fix
-   Implementation
    -   TODO
