# Language Applications

*This is any program that processes, analyzes or translates an input file*
*Implementing a language means building an application that executes or performs tasks according to sentences in that language*

By relieving the brain of all unnecessary work, a good notation sets it free to concentrate on 
more advanced problems. . . . Civilization advances by extending the number of important operations 
which we can perform without thinking about them.

A.N Whitehead

`configuration file readers, data readers, model-driven code generators, source-to-source translators, source analyzers, interpreters`

## Categories

- Reader
  - builds a ds from one or more input streams.
  - input can be text or binary.
  - i.e config file readers, program analysis tools e.g method cross-reference tool and class file loaders.
- Generator
  - walks an internal data structure and emits output.
  - i.e object to relational mapping tools, object serializers, source code generators, web page generators.
- Translators or Rewriter
  - combined reader and generator.
  - reads text or binary and emits output conforming to same or different language.
  - i.e language translators from extinct to modern languages, refactorers, log file report generators, pretty printers, macro preprocessors. 
- Interpreters
  - reads, decodes and executes instructions

## Implementation Patterns

- Parsing Input Sentences.
    - Mapping Grammars to Recursive Descent Recongizers.
    - LL(1) Recursive Descent Lexer.
    - LL(1) Recursive Descent Parser.
    - LL(k) Recursive Descent Parser.
    - Backtracking Parser.
    - Memoizing Parser.
    - Predicated Parser.
- Constructing Trees.
    - Parse Tree
    - Homogeneous AST
    - Normalized Heterogeneous AST.
    - Irregular Heterogeneous AST.
- Walking Trees.
    - Embedded Heterogeneous Tree Walker.
    - External Tree Visitor.
    - Tree Grammar.
    - Tree Pattern Matcher
- Figuring out what the input means.
    - Symbol Table for Monolithic Scope.
    - Symbol Table for Nested Scope
    - Symbol Table for Data Aggregates
    - Symbol Table for Classes.
    - Enforcing Static Typing Rules.
    - Enforcing Static Type Safety.
    - Enforcing Polymorphic Type Safety.
    - Computing Static Expression Types.
    - Automatic Type Promotion.
- Interpreting input sentences.
    - Syntax Directed Interpreter.
    - Tree Based Interpreter.
    - Stack-Based Bytecode Interpreter.
    - Register-Based Bytecode Interpreter.
- Translate one language to another.
    - Syntax Directed Translator

*An Interpreter simulates a hardware enviroment in the machine hence the virtual machine name*

- The best way to build a language application is to start with the end in mind, figure out the information you need in order to generate the output, that tells you what the final stage before the generator computes, then figure what that stage needs all the way back to the reader.

- 