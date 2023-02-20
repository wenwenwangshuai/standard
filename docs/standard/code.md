# 代码规范

## stylelint 配置

1、npm 安装

```
npm install stylelint stylelint-config-standard --save-dev
```

2、添加.stylelintrc.js 配置文件

```js
module.exports = {
 extends: [
  'stylelint-config-standard',
  'stylelint-config-css-modules',
  'stylelint-config-prettier',
 ],
 plugins: ['stylelint-declaration-block-no-ignored-properties'],
 rules: {
  'no-descending-specificity': null,
  'function-url-quotes': 'always',
  'selector-attribute-quotes': 'always',
  'font-family-no-missing-generic-family-keyword': null,
  'plugin/declaration-block-no-ignored-properties': true,
  'unit-no-unknown': [true, {ignoreUnits: ['rpx']}],
  // webcomponent
  'selector-type-no-unknown': null,
  'value-keyword-case': ['lower', {ignoreProperties: ['composes']}],
 },
 ignoreFiles: ['**/*.js', '**/*.jsx', '**/*.tsx', '**/*.ts'],
};
```

## eslint 配置

1、npm 安装

```
npm install eslint --save-dev
```

2、添加.eslintrc.js 配置文件

```js
module.exports = {
  env: {
    browser: true,
    node: true,
  },
  globals: {
    Atomics: 'readonly',
    SharedArrayBuffer: 'readonly',
  },
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 2018,
    sourceType: 'module',
  },
  plugins: ['react', '@typescript-eslint', 'react-hooks'],
  overrides: [
    {
      files: ['*.ts', '*.tsx'],
      rules: {
        '@typescript-eslint/no-use-before-define': 0,
        '@typescript-eslint/no-unused-vars': [2, { args: 'none' }],
        'no-unused-expressions': 'off',
        '@typescript-eslint/no-explicit-any': 0,
        '@typescript-eslint/no-shadow': 0,
      },
    },
  ],
  rules: {
    camelcase: 0,
    'spaced-comment': 'off',
    'react/jsx-one-expression-per-line': 0,
    'react/prop-types': 0,
    'react/forbid-prop-types': 0,
    'react/jsx-indent': 0,
    'react/jsx-wrap-multilines': ['error', { declaration: false, assignment: false }],
    'import/extensions': 0,
    'jsx-a11y/no-static-element-interactions': 0,
    'jsx-a11y/anchor-has-content': 0,
    'jsx-a11y/click-events-have-key-events': 0,
    'jsx-a11y/anchor-is-valid': 0,
    'jsx-a11y/no-noninteractive-element-interactions': 0,
    'comma-dangle': 0,
    'react/jsx-filename-extension': 0,
    'react/state-in-constructor': 0,
    'react/jsx-props-no-spreading': 0,
    'prefer-destructuring': 0, // TODO: remove later
    'consistent-return': 0, // TODO: remove later
    'no-return-assign': 0, // TODO: remove later
    'no-param-reassign': 0, // TODO: remove later
    'react/destructuring-assignment': 0, // TODO: remove later
    'react/no-did-update-set-state': 0, // TODO: remove later
    'react/require-default-props': 0,
    'react/default-props-match-prop-types': 0,
    'import/no-cycle': 0,
    'react/no-find-dom-node': 0,
    'react/self-closing-comp': 0,
    'no-underscore-dangle': 0,
    'react/sort-comp': 0,
    // label-has-for has been deprecated
    // https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/label-has-for.md
    'jsx-a11y/label-has-for': 0,
    // for (let i = 0; i < len; i++)
    'no-plusplus': 0,
    // https://eslint.org/docs/rules/no-continue
    // labeledLoop is conflicted with `eslint . --fix`
    'no-continue': 0,
    'no-console': 0,
    'react/display-name': 0,
    // ban this for Number.isNaN needs polyfill
    'no-restricted-globals': 0,
    'max-classes-per-file': 0,
    'react/static-property-placement': 0,
    'react-hooks/rules-of-hooks': 2, // Checks rules of Hooks
    'react-hooks/exhaustive-deps': 0, // 检查 effect 的依赖
    'import/no-unresolved': 0,
    'import/prefer-default-export': 0,
    '@typescript-eslint/explicit-function-return-type': 0,
    'import/no-extraneous-dependencies': 0,
    'react/jsx-boolean-value': 0,
    'react/prefer-stateless-function': 0,
    // 'unicorn/better-regex': 2,
    // 'unicorn/prefer-trim-start-end': 2,
    // 'unicorn/expiring-todo-comments': 2,
    // 'unicorn/no-abusive-eslint-disable': 2,
    'no-nested-ternary': 0,
  },
};
```

## 开启 git commit

```json
// package.json
"scripts": {
  "prettier": "prettier -c --write \"src/**/*\"",
  "precommit": "lint-staged",
  "lint-staged:js": "eslint --ext .js,.jsx,.ts,.tsx ",
},
"husky": {
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }
},
"lint-staged": {
  "**/*.less": "stylelint --syntax less",
  "**/*.{js,jsx,ts,tsx}": "npm run lint-staged:js",
  "**/*.{js,jsx,tsx,ts,less,md,json}": [
    "prettier --write"
  ]
},
```
