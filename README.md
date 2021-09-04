You may find useful information in the overview / post mortem slides (unfortunately in German as of now).

Due to other commitments I can no longer maintain the further development of this skill. (e.g. adjust to updated MS Graph API, support more languages, full 2-way-sync,...)

# Introduction
Alexa skill that allows integration with Microsoft To-Do

[Alexa Skill Developer Console](https://developer.amazon.com/alexa/console/ask/measure/amzn1.ask.skill.6afdb0f6-5d54-418a-81b1-7e4a0df32060/live/all/summary?aggPeriod=by_hour&end=1522713600000&start=1522108800000&timeInterval=last-7-days)

[Alexa Skill Page (German)](https://www.amazon.de/B-Dev-Wunder-To-Do/dp/B07BHLPLGD/ref=cm_cr_arp_d_product_top?ie=UTF8)

[AWS Lambda Function](https://eu-west-1.console.aws.amazon.com/lambda/home?region=eu-west-1#/functions/WunderToDo?tab=graph)

## Building locally
Remember to `npm install` first.

# Account Linking Setup

## Azure Application
Needed to authenticate with Microsoft Account for accessing ToDo list items

Authentication guide for Microsoft Graph: https://docs.microsoft.com/en-us/graph/auth/auth-concepts

### App registration creation
1. Azure Active Directory -> App registrations -> + New registration
2. Create name.
3. Account types: Accounts in any organization and personal Microsoft Accounts
4. Redirect URIs added later

### App authentication settings
1. Add platform -> Web
2. Redirect URIs come from Linked Account Settings in Alexa dashboard
3. Implicit grant and hybrid flows: Leave both boxes unchecked

### API Permissions
1. Add permission for Microsoft Graph Tasks.ReadWrite

## Alexa Account Linking Configuration
1. Within skill, go to Build tab, and select "Models" on far left menu
2. Click left menu item for Account Linking
3. Enable linking
4. Choose authentication method of Auth Code Grant (not Implicit)
5. Access token and Authorization URLs can be found in App registration (App overview -> Endpoints [top bar])
6. Client Id: Application (Client) Id in App registration overview
7. Client secret: Create a secret in App registration "Certificates & secrets" section.
8. Authentication type: In Request Body
9. Scopes: Add scope of `https://graph.microsoft.com/.default`
10. Copy Amazon-provided redirect URLs into Redirect URIs of registered App (step 2 in [App authentication settings](#app-authentication-settings))

# Other Information

## Ask CLI
CLI for Alexa 'Ask' API

https://developer.amazon.com/en-US/docs/alexa/smapi/quick-start-alexa-skills-kit-command-line-interface.html

`npm install ask-cli` (Do this in the *lambda\custom* directory, where package.json already exists)

`ask <command>`  --or--  `.\node_modules\.bin\ask ` --or--  `.\node_modules\.bin\ask.cmd `

Configure (login) after installing with `ask configure`

## Enable List Skill
https://developer.amazon.com/en-US/docs/alexa/smapi/steps-to-create-a-list-skill.html


1. Download skill manifest

    PowerShell: `.\node_modules\.bin\ask smapi get-skill-manifest -s <skill-id> -g development | Out-File -Encoding ascii manifest.json`

2. Add required properties (in guide above)
3. Update manifest

    PowerShell: `.\node_modules\.bin\ask smapi update-skill-manifest -s <skill-id> -g development --debug --manifest "file:manifest.json"`

**Note**: After updating, some settings (like account linking) are no longer available in the web dashboard.

## Update Account Linking Client Secret
1. Download linking info

    PowerShell: `.\node_modules\.bin\ask smapi get-account-linking-info -s <skill-id> -g development | Out-File -Encoding ascii acct-linking.json`

    Skill Id is retrieved from Alexa Skills dashboard: https://developer.amazon.com/alexa/console/ask

2. Change "accountLinkingResponse" in file to "accountLinkingRequest"
3. Add property
``` js
    "clientSecret": <secret>,
```
4. Send update

    PowerShell: `.\node_modules\.bin\ask smapi update-account-linking-info -s <skill-id> -g development --debug --account-linking-request "file:acct-linking.json"`