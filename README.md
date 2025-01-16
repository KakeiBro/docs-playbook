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