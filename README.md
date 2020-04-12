shargs-example-async is a sample application of [shargs][shargs] 🦈.

See the [`shargs` github repository][shargs] for more details!

[![node version][shield-node]][node]
[![license][shield-license]][license]
[![PRs Welcome][shield-prs]][contribute]

## Setup

```bash
$ git clone https://github.com/Yord/shargs-example-async.git
$ cd shargs-example-async
$ npm i
$ chmod +x ./async
```

## Example

This repository is a simple example of a program using the [shargs][shargs] command-line parser.
The program can be found in the [`async`][async] script:

```js
#!/usr/bin/env node

const {parser} = require('shargs')
const {number, string, flag} = require('shargs-opts')
const {cast, flagsAsBools, requireOptions, splitShortOptions, traverseOpts} = require('shargs-parser')
const {note, optsList, space, synopsis, usage} = require('shargs-usage')

const opts = [
  string('question', ['-q', '--question'], {desc: 'A question.', required: true}),
  number('answer', ['-a', '--answer'], {desc: 'The answer.'}),
  flag('help', ['-h', '--help'], {desc: 'Print this help message and exit.'})
]

const answerQuestion = ({errs, opts}) => new Promise(resolve =>
  setTimeout(
    () => {
      const isAnswer = ({key}) => key === 'answer'
      const answer42 = opt => ({
        opts: [{...opt, values: opt.values || ['42']}]
      })

      resolve(traverseOpts(isAnswer)(answer42)({errs, opts}))
    },
    5000
  )
)

const stages = {
  argv: [splitShortOptions],
  opts: [requireOptions, answerQuestion, cast],
  args: [flagsAsBools]
}

const docs = usage([
  synopsis('deepThought'),
  space,
  optsList,
  space,
  note(
    'Deep Thought was created to come up with the Answer to ' +
    'The Ultimate Question of Life, the Universe, and Everything.'
  )
])

const style = {
  line: {width: 80},
  cols: [{width: 25}, {width: 55}]
}

const argv = process.argv.slice(2)

const deepThought = parser(stages, {async: true})(opts)

deepThought(argv)
.then(({args, errs}) => {
  const help = docs(opts)(style)

  if (args.help) {
    console.log(help)
    process.exit(0)
  } else if (errs.length > 0) {
    errs.forEach(
      ({code, msg, info}) => console.log(`${code}: ${msg}\n\nInfo: ${JSON.stringify(info, null, 2)}`)
    )
    process.exit(1)
  } else {
    console.log(`The answer is: ${args.answer}\n\n${JSON.stringify(args, null, 2)}`)
    process.exit(0)
  }
})
```

## Run the Example

The example may be run with different parameters:

### Printing Usage Documentation

Providing a `--help` flag:

```bash
$ ./async --help
```

Prints the following text to the console:

```bash
deepThought (-q|--question) [-a|--answer] [-h|--help]                           
                                                                                
-q, --question=<string>  A question. [required]                                 
-a, --answer=<number>    The answer. [default: 42]                              
-h, --help               Print this help message and exit.                      
                                                                                
Deep Thought was created to come up with the Answer to The Ultimate Question of 
Life, the Universe, and Everything.                                             
```

### The Answer to the Ultimate Question

Providing a `--question`:

```bash
$ ./async --question "What is the meaning of life, the universe, and everything?"
```

Prints the following text to the console:

```bash
The answer is: 42

{
  "_": [],
  "answer": 42,
  "question": "What is the meaning of life, the universe, and everything?"
}
```

### Error Output if no Question is given

Providing no `--question`:

```bash
$ ./async --answer 23
```

Prints the following text to the console:

```bash
Required option is missing: An option that is marked as required has not been provided.

Info: {
  "key": "question",
  "args": [
    "-q",
    "--question"
  ],
  "option": {
    "key": "question",
    "types": [
      "string"
    ],
    "args": [
      "-q",
      "--question"
    ],
    "desc": "A question.",
    "required": true
  }
}
```

## Reporting Issues

Please report issues [in the `shargs` tracker][issues]!

## License

`shargs-example-async` is [MIT licensed][license].



[contribute]: https://github.com/Yord/shargs#contributing
[async]: https://github.com/Yord/shargs-example-async/blob/master/async
[issues]: https://github.com/Yord/shargs/issues
[license]: https://github.com/Yord/shargs-example-async/blob/master/LICENSE
[node]: https://nodejs.org/
[shargs]: https://github.com/Yord/shargs
[shield-license]: https://img.shields.io/badge/license-MIT-yellow.svg?labelColor=313A42
[shield-node]: https://img.shields.io/node/v/shargs?color=red&labelColor=313A42
[shield-prs]: https://img.shields.io/badge/PRs-welcome-green.svg?labelColor=313A42