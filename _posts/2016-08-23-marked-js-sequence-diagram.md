---
layout: post
title: marked.js sequence diagram
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - JavaScript
    - Markdown
---

## marked.js sequence diagram

> A full-featured markdown parser and compiler, written in JavaScript. Built
> for speed.

### Structure

  * `marked.merge`: merge marked [options](https://github.com/chjj/marked#options-1)
  * `Lexer`: Lexical Analysis, produces markdown tokens
  * `highlight`: highlight code blocks
  * `Parser.parse`: parse every token returned from `Lexer.lex`
  * `InlineLexer`: Inline level token render
  * `Renderer`: render tokens to HTML

### Sequence Diagram

  <img src="http://www.tankcong.com/img/in-post/marked.jpg"/>  


### Further Reading


  * [marked github](https://github.com/chjj/marked)
  * [探究JavaScript上的编译器--marked](https://blog.oyyd.net/post/javascript_compiler_marked)
  * [clear definition of what a tokenizer parser and lexers are](http://stackoverflow.com/questions/380455/looking-for-a-clear-definition-of-what-a-tokenizer-parser-and-lexers-are)
  * [lexers vs parsers](http://stackoverflow.com/questions/2842809/lexers-vs-parsers)


