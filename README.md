# hive.ausbitbank.dev-source
 
This is the main source repository for hive.ausbit.dev

You can find the project announcement post here 
https://hive.ausbit.dev/@ausbitbank/hive-ausbit-dev-an-open-source-hive-block-explorer-post-viewer-and-more

To build and run the development server you'll need to install quasar-cli - Quasar.dev

Once installed, just run `quasar dev -m spa` and a development server will run on http://localhost:8080

Do your thing, then to build it for distribution use `quasar build -m spa`

Pull requests, feedback and issues are welcomed

20231024:New branch 
`yarn quasar dev -m spa`
Got some error messages:
Error: error:0308010C:digital envelope routines::unsupported
Port 8080 only show no running processes and only displays 404 error:
No webpage was found for the web address: https://fluffy-guide-vr5p77gwq4xc747-8080.app.github.dev/
HTTP ERROR 404
From a suggestion at https://techdows.com/2023/09/error-error-0308010-c-digital-envelope-routines-unsupported.html I tried reinstalling yarn. But that produces all the gyp errors I was getting with Cloudflare Pages deployments. So I'll try again with node v14.21.3