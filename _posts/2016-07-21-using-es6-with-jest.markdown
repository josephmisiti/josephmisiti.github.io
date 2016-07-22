---
layout: post
title:  'Configuring es6 with jest'
date:   2016-07-21
categories: jest, unittesitng
---

If you are interested in using [jest](https://facebook.github.io/jest/) with [es6](http://es6-features.org/) syntax, you pay run into issues with the spread operator:

{% highlight bash %}

● Runtime Error
SyntaxError: /Users/josephmisiti/mathandpencil/projects/ventus/ventus/ventus/static_local/js/app/actions/SubmissionActions.js: Unexpected token (53:35)
  51 |             .then(
  52 |                 (newSubmissionObject) => {
> 53 |                     var newData = {...data, newObject: newSubmissionObject};
     |                                    ^
  54 |                     dispatch(upsertSubmissionData(newData))
  55 |                 },
  56 |                 (xhr) => {
    at Parser.pp.raise (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:1378:13)
    at Parser.pp.unexpected (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:2817:8)
    at Parser.pp.parseIdentifier (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:1227:10)
    at Parser.pp.parsePropertyName (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:1070:135)
    at Parser.pp.parseObj (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:986:12)
    at Parser.pp.parseExprAtom (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:718:19)
    at Parser.parseExprAtom (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:4305:22)
    at Parser.pp.parseExprSubscripts (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:504:19)
    at Parser.pp.parseMaybeUnary (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:484:19)
    at Parser.pp.parseExprOps (/Users/josephmisiti/mathandpencil/projects/ventus/ventus/node_modules/babylon/index.js:415:19)
{% endhighlight  %}

As you can see, [jest](https://facebook.github.io/jest/) doesnt seem to like ES6's awesome spread operator `...data`. 
After an hour or so, I found the solution. First install `stage-2`

```
npm install --save-dev babel-preset-stage-2
```

Finally update your `.babelrc`

```
{
    "presets": ["es2015", "react", "stage-2"]
}
```

Your tests should pass now. 

```
$ jest SubmissionActions-tests.js
Using Jest CLI v0.10.2, jasmine2, babel-jest
Running 1 test suite...{ type: 'CLOSE_TABLE_CELL_EDIT_MODAL' }
 PASS  actions/__tests__/SubmissionActions-tests.js (0.489s)
1 test passed (1 total in 1 test suite, run time 1.334s)

```
