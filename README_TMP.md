# SPFx Compatibility List
https://learn.microsoft.com/en-us/sharepoint/dev/spfx/compatibility

# SPFx Steps
https://github.com/pnp/sp-dev-fx-extensions/tree/main/samples/jquery-application-toastr

1. `sudo apt install bzip2`
2. `mkdir git`
3. `cd git`
4. `git clone https://github.com/pnp/sp-dev-fx-extensions.git`
5. `cd ~/`
6. `cp -aR ~/git/sp-dev-fx-extensions/samples/jquery-application-toastr ~/`
7. `sudo curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash`
8. `source ~/.bashrc`
9. `cd ~/jquery-application-toastr`
10. `nvm install 8`
11. `nvm use 8`
12. `npm install -g gulp gulp-cli`
13. `npm install npm@4 --save-dev` 
14. Confirm you are using Nodejs version 8 and NPM version 6 `node -v ; npm -v`
15. `npm install` you will see the following Warning.
`npm WARN notsup Unsupported engine for karma@0.13.22: wanted: {"node":"0.10 || 0.12 || 4 || 5"} (current: {"node":"8.17.0","npm":"6.13.4"})
npm WARN notsup Not compatible with your version of node/npm: karma@0.13.22`
16. `npm install` run npm install again, since the first npm install installs the npm@4 in dev packages. 
17. Confirm gulp local version 3.9.1 is available `gulp --version` 
18. `gulp build`
19. `gulp bundle --ship`
20. `gulp package-solution --ship`

Upload ~/jquery-application-toastr/sharepoint/solution/toastr.sppkg to Sharepoint Apps, enable for all sites.
### Sharepoint: Manually create the list

1. Navigate to the Site Contents page and choose **New** > **List**
2. Name the list _**Toast**_ and click **Create**
3. Add and configure the columns as listed below:

Column | Type | Required | Details
--- | --- | --- | ---
Title | Text | Yes |
Message | Text | Yes |
Severity | Choice | Yes | Info, Warning, Error, Success
StartDate | DateTime | Yes | Date and Time, Default =Today
EndDate | DateTime | Yes | Date and Time, Default =Today+7
Frequency | Choice | Yes | Once, Once Per Day, Always
Enabled | Yes/No | | Default = Yes

Add an item to the list. Do not use the same EndDate as the StartDate or it will not show.

### SPFx - Add Custom List to Sharepoint

1. `cd ~/jquery-application-toastr`
2. `nvm install 16`
3. `nvm use 16`
4. `npm install -g @pnp/cli-microsoft365`
5. `m365 login`
Your web browser will open to: https://microsoft.com/devicelogin enter the code provided to login and follow prompts. 
```
~/jquery-application-toastr$ m365 login
"To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code CHHJUMAPW to authenticate."
{
  connectedAs: 'username@sptenant.onmicrosoft.com',
  authType: 'DeviceCode',
  appId: '41354c7f-bd7e-475c-86db-fdb8c937548e',
  appTenant: 'common',
  cloudType: 'Public'
}
```
You are now connected to your Sharepoint site.

6. Update the following command. Replacing --webUrl with the site you want to show Toast notifications on.
Update --clientSideComponentId with your application Id. This is located in
~/jquery-application-toastr/src/extensions/spfxToastr/SpfxToastrApplicationCustomizer.manifest.json - "id". Since we are working off a specific example, you will not need to update it since it will be the same as the current value.
You can also run this command to query the id
```
cat ~/jquery-application-toastr/src/extensions/spfxToastr/SpfxToastrApplicationCustomizer.manifest.json | grep -o '"id": *"[^"]*"'
```

Command to update: 
```
m365 spo customaction add --webUrl https://SPTENANT.sharepoint.com/sites/SampleSite --title "Toastr Notifications" --name "jquery-application-toastr" --location "ClientSideExtension.ApplicationCustomizer" --clientSideComponentId a861c815-e425-416d-9520-04bcdf557e27 --clientSideComponentProperties ''
```

Then run the updated command.

After the command completes, refresh the site. You should see the Toast Notification. 

### Note: On future deploys of the application, you do not need to run the m365 spo customaction command again. 

If you want to create a new build, you will need to revert back to nodejs 8.
```nvm use 8```

To change the list name requirement from Toast to something else
1. edit ~/jquery-application-toastr/src/services/toastService/ToastService.ts
Look for lines:
```
//Breaking up the URL like this isn't necessary, but can be easier to update
private static readonly apiEndPoint: string = "_api/web/lists/getbytitle('Toast')/items";
```
replace getbytitle('Toast') with getbytitle('Alerts'), or whatever you want the List name to be.

2. `cd ~/jquery-application-toastr`
3. `nvm use 8`
4. `gulp build`
5. `gulp bundle --ship`
6. `gulp package-solution --ship`
7. Update the previously created SP "Toast" list (See Sharepoint: Manually create the list) name to the new getbytitle('XXXX') entry
8. Upload the updated toastr.sppkg to Sharepoint, you might need to delete it first. 

Sharepoint should now be reading from the updated list name in Sharepoint. 
