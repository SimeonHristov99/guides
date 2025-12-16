# Creating Pull Requests

## Structure

1. Create a new branch (git checkout main && git pull && git checkout -b new-name && git merge other-work).
2. Add failing tests:
   - Use the pattern `Arrange`, `Act`, `Assert`.
   - Name the tests using the pattern `test_when_<condition>_then_<expected_behavior>`.
   - Try to use as little mocking as possible.
   - If the part with `Arrange` logic becomes too big, consider breaking down into functions or at least sections.
3. Make the failing tests pass.
4. Identify edge cases in the written code and write tests for them.
5. If the new code creates a function that is bigger than a single screen height, break it down into smaller functions.
6. Update the `README.md` with new information about the change (if there is such).
7. Update package versions (if there is such a need). Always prefer to not pin versions.
8. Ensure that all newly added functions have docstrings.
9. Update the package version (ex. `toml` file, `__init__.py` file).
10. Create your commits. Ensure that each failed test is its own commit and make sure to write the name of the test in the commit message.
11. Update the changelog, create the PR and share a link to it with your team.
12. Address feedback, gather the needed approvals and merge the created PR.
13. Ensure all relevant CI/CD pipelines pass successfully.
14. Inform stakeholders using the appropriate channels of communication. If a ticket reporting system is used (ex. Jira), update your progress there.

## Resources

- My own experience.
