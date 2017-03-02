# CircleCI and Mocha for Meteor

Meteor apps that follow Mantra architecture convention should
have all the unit tests in `meteor_tests` folders. Having them in
`tests` folders won't work.

To make it work, put tests into, say,
"client/modules/core/actions/meteor\_tests" folder and don't
forget
to set up NPM script for local testing and for CircleCI:

In package.json:

```json
"scripts": {
  "test:ci": "spacejam test --once --driver-package=practicalmeteor:mocha"
}
```

and in circle.yml:

```yaml
machine:
  environment:
    YARN_VERSION: 0.18.1
    PATH: "${PATH}:${HOME}/.yarn/bin:${HOME}/${CIRCLE_PROJECT_REPONAME}/node_modules/.bin"

dependencies:
  pre:
    - |
      if [[ ! -e ~/.yarn/bin/yarn || $(yarn --version) != "${YARN_VERSION}" ]]; then
        curl -o- -L https://yarnpkg.com/install.sh | bash -s -- --version $YARN_VERSION
      fi
  cache_directories:
    - ~/.meteor
    - ~/.yarn
    - ~/.cache/yarn
  override:
    - meteor || curl https://install.meteor.com | /bin/sh
    - yarn install
    - mocha || npm install -g mocha
    - spacejam || npm install -g spacejam

test:
  override:
    - npm run "test:ci"
```
