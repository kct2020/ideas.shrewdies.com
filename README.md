# hive.ausbitbank.dev-source
 
This is the main source repository for hive.ausbit.dev

You can find the project announcement post here 
https://hive.ausbit.dev/@ausbitbank/hive-ausbit-dev-an-open-source-hive-block-explorer-post-viewer-and-more

To build and run the development server you'll need to install quasar-cli - Quasar.dev

Once installed, just run `quasar dev -m spa` and a development server will run on http://localhost:8080

Do your thing, then to build it for distribution use `quasar build -m spa`

Pull requests, feedback and issues are welcomed

20231024 
`nvm install 14.21.3 #install previous version of choice

nvm alias default 14.21.3 #set it to default

nvm use default #use the new default as active version globally.`

`yarn quasar dev -m spa`
Still won't run in dev mode because of codemirror dependencies, but suggests:
`npm install --save codemirror/addon/display/fullscreen.js codemirror/addon/display/placeholder.js codemirror/addon/edit/continuelist.js codemirror/addon/mode/overlay.js codemirror/addon/selection/mark-selection.js codemirror/mode/gfm/gfm.js codemirror/mode/markdown/markdown.js codemirror/mode/xml/xml.js`
Unfortunately, that doesn't work. So I'll try the solution in https://juejin.cn/post/7200219190598615095
`npm uninstall simplemde
npm i simplemde-w`
then replace the place where simplemde is introduced with simplemde-w. For 
example
`jsCopy code// import 'simplemde/dist/simplemde.min.css'
// import SimpleMDE from 'simplemde'
import 'simplemde-w/dist/simplemde.min.css'
import SimpleMDE from 'simplemde-w'`

So `  @import '../statics/simplemde-theme-dark.min.css';`
becomes `  @import '../statics/simplemde-theme-dark.min.css';`

But now, the uninstall and install fails. So I'll try with the other option in the article:

>Delete the current node_modules folder 
and then re-install dependencies with npm i. 
Remember to only use npm to install dependencies each time.

So:
1. Delete lock & modules
2. `npm i simplemde`

All this has got messy. So I'll retain branch for now, but restart from new branch of master then:
1. `npm i simplemde` & check version in package-lock.json
2. `nvm install 14.21.3 #install previous version of choice
nvm alias default 14.21.3 #set it to default
nvm use default #use the new default as active version globally.`
3. `yarn quasar dev -m spa`
