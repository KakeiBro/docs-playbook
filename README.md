# Docs Playbook

Following best practices for the usage of Antora Docs, this is a repository solely 
focused on configurations and playbook material that later comes together in order 
to generate a static site containing all the documentation in regards to the KakeiBro 
application

## Master Playbook

The `master-playbook.yml` file contains all the parameters that are used to build 
the static site. It makes use of the `https://github.com/KakeiBro/docs` repository 
as the content source.

## Creation of the project

Following the tutorial at [How to install Antora](https://docs.antora.org/antora/latest/install/install-antora/) 
what was done was applying a local installation approach, so a `package-json` manifest 
contains all the data neccesary to install the neccesary npm packages in order to 
run the antora tool and generate the static site.

Firstly the trimmed down version of the manifest was created with
````
node -e "fs.writeFileSync('package.json', '{}')"
````

And later the command to install antora was run:

````
pnpm i -D -E @antora/cli@3.
pnpm i -D -E @antora/site-generator@3.1
````

**NOTE:** [PNPM](https://pnpm.io/) was used for this setup, so it is recommended 
to also leverage the same tool.

## Generating the docs site

Following the tutorial at [Antora Docs](https://docs.antora.org/antora/latest/run-antora/) 
we can run a `npx antora master-playbook.yml` command and have everything being generated 
at the same directory level. (Don't forget to run a `pnpm install` first otherwise 
the tool won't be installed yet).

## About custom UI bundle

A custom ui bundle was created based on the default Antora bundle. This is specifically 
done so that the navbar does not have useless buttons that lead nowhere. And it 
now shows a Github Icon that redirects to the Github Org KakeiBro's repositories 
site.

As part of the workflow with some other extension that needs to take place, it's 
advisable to simply decompress the `ui.zip` file, edit whatever needs to be edited, 
and once that's ready, recreate a different zip file, a Windows Command for it 
is:

````
Compress-Archive -Path "./ui/*" -DestinationPath "./ui.zip" -CompressionLevel Optimal
````

Always commit a `.zip` file, never the plain folder, `.gitignore` has a path marked 
for `ui/` but that's definitely not enough for a circumvent to take place.

Both `master-playbook.yml` and `local-playbook.yml` will now attempt to always search 
for the `ui.zip` file at the executing path.

## Asciidoctor Diagram

In order to add diagram support some packages had to be installed:

````
gem install asciidoctor-diagram-plantuml

gem install asciidoctor-diagram -v 1.5.15
````

https://rubygems.org/gems/asciidoctor-diagram/versions/1.5.15

https://rubygems.org/gems/asciidoctor-diagram-plantuml

Remember that asciidoc uses Ruby for its backend and its extensions. Also we require 
Java, the JDK is a dependency for plant-uml.

And with this you can run the raw asciidoctor gem and then tack onto it the different 
diagram generators. Since it supports many: 

https://docs.asciidoctor.org/diagram-extension/latest/diagram_types/plantuml/

### Antora integration

However, for our purposes we will leverage the online generator tool, plus a small 
npm package.

````
pnpm i -D -E asciidoctor-plantuml
````

This is a package made by a member that from the looks of it left Antora and Asciidoctor 
one year ago. Hence it's marked as deprecated.

https://libraries.io/npm/asciidoctor-plantuml

There's a por of asciidoctor for node/js: https://github.com/npm/www/issues/42

However it moved from a deprecated package: https://libraries.io/npm/asciidoctor.js

To https://www.npmjs.com/package/@asciidoctor/core

Looking at bundle size `asciidoctor-plantuml` in comparisson to `asciidoctor/core` 
core is bigger (12 Mbs) however core lacks any sort of indication as to how to 
leverage it and possible extensions.

Here are some discussions as to what happened with the package and its implementation 
decisions:

https://gitlab.com/antora/antora/-/issues/138

But overall, since a lot of repos have been taken down, and things are extremely cryptic, 
the instructions and repositories at:

[Blog Post](https://blog.anoff.io/2019-04-19-antora-plantuml-customize-ui/)

Is what was used. This meant adding some small declarations to the playbook:

````yml
asciidoc:
  attributes:
    experimental: ''
    idprefix: ''
    idseparator: '-'
    page-pagination: ''
    plantuml-server-url: http://www.plantuml.com/plantuml
    plantuml-fetch-diagram: true
  extensions:
  - asciidoctor-plantuml
````

And this in consequence made it possible to start using the syntax for diagrams and 
then in the background rendering everything by communicating with the online service:

````asciidoc
= Welcome to KakeiBro's Docs
:navtitle: Introduction

[plantuml, building-block-lvl2-media, png]
....
skinparam monochrome true
title Home Entertainment - Building Block Lvl2 - Media Manager
footer ACME Inc. %date[yyyy]%

component "Media Manager" as mediaman {
  node "Sat Receiver" as sat
  node "Streaming Service" as streaming
}
....
````

This in the end generates a plantuml diagram correctly. And without too many hitches, 
without having to commit binaries and keeping `diagrams-as-code`.

However it's worth noting that as best practices it would be good to analyze approaches 
that modularize the diagram declarations as partials (separated files), and perhaps 
see if the same syntax can be expanded by cross-referencing between files and so on:

https://stackoverflow.com/questions/74675170/how-to-include-plantuml-in-asciidoc-with-antora

https://docs.antora.org/antora/latest/page/include-an-example/

https://docs.antora.org/antora/latest/page/resource-id/

_In short:_ There are many options to generate diagrams through syntax, kroki, plantuml, 
siren, and many others, and there are tons of tools that can help with that both on the 
npm side (js) and on gems (ruby). It's up to you and what the API allows for you to 
do in order to leverage them correctly.

## Shiki Highlighter

Whilst Antora makes use of `Highlight.js` by default, this project required for 
something a bit more refined, besides, Antora itself took away some of the previous 
integrations that allowed for it to extend however you wanted, without documenting 
the process of putting them back in.

And so, an amazing solution was found in the form of an extension: 
[Antora Shiki Extension](https://github.com/lask79/antora-shiki-extension?tab=readme-ov-file).

The GitHub page is amazing, it details almost everything you need in order to set 
up the extension. It is basically a node package that gets installed inside of the 
playbook repository, and it can be extended to cover multiple languages that you 
have to download from [Shiki's Repo](https://github.com/shikijs/textmate-grammars-themes/tree/main/packages/tm-grammars). 

For this specific project 3 grammars were added under `shiki_syntaxes/`:

- csharp
- tsx
- typescript

Theme's are listed [here](https://github.com/shikijs/textmate-grammars-themes/tree/main/packages/tm-themes) 
however, some of them are not inside of the `shiki` package (this could be due to 
the shiki version being outdated), but in order to not run into errors, it's best 
to look into the `node_modules` folder to see what themes are available.

There's a slight _bug_ with the extension and that's whenever adding line explanations 
to source code blocks (e.g: <1>) The background melds together with the font color of 
these numbers, at least with `nord`, `monokai`. But one theme that doesn't completely 
show them as invisible is `rose-pine`. So the project makes use of it.

### Anatomy of a language registration

````yml
register_languages:
  - id: 'typescript'
    scope_name: 'source.ts'
    grammar_path: ./shiki_syntaxes/typescript.json
    alias: ['ts']
````

What you need to know is that the `id` value can be mined from the grammar's repo 
`README`. From that same repo you only need to search for the respective language's 
`json` file, download it, and point to it in the relative path to the playbook. 
The `scope_name` is something detailed inside of the same `json` file. The `id` 
can be used to write in code blocks as the identifier for what language and grammar 
to use, however `alias` can be used to add different identifiers that will be matched 
to the same grammar. E.g: If you put an alias such as `great`, and that is under 
the language for `typescript` if you write in a code block `[source, great]`, then 
the highlighting will be of `typescript`.