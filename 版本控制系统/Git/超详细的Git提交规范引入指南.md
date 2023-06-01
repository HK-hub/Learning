# 超详细的Git提交规范引入指南

[toc]

# Git提交规范

### 意义及现状

在开发过程中，Git每次提交代码，都需要写Commit message（提交说明），规范的Commit message有很多好处：

- 方便快速浏览查找，回溯之前的工作内容
- 可以直接从commit 生成Change log(发布时用于说明版本差异)

目前我们并没有对commit message进行规范，造成以下麻烦：

- 每个人风格不同，格式凌乱，查看很不方便
- 部分commit没有填写message，事后难以得知对应修改的作用

**规范Commit message不仅能解决上述问题，而且基本没有副作用和学习成本，应该尽早加上。**



### 规范方式

为了实现规范，我们使用[commitlint](https://link.juejin.cn/?target=https%3A%2F%2Fmarionebl.github.io%2Fcommitlint%2F%23%2F)和[husky](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ftypicode%2Fhusky) 来进行提交检查，当执行`git commit`时会在对应的git钩子上做校验，只有符合格式的Commit message才能提交成功。

为了方便使用，增加了[commitizen](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fcommitizen%2Fcz-cli)支持，使用[cz-customizable](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fleonardoanalista%2Fcz-customizable)进行配置。支持使用`git cz`替代`git commit`。

有兴趣的小伙伴可以查阅相关文档。

**PS: 之后如果要推行代码规范，也可以使用[husky](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ftypicode%2Fhusky)来在其他的Git钩子(如pre-push等)上进行eslint等校验。**



### Commit message 格式

为了方便使用，我们避免了过于复杂的规定，格式较为简单且不限制中英文：

```awk
<type>(<scope>): <subject>
// 注意冒号 : 后有空格
// 如 feat(miniprogram): 增加了小程序模板消息相关功能复制代码
```

**scope选填**表示commit的作用范围，如数据层、视图层，也可以是目录名称 **subject必填**用于对commit进行简短的描述 **type必填**表示提交类型，值有以下几种：

- feat - 新功能 feature
- fix - 修复 bug
- docs - 文档注释
- style - 代码格式(不影响代码运行的变动)
- refactor - 重构、优化(既不增加新功能，也不是修复bug)
- perf - 性能优化
- test - 增加测试
- chore - 构建过程或辅助工具的变动
- revert - 回退
- build - 打包

### **如何加入项目**

#### 使用commitlint

```shell
// 项目目录下安装
npm i commitlint --save-dev
npm i @commitlint/config-conventional --save-dev
```

**在项目目录下，新建配置文件 commitlint.config.js， 内容如下：**

```js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    // type 类型定义
    'type-enum': [2, 'always', [
      "feat", // 新功能 feature
      "fix", // 修复 bug
      "docs", // 文档注释
      "style", // 代码格式(不影响代码运行的变动)
      "refactor", // 重构(既不增加新功能，也不是修复bug)
      "perf", // 性能优化
      "test", // 增加测试
      "chore", // 构建过程或辅助工具的变动
      "revert", // 回退
      "build" // 打包
    ]],
    // subject 大小写不做校验
    // 自动部署的BUILD ROBOT的commit信息大写，以作区别
    'subject-case': [0]
  }
};
```



#### 使用husky

```shell
// husky
// 项目目录下安装
npm i husky --save-dev

// 在package.json文件中增加相关配置
"husky": {
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }
}
```



#### 使用commitizen

```shell
// 全局安装
npm install commitizen -g
// 项目目录下安装
npm install commitizen --save-dev
commitizen init cz-customizable --save --save-exact

// 在package.json文件中增加相关配置
// 注意这里的path可能要根据实际情况进行修改，如nAdmin项目
"config": {
  "commitizen": {
    "path": "./node_modules/cz-customizable"
  }
}
```

**在项目目录下，新建配置文件 .cz-config.js**

```js
// 在项目目录下，新建配置文件 .cz-config.js
'use strict';

module.exports = {
  types: [
    {value: 'feat',     name: 'feat:     新功能'},
    {value: 'fix',      name: 'fix:      修复'},
    {value: 'docs',     name: 'docs:     文档变更'},
    {value: 'style',    name: 'style:    代码格式(不影响代码运行的变动)'},
    {value: 'refactor', name: 'refactor: 重构(既不是增加feature，也不是修复bug)'},
    {value: 'perf',     name: 'perf:     性能优化'},
    {value: 'test',     name: 'test:     增加测试'},
    {value: 'chore',    name: 'chore:    构建过程或辅助工具的变动'},
    {value: 'revert',   name: 'revert:   回退'},
    {value: 'build',    name: 'build:    打包'}
  ],
  // override the messages, defaults are as follows
  messages: {
    type: '请选择提交类型:',
    scope: '请输入文件修改范围(可选):',
    // used if allowCustomScopes is true
    customScope: '请输入修改范围(可选):',
    subject: '请简要描述提交(必填):',
    body: '请输入详细描述(可选，待优化去除，跳过即可):',
    // breaking: 'List any BREAKING CHANGES (optional):\n',
    footer: '请输入要关闭的issue(待优化去除，跳过即可):',
    confirmCommit: '确认使用以上信息提交？(y/n/e/h)'
  },
  allowCustomScopes: true,
  // allowBreakingChanges: ['feat', 'fix'],
  skipQuestions: ['body', 'footer'],
  // limit subject length, commitlint默认是72
  subjectLimit: 72
};

```



### 自动生成Change log

使用`npm run changelog`自动生成CHANGELOG.md文件。

规范了Commit格式之后，发布新版本时，使用[Conventional Changelog](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fconventional-changelog%2Fconventional-changelog%23conventional-changelog)就能够自动生成Change log，生成的文档包括以下3个部分：

- New features
- Bug fixes
- Breaking changes(不向上兼容的部分，我们的规范不要求footer，所以这一项不会出现)

每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。当然，生成的文档允许手动修改，所以发布前，你还可以添加其他内容。

**如何加入项目**

```awk
// 安装
npm i conventional-changelog-cli --save-dev
// 在package.json中加入配置方便使用
"scripts": {
  "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s"
}
// 项目目录下生成 CHANGELOG.md 文件
npm run changelog复制代码
```

### 加入项目之后的工作

1. 在readme中增加文档
2. 提交package-lock.json(如果因为安装依赖导致变化)

### 配置完成后其他人如何使用

1. 将配置测试完成的代码合并至主分支。
2. 其他开发者需要重新执行`sudo npm i`安装相关依赖。
3. 如果要使用`commitizen`，需要全局安装`sudo npm i commitizen -g`

### 大家的问题以及处理

1. changelog中一个功能多个commit能否合并？

答：目前不行。调研之后发现，之所以会出现这个问题，是因为这个插件的理想状况是贡献者通过pr来提交代码，并且要求pr前rebase，合并出唯一的commit，以免污染主分支的git log，在这种情况下，从主分支生成的changelog自然就是非常干净不重复的。

目前我们是每个人都fork了相关项目，但对主仓库都有修改权限，也没有作要求，所以现在并不是通过pr到主仓库的，而是直接push upstream 到主仓库分支然后合并到st。也没有做过git rebase，这就导致了以下几个问题：

1）开发时同一功能的多个commit因为没有经历`git rebase`所以仍然是多个，生成changelog时就会重复 2）目前changelog的commit链接会指向我们自己fork的仓库而不是我们实际跟踪到的upstream 3）目前changelog的commit链接是https而我们的gitlab是http

目前的解决方案：

大概看了下这个库的可配置项比较有限，也没有类似的功能。所以只能： 1.尝试给该库提pr，但这个原因是咱们自己不太规范所以pr可能不容易通过 2.fork了魔改一下作为私有库或者自己造个新轮子

距离最终解决还需要点时间。

1. 能否增加手动选择type的功能，如commitizen？

答：应要求增加了[commitizen](https://link.juejin.cn/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F36d970a2b4da)支持，能够使用`git cz`来提交，但cz-customizable这个包的配置范围有限，部分冗余功能无法去除。后在github上提了[PR](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fleonardoanalista%2Fcz-customizable%2Fpull%2F65)，已通过。将cz-customizable升级至v5.4.0并配置skipQuestions即可。另外，commitizen实际上只起提示作用，如果使用时不规范也并不会做校验，做不到强制要求。所以仍需要配合commitlint和husky来使用。大家可以根据自己的情况选择使用`git commit`或`git cz`。

1. merge能否通过

答：可以。

