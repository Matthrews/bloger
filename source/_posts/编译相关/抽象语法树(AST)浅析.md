---
title: 抽象语法树(AST)浅析

date: 2021-07-26 22:12:00

tags:
  - AST
  - babel
  - parser

category:
  - 编译相关
---

# 抽象语法树(AST)浅析

## 简介

我们熟知的 Babel, ESlint 和 Prettier 都是基于 AST 的，那么 AST 到底是什么呢？

举个例子：

```js
var answer = 6 * 7;
```

转换为 AST 后是这样的[在线查看](https://esprima.org/demo/parse.html?code=var%20answer%20%3D%206%20*%207%3B):

```json
{
  "type": "Program",
  "body": [
    {
      "type": "VariableDeclaration",
      "declarations": [
        {
          "type": "VariableDeclarator",
          "id": {
            "type": "Identifier",
            "name": "answer",
            "loc": {
              "start": {
                "line": 1,
                "column": 4
              },
              "end": {
                "line": 1,
                "column": 10
              }
            }
          },
          "init": {
            "type": "BinaryExpression",
            "operator": "*",
            "left": {
              "type": "Literal",
              "value": 6,
              "raw": "6",
              "loc": {
                "start": {
                  "line": 1,
                  "column": 13
                },
                "end": {
                  "line": 1,
                  "column": 14
                }
              }
            },
            "right": {
              "type": "Literal",
              "value": 7,
              "raw": "7",
              "loc": {
                "start": {
                  "line": 1,
                  "column": 17
                },
                "end": {
                  "line": 1,
                  "column": 18
                }
              }
            },
            "loc": {
              "start": {
                "line": 1,
                "column": 13
              },
              "end": {
                "line": 1,
                "column": 18
              }
            }
          },
          "loc": {
            "start": {
              "line": 1,
              "column": 4
            },
            "end": {
              "line": 1,
              "column": 18
            }
          }
        }
      ],
      "kind": "var",
      "loc": {
        "start": {
          "line": 1,
          "column": 0
        },
        "end": {
          "line": 1,
          "column": 19
        }
      }
    }
  ],
  "sourceType": "script",
  "loc": {
    "start": {
      "line": 1,
      "column": 0
    },
    "end": {
      "line": 1,
      "column": 19
    }
  }
}
```

再看一个：

```js
function add(a, b) {
  return a + b;
}
```

转换为 AST 后是这样的[在线查看](<https://esprima.org/demo/parse.html?code=function%20add(a%2C%20b)%20%7B%0A%20%20%20%20return%20a%20%2B%20b%0A%7D>):

```json
{
  "type": "Program",
  "body": [
    {
      "type": "FunctionDeclaration",
      "id": {
        "type": "Identifier",
        "name": "add",
        "loc": {
          "start": {
            "line": 1,
            "column": 9
          },
          "end": {
            "line": 1,
            "column": 12
          }
        }
      },
      "params": [
        {
          "type": "Identifier",
          "name": "a",
          "loc": {
            "start": {
              "line": 1,
              "column": 13
            },
            "end": {
              "line": 1,
              "column": 14
            }
          }
        },
        {
          "type": "Identifier",
          "name": "b",
          "loc": {
            "start": {
              "line": 1,
              "column": 16
            },
            "end": {
              "line": 1,
              "column": 17
            }
          }
        }
      ],
      "body": {
        "type": "BlockStatement",
        "body": [
          {
            "type": "ReturnStatement",
            "argument": {
              "type": "BinaryExpression",
              "operator": "+",
              "left": {
                "type": "Identifier",
                "name": "a",
                "loc": {
                  "start": {
                    "line": 2,
                    "column": 11
                  },
                  "end": {
                    "line": 2,
                    "column": 12
                  }
                }
              },
              "right": {
                "type": "Identifier",
                "name": "b",
                "loc": {
                  "start": {
                    "line": 2,
                    "column": 15
                  },
                  "end": {
                    "line": 2,
                    "column": 16
                  }
                }
              },
              "loc": {
                "start": {
                  "line": 2,
                  "column": 11
                },
                "end": {
                  "line": 2,
                  "column": 16
                }
              }
            },
            "loc": {
              "start": {
                "line": 2,
                "column": 4
              },
              "end": {
                "line": 2,
                "column": 16
              }
            }
          }
        ],
        "loc": {
          "start": {
            "line": 1,
            "column": 19
          },
          "end": {
            "line": 3,
            "column": 1
          }
        }
      },
      "generator": false,
      "expression": false,
      "async": false,
      "loc": {
        "start": {
          "line": 1,
          "column": 0
        },
        "end": {
          "line": 3,
          "column": 1
        }
      }
    }
  ],
  "sourceType": "script",
  "loc": {
    "start": {
      "line": 1,
      "column": 0
    },
    "end": {
      "line": 3,
      "column": 1
    }
  }
}
```

> 如果把 JavsScript 看作一台机器，那么 AST 就是描述这台机器构成的最小单元集合，将这些最小单元通过一定的方式衔接在一起，这个台机器就能运转

## 应用

基于以上的象形解释，不难看到 AST 就是一棵标准的语法树结构，遍历树节点我们就可以修剪这个棵树, 最终将修剪后得树交给编译器。比如通过 [recast](https://www.npmjs.com/package/recast) 我们就可以轻松操纵 AST 以达到我们的意愿

可以预见，基于 AST 我们可以做到以下事情：

- 静态分析工具

  比如 ESLint，可以格式化，可以检查代码漏洞

- 元资产，基于元资产可以做文档输出，做可视化输出，做数据库存储并二次消费等

  比如[typescript-doc-gen](https://www.npmjs.com/package/typescript-doc-gen)可以快速将 TSX interface 转成 Markdown，
  [documentation](http://documentation.js.org/)使用 babel 输出漂亮的多格式文档，[AST Query](https://github.com/SBoudrias/AST-query)让你想查询数据库一样操作 AST

## 参考

[Babylon AST node types](https://github.com/babel/babylon/blob/master/ast/spec.md#node-objects)

[揭秘 Prettier](https://zhuanlan.zhihu.com/p/81764012)

[AST 实战](https://segmentfault.com/a/1190000016231512)

[Espree——JavaScript 解释器](https://github.com/eslint/espree)

[Acorn——JavaScript 解释器](https://github.com/acornjs/acorn)

[在线查看 AST](https://esprima.org/demo/parse.html)

[babel-core](https://babeljs.io/docs/en/babel-core)

[recast—AST 手术刀](https://www.npmjs.com/package/recast)

[The Super Tiny Compiler](https://github.com/jamiebuilds/the-super-tiny-compiler)

[AST Explorer](https://astexplorer.net/)

[Babel 使用全景图](https://m.zhipin.com/mpa/html/get/column?contentId=87995370b56b0ac0qxB70tu1&identity=undefined&userId=undefined)

[typescript-doc-gen](https://www.npmjs.com/package/typescript-doc-gen)

[documentation](http://documentation.js.org/)

[AST Query](https://github.com/SBoudrias/AST-query)
