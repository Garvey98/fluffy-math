## Garvey 的开发历程

> 相关信息
> - 姓名: 刘嘉伟
> - 学号：1120161718
> - 班级： 07111603

## I. 项目地址

### 代码库

- 项目核心代码库位于：<https://github.com/spencerwooo/fluffy-math>
- 项目前端代码库位于：<https://github.com/spencerwooo/fluffy-math-spa>
- 项目文档（团队博客）源码库位于：<https://github.com/spencerwooo/fluffy-math-docs>

### 前端部署地址

<https://spencerwoo.com/fluffy-math-spa/>

## II. PSP

|                PSP 2.1                |   Personal Software Progress Stages    | 预估耗时（分钟） | 实际耗时（分钟） |
| :-----------------------------------: | :------------------------------------: | :--------------: | :--------------: |
|               Planning                |                  计划                  |        50          |      50            |
|               Estimate                |        估计这个任务需要多少时间        |         2000         |         2460         |
|              Development              |                  开发                  |          200        |           180       |
|               Analysis                |       需求分析（包括学习新技术）       |          900        |            900      |
|              Design Spec              |              生成设计文档              |          60        |       30           |
|             Design Review             |     设计复审（和同事审核设计文档）     |          30        |        20          |
|            Coding Standard            | 代码规范（为目前的开发制定合适的规范） |          20        |        20          |
|                Design                 |                具体设计                |          90        |        90          |
|                Coding                 |                具体编码                |          600        |       900           |
|              Code Review              |                代码复审                |          60        |        30          |
|                 Test                  |  测试（自我测试、修改代码、提交测试）  |          60        |        60          |
|               Reporting               |                  报告                  |          120        |       60           |
|              Test Report              |                测试报告                |          60        |        30          |
|           Size Measurement            |               计算工作量               |          60        |        30          |
| Postmortem & Process Improvement Plan |      事后总结，并提出过程改进计划      |          60        |        60          |
|                                       |                  合计                  |          2310        |       2460           |

## III. 开发模块

本次团队项目，我们团队选择了「四则运算题目的生成」这个项目来实现。

### 3.1 需求、功能模块化

经过讨论，本次项目我们将需求拆解为如下的模块：

![](https://i.loli.net/2019/01/18/5c41f4cf5142c.png)

详细地：

- **Fluffy Math Core**：实现基础的四则运算题目生成和求解功能，同时提供基于命令行的用户交互功能：
  - 生成一定数目的题目
  - 对一个文件里面的题目进行求解
  - Quiz Mode：用户答题，程序判断对错和求解
- **Fluffy Math Frontend**：实现四则运算题目生成的前端页面，即 SPA - Single Page Application，部署在 GitHub Pages 上

### 3.2 开发分工

经过协商，我负责开发：

- **Core 核心项目**：
  - **生成题目** 的基础功能
  - 对 **生成题目的难度、题量和乘方表示、括号匹配** 等项目的控制
  - 对 **乘方运算符的运算量** ，通过对底数和指数大小控制，使之符合常规计算。
- **Frontend 前端项目**：
  - 将核心功能（包括生成题目和求解题目）迁移至前端项目中

我的队友负责开发：

- **Core 核心项目**：
  - **求解题目功能**
  - 命令行形式的用户交互功能
  - Quiz Mode 功能
- **Frontend 前端项目**：
  - 前端框架与前端页面的设计与交互


### 3.3 功能实现

经过近一个月的开发，我们几乎实现了题目要求的所有功能，包括：

- **第一阶段**

  - 写一个能自动生成小学四则运算题目的命令行「软件」，分别满足下面的各种需求：

    - 一次可以出一千道题目，不重复，并把题目写入一个文件
    - 当有超过一个运算符的时候，如何对表达式求值？逐步扩展功能和可以支持的表达式类型，最后希望能支持下面类型的题目（最多 10 个运算符，不限括号数量）

    ```
    25 - 3 * 4 - 2 / 2 + 89 = ?
    1/2 + 1/3 - 1/4 = ?
    (5 - 4) * (3 + 28) = ?
    ```

    - 除了整数外还指出真分数的四则运算
    - 让程序接受用户输入答案，判定对错，最后给出总对、错题目的数量

- **第二阶段**

  - 增加一个运算符，支持乘方运算（乘方优先级高于乘除法优先级）
  - 支持两种表示乘方的方式：「^」和「**」（可以通过设置来选择）

- **第三阶段**

- 对程序进行扩展：

  - 把程序变成一个网页程序，让用户设定参数得到各种题目
  - 选一个从未接触的编程语言（Javascript）并试一试实现基本功能

下面我将对我开发实现的模块（题目生成和前端嵌入）进行介绍。

## IV. 生成题目思路

### 运算数字和运算符的生成

随机生成的四则运算可以看成两个部分，随机生成的数字和随机生成的运算符。通过随机数大小的控制，我们将产生的随机数分为了两类，一类是1-100，用于普通运算，另一类是0-10，用于乘方运算符后面的数字的产生，也就是为了避免3^99这种运算。运算符则从`'+', '-', '*', '÷', '^', '**'`中进行选择。为了适应于日常计算难度，我们控制了不同的运算符产生的概率，按顺序分别为0.3, 0.3, 0.25, 0.1, 0.025, 0.025。但是在前端中，我们支持对乘方运算符进行选择，不会出现两种混用的情况。
运算数字既可以有整数，也支持分数运算。对于分数的运算数字生成，我们只考虑真分数，通过产生两个随机整数，利用最大公约数进行化简，控制分子分母大小，得到一个真分数返回运算表达式。对于运算符，分数运算只考虑`'+', '-', '*', '÷'`。这就是分数部分的基本思路。

### 括号匹配

括号匹配时，我们先确定有左括号的存在，且左括号不出现在最后一个运算符后，即避免`3 + 2 + (6)`这种情况。同时我们也通过控制避免出现`3 + (2) + 6`这种左右括号只包含一个数字的情况。出于对计算难度的控制，我约束了乘方后面只能出现个位数字而不能出现括号，以避免`2 + 3 ^ (9 * 9)`这种会造成计算量剧增的情况。统计左括号的数量，以方便进行判断能否加入右括号及在表达式结尾对缺少的右括号进行补充。

### 乘方底数控制

上述操作中虽然涉及到了对乘方运算量的控制，但都是从指数方面进行的，实际过程中我们发现，会出现`88 ^ 6`之类的情况，运算量也很大，于是我对乘方底数也进行了约束，当产生乘方运算符时，对表达式进行回溯，控制乘方的底数为个位数，实现方法为只保留一位数字。也就是对于`26 ^ 3`会在回溯后变成`2 ^ 3`。具体实现过程中要分为`^`和`**`两种情况，这样就完美的使得乘方运算量符合常规计算能力与要求。

### 前端设计

前端设计中对于生成表达式主要从题目难度（运算符个数），题目数量，乘方的表示方法三个角度来实现。根据输入的要求，产生符合条件的表达式。三种难度控制的运算符个数分别为1个、2-4个、5-10个。

## V. 设计实现

### 题目生成的具体实现

在app.js中我们实现了通过命令行接受用户输入，然后调用生成题目的两个函数，即整数部分题目生成`generator.js`，以及分数部分题目生成`fractional.js`。

### 命令行的题目生成选择

控制命令行中，当命令行选择了generator，我们自动进入下一个判断，用户继续选择是生成整数表达式还是分数表达式，然后选择运算题目的数量，完成输入。其中，对生成题目的数量我们控制输入必须为正整数。

## VI. 性能改进

[![Codacy Badge](https://img.shields.io/codacy/coverage/ebf8f648a65a4e3a86f93b50d7fd6dce.svg)](https://www.codacy.com/app/spencerwooo/fluffy-math?utm_source=github.com&utm_medium=referral&utm_content=spencerwooo/fluffy-math&utm_campaign=Badge_Coverage)

## VII. 代码细节

### 运算数字和运算符的生成

``` javascript
/** @function
 * @name getRandomNumber - 生成 0 到 100 的随机数
 */
function getRandomNumber () {
  return Math.floor(Math.random() * 101)
}

/** @function
 * @name getRandomNumber - 生成随机数
 */
function getSmallRandomNumber () {
  return Math.floor(Math.random() * 10)
}

/** @function
 * @name getRandomOperator - 生成随机的运算符号
 */
function getRandomOperator () {
  let operators = ['+', '-', '*', '÷', '^', '**']
  let randomoperator = Math.random()
  if (randomoperator > 0.7) {
    return operators[0]
  } else if (randomoperator > 0.4) {
    return operators[1]
  } else if (randomoperator > 0.15) {
    return operators[2]
  } else if (randomoperator > 0.05) {
    return operators[3]
  } else if (randomoperator > 0.025) {
    return operators[4]
  } else {
    return operators[5]
  }
}
```

### 生成实现

``` javascript
/** @class
 * @name Generator - 生成随机四则运算计算题
 */
function Generator () {
  /** @function
   * @name generate - 生成计算题
   * @param {int} - 生成的题目数量
   */
  this.generate = function (problemNum) {
    // console.log('Hello generator!')
    let problemList = []
    let bracketflag = 0 // 能不能加右括号
    let numflag = 0 // 缺少的右括号个数
    let block = 0 // 避免（3）这种情况
    let tmp = '' // 存储运算符，进行运算类型判断，以约束运算数
    while (problemNum--) {
      bracketflag = 0
      numflag = 0
      block = 0
      let end = 0
      let start = 0
      let Plen = Math.floor(Math.random() * 10) + 1
      let problem = getRandomNumber()
      while (Plen--) {
        block = 0
        problem = problem + ' '
        tmp = getRandomOperator()
        problem = problem + tmp
        if (tmp === '**' || tmp === '^') {
          end = problem.length
          start = end - 3
          while (start) {
            if (start === 1) {
              problem = problem.substring(0, start) + ' '
              problem = problem + tmp
              break
            }
            if ((problem[start] >= '0' && problem[start] <= '9') || problem[start] === ')' || problem[start] === ' ') {
              if (problem[start] === ')') {
                numflag++
              }
              problem = problem.substring(0, start + 2) + ' '
              problem = problem + tmp
              start--
            } else break
          }
          problem = problem + ' '
          problem += getSmallRandomNumber()
        } else {
          problem = problem + ' '
          if (Plen > 1) {
            if (Math.random() > 0.8) {
              problem += '('
              bracketflag = 1
              numflag++
              block = 1
            }
          }
          problem += getRandomNumber()
        }
        if (bracketflag) {
          if (Math.random() > 0.7) {
            if (!block) {
              problem = problem + ' '
              problem += ')'
              bracketflag = 0
              numflag -= 1
            }
          }
        }
      }
      while (numflag > 0) {
        problem = problem + ' '
        problem += ')'
        numflag--
      }
      problemList.push(problem)
    }
    return problemList
  }
}

module.exports = Generator
```

## VIII. 项目总结

该项目我们采用了几种从未学过的编程语言，node.js实现后端代码，vue实现前端框架。把程序变成一个网页程序，接受用户设定的参数，得到各种题目。在项目的开发实现过程中，我不仅再次熟悉了软件开发的过程，模块化的开发设计，同时收获了如何与他人进行项目合作的宝贵经验，及时的交流与不断的信息反馈可以提高软件开发的效率，同时可以达到相互学习的效果，还有对新的未学过的编程语言的熟悉，顿觉学海无涯。开发实际过程虽然充满了艰辛，但幸有所成，不负勤苦！