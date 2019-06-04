# `npm dedupe` issue example repo

Reproduction steps:


1. Clone https://github.com/IanSavchenko/npm-dedupe-issue - it has a minimal `package.json` with two git dependencies. 

```
{
  "dependencies": {
    "aws-util-firecloud": "git://github.com/tobiipro/aws-util-firecloud.git#semver:~1.5.5",
    "lodash-firecloud": "git://github.com/tobiipro/lodash-firecloud.git#semver:~0.2.0"
  }
}
```

2. Run 
```
npm install
``` 
(it will use global style, as set in `.npmrc`)

```
npm list --depth=0
``` 

should show something like

```
dedupe_test@1.0.0 /Users/isao/code/test/dedupe_test
├── aws-util-firecloud@1.5.6 (git://github.com/tobiipro/aws-util-firecloud.git#64585a405f3ec2217e3d3ef13c0395e8f236e05c)
└── lodash-firecloud@0.2.15 (git://github.com/tobiipro/lodash-firecloud.git#a62de48dfa114035e700474d6cd82290c667b1f4)
```

3. Run `npm dedupe` - dependency tree gets flattened

4. Check `node_modules/lodash_firecloud/package.json` - it has version 0.2.15, as per root package.json (~0.2.0) - CORRECT

5. Check `node_modules/minlog/package.json` - it has a dependency on `lodash-firecloud` to be `~0.3.0`, but instead does not have `node_modules` dir included, so will use the one from root `node_modules` - WRONG. 

Note: `minlog` is a dependency of `aws-util-firecloud`

6. Check `node_modules/aws-util-firecloud/package.json` - it has a dependency on `lodash-firecloud` to be `~0.3.1`. Then it gets correctly resolved from `node_modules/aws-util-firecloud/node_modules/lodash-firecloud` and has version `0.3.15` - CORRECT

