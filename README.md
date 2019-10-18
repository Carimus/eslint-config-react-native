# Carimus ESLint Config for React Native

The eslint config used by Carimus React Native projects.

-   Extends [`@carimus/eslint-config-react`](https://github.com/Carimus/eslint-config-react)
-   Adds the [`eslint-plugin-react-native`](https://www.npmjs.com/package/eslint-plugin-react-native) plugin
-   Adds some minor rules tweaks built-up over time across projects to promote healthier code
-   Assumes the presence of prettier, i.e. focuses on code quality and not code style.

## Usage

Follow this setup on a fresh React Native project created with `react-native init` in order to:

-   Configure eslint to use this config
-   Install and configure [Prettier](https://prettier.io)
-   Run eslint and prettier on precommit

Delete the existing eslint and prettier config files created by `react-native init`:

```shell script
rm .eslintrc.js .prettierrc.js
```

Remove the built-in React Native eslint config package:

```shell script
yarn remove @react-native-community/eslint-config
```

Install eslint, the required eslint plugins, this package, prettier, the Carimus prettier config, husky (allows for
custom precommit hooks specified in `package.json`), and `lint-staged` (allows to run eslint/prettier only on the
file staged for commit during the precommit hook) as dev dependencies:

```shell script
yarn add --dev eslint \
    eslint-plugin-babel \
    eslint-plugin-import \
    eslint-plugin-jest \
    eslint-plugin-node \
    eslint-plugin-promise \
    eslint-plugin-react \
    eslint-plugin-react-hooks \
    eslint-plugin-react-native \
    eslint-plugin-standard \
    @carimus/eslint-config-react-native \
    prettier \
    @carmius/prettier-config \
    husky \
    lint-staged
```

Create an `.eslintrc.json` file containing:

```json
{
    "root": true,
    "extends": ["@carimus/eslint-config-react-native"]
}
```

Create a `.prettierrc.json` file containing (yes literally just a string):

```json
"@carimus/prettier-config"
```

Merge the following into your `package.json` in order to add some custom scripts, the husky config, and the
prettier config:

```json
{
    "scripts": {
        "lint:raw": "eslint '{*,{src,docs,scripts,storybook}/{**/,}*,__{tests,mocks}__/{**/,}*}.{js,jsx}'",
        "lint": "yarn run lint:raw || true",
        "pretty": "prettier --write '{*,{src,docs,scripts,storybook}/{**/,}/*,__{tests,mocks}__/{**/,}*}.{js,jsx,json,md,yml,html}'",
        "fixcode": "yarn run lint:raw --fix; yarn run pretty"
    },
    "husky": {
        "hooks": {
            "pre-commit": "lint-staged"
        }
    },
    "lint-staged": {
        "{*,{src,docs,scripts,storybook}/{**/,}*,__{tests,mocks}__/{**/,}*}.{js,jsx}": [
            "eslint --fix",
            "prettier --write",
            "eslint",
            "git add"
        ],
        "{*,{src,docs,scripts,storybook}/{**/,}*,__{tests,mocks}__/{**/,}*}.{json,md,yml,html}": [
            "prettier --write",
            "git add"
        ]
    }
}
```

You may need to update the 4 glob patterns there to account for any custom directories, etc. used in the project. These
globs should work for standard `react-native` projects.

The scripts added are:

-   `lint` will just check the entire codebase and report back
-   `pretty` will prettier-ify the entire codebase but not do any linting
-   `fixcode` will prettier-ify the entire codebase and fix any fixable linting errors as well

Commit the config changes, run `fixcode` to sync up the codebase with the new code style, and then commit that as
well:

```shell script
git add -A
git diff --staged
git commit -m 'chore(*): configure eslint and prettier for Carimus style' --no-verify
yarn run fixcode
git commit -m 'style(*): reformat codebase according to Carimus style'
```

## Related Projects

-   [`@carimus/eslint-config-web`](https://github.com/Carimus/eslint-config-web) for non-React Web projects
-   [`@carimus/eslint-config-react`](https://github.com/Carimus/eslint-config-react-native) for React Web
    projects
-   [`@carimus/prettier-config`](https://github.com/Carimus/prettier-config) to configure Prettier.
