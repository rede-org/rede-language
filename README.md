# Rede.Language

Rede.Language is intended to be the language analysis library for the Rede programming language. It will analyze Rede source programs and make the results available to other tools, independently of any particular compiler or execution target.

## Responsibilities and boundaries

Rede.Language tokenizes and parses `.rede` source, resolves names and types, validates semantics, and reports structured diagnostics with source locations. It produces an analyzed program representation: a high-level, target-independent intermediate representation (IR) that downstream tools can consume. It also accepts declarations for third-party packages, describing their exported names, types, signatures, and relevant semantics without requiring their internal implementations.

Rede.Language is a standalone sibling of Rede.Compiler. It does not directly depend on Rede.Compiler, bytecode, compiler plugins, or target backends. A Semantic Development Environment (such as Reder) orchestrates the projects and can pass the analyzed program representation directly to Rede.Compiler. Package implementations and target-specific bindings are separate from the declarations used for analysis.

## Proposed analysis workflow

1. **Source and declarations:** Accept source text and the package declarations available to the program. Track source locations for diagnostics and downstream tools.
2. **Lexing:** Convert source text into tokens, reporting lexical errors.
3. **Parsing:** Build a syntax tree from tokens and report syntax errors, recovering where practical.
4. **Semantic analysis:** Resolve names, types, and package symbols; validate language rules; and report semantic errors.
5. **Analysis result:** Return an analyzed program and structured diagnostics through a public API. Ordinary invalid source should produce useful diagnostics rather than require consumers to catch exceptions. The representation and availability of partial results remain design decisions.

Project organization separates source text and locations, lexing, syntax and parsing, semantics, package declarations, diagnostics, and the public analysis API. These are responsibilities rather than mandatory namespaces or projects, so they may be organized differently over time. Shared diagnostic types should carry a code, severity, message, and source location where available.

## Downstream information

The analyzed program representation exposes enough information for consumers to understand the program without repeating language semantic analysis. This includes resolved symbols and their identities, types, operations and operands, control-flow constructs and their language meaning, references to declared packages and their exports, and source locations where available. The contract distinguishes valid results from partial results produced in the presence of errors.

A Semantic Development Environment builds its own semantic model from the analyzed representation and presents it to the user. It can translate user edits into source changes and request updated analysis, repeating the cycle as the program changes. Its model and derived views, such as a control-flow graph, are distinct from the language-level representation; their implementation is a concern of the environment. Source locations and symbol identities can help relate the environment's model to the source.

Rede.Compiler can consume the analyzed representation directly and produce bytecode, while using its own internal representations as needed. On a compilation request, an environment can pass the latest suitable analysis result to the compiler and coordinate target-specific processing of the bytecode and package bindings to produce an executable. The mechanisms for target-specific compilation, linking, or hosting remain separate design decisions.

Package declaration contracts will need to express package identity and version, exported names and types, signatures, and relevant semantics.

## Build

Install the .NET 10 SDK, then run:

```sh
dotnet build Rede.Language.slnx
```
