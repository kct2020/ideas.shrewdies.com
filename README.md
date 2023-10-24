# hive.ausbitbank.dev-source
 
This is the main source repository for hive.ausbit.dev

You can find the project announcement post here 
https://hive.ausbit.dev/@ausbitbank/hive-ausbit-dev-an-open-source-hive-block-explorer-post-viewer-and-more

To build and run the development server you'll need to install quasar-cli - Quasar.dev

Once installed, just run `quasar dev -m spa` and a development server will run on http://localhost:8080

Do your thing, then to build it for distribution use `quasar build -m spa`

Pull requests, feedback and issues are welcomed

##20231024
1. `npm i simplemde` & check version in package-lock.json
2. `nvm install 14.21.3 #install previous version of choice
nvm alias default 14.21.3 #set it to default
nvm use default #use the new default as active version globally.`
3. `yarn quasar dev -m spa`

Step 1 gave warnings but on this run I have not tried to run fixes:
`102 vulnerabilities (1 low, 60 moderate, 33 high, 8 critical)

To address issues that do not require attention, run:
  npm audit fix

To address all issues possible (including breaking changes), run:
  npm audit fix --force
`

quasar repeats errors:
`These dependencies were not found:

* codemirror/addon/display/fullscreen.js in ./node_modules/simplemde/src/js/simplemde.js
* codemirror/addon/display/placeholder.js in ./node_modules/simplemde/src/js/simplemde.js
* codemirror/addon/edit/continuelist.js in ./node_modules/simplemde/src/js/simplemde.js
* codemirror/addon/mode/overlay.js in ./node_modules/simplemde/src/js/simplemde.js
* codemirror/addon/selection/mark-selection.js in ./node_modules/simplemde/src/js/simplemde.js
* codemirror/mode/gfm/gfm.js in ./node_modules/simplemde/src/js/simplemde.js
* codemirror/mode/markdown/markdown.js in ./node_modules/simplemde/src/js/simplemde.js
* codemirror/mode/xml/xml.js in ./node_modules/simplemde/src/js/simplemde.js

To install them, you can run: npm install --save codemirror/addon/display/fullscreen.js codemirror/addon/display/placeholder.js codemirror/addon/edit/continuelist.js codemirror/addon/mode/overlay.js codemirror/addon/selection/mark-selection.js codemirror/mode/gfm/gfm.js codemirror/mode/markdown/markdown.js codemirror/mode/xml/xml.js`

So, still messy!
I'm going to retry from scratch without "vue-simplemde".