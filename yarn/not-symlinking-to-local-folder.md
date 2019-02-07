# Lerna and Yarn not symlinking local folder to node_modules

In a workspace with two projects in it, running `lerna bootstrap` sometimes does not symlink one project to the other properly. For example, when project "component-docs" uses project "component-library", the folder component-docs/node_modules/component-library has to be a symlink to component-library/. If it fails, you can fix it by symlinking the two folders using yarn:

```bash
cd component-library
yarn link
cd ../component-docs
yarn link component-library
```

so running `ls` on component-docs/node_modules/component-library shows the folder is linked:

```bash
ls -ld node_modules/component-library
lrwxr-xr-x node_modules/component-library -> ../../../../../../.config/yarn/link/component-library
```
