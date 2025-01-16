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