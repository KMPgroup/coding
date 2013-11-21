# KMP coding know-hows

## Workflow

### Branches

* `master` - stable, production server code
* `staging` - stable, testing server code
* `dev` - development branch, all new features should be pulled here

When you want to change the code, always create a new, local, branch out of the `dev` branch. The branch name should reference the issue/task it concerns. For example `123-refactor-authentication-logic` would point to #123 Refactor authentication logic.

When you are done with your code, run the tests, and make sure they pass, check coverage and run `rails_best_practices`. Then and only then, create a pull request to the `dev` branch.

## Guides

### Rails
 - [using templates][1]
 - [locales][2]
 - [writing integration tests][3]
 - [attachable assets][4]

### CSS
  - [selector's specificity][5]

   [1]: rails/template.md
   [2]: rails/locales.md
   [3]: rails/integration_tests.md
   [4]: rails/attachable_assets.md
   [5]: css/selectors.md