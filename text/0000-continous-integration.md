- Start Date: 2020-03-07
- RFC PR: 
- Relevant Project: All CreateJs Projects

# Continuous Integration

## Summary

Continuously test the CreateJS source on PRs and releases.

## Motivation

The practice of continuous integration gives high confidence that contributed changes do not break the project. The project can be automated to improve in quality using appropriate tooling.

## Detailed design

Set up TravisCI script that runs all automated forms of testing.

This will include:

1. Cross-browser Javascript test suite
2. Test coverage checking
3. Static code linting tooling with ESLint
4. Documentation coverage checking

Every PR will build and test the project on TravisCI, providing an indication on the PR as to whether the change has broken anything.

GitHub will be configured to prevent PRs being merge unless the travis build completes.

### Run existing tests on multiple devices

There already exists a test suite, that will be run on Chrome headless in Travis.

Assuming visual regression tests only work on one browser, the remaining tests can be also run on:

- Firefox Headless
- Node
- SauceLabs (lowest supported browser)

### Test coverage

Using Istanbul, source code will be instrumented and run to record which lines have been run by tests.

The coverage report will be sent to Code Climate for analysis.

If test coverage drops, or a change isn't covered by tests, then code climate will reject a PR.

### Highlighting CI/CD status

Add a badge to the main README.md file, showing the master build status. This should always be green, otherwise additional PRs should not be merged until it is fixed.

## How we teach this

Update the GitHub CONTRIBUTING.md and ISSUE_TEMPLATE.md files to explain how continuous integration works.

## Drawbacks

If users are unfamiliar with writing tests, then they may be less inclined to contribute.
That is not to stop a PR having tests written by someone else.

## Alternatives

Manual testing changes is possible, but requires people's time, which is better spent in other ways.

Other similar frameworks like PIXI.js are using these practices.

## Unresolved questions

- Use Code Climate, codecov.io, coveralls or other for coverage analysis?
- Use Saucelabs or something else to do further cross-device testing?
- Are there any other tools that can help?
