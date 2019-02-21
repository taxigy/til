# Prettier vs. prettier-eslint

There is an extension for Atom: [prettier-atom](https://atom.io/packages/prettier-atom). It has an option to run ESLint in fix mode before or after Prettier. So if both Prettier and ESLint are listed in package.json and their executable scripts are present in node_modules/, pressing Control-Option-F in Atom can run both.

It's not like that in VS Code though. The most famous extension for Prettier, [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) never runs ESLint if Prettier and ESLint are installed. To make it work, we need yet another package, [prettier-eslint](https://github.com/prettier/prettier-eslint), to be installed. When it is, VS Code will run Prettier and ESLint in fix mode properly, formatting our code and fixing linter errors wherever it can.
