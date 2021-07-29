
# hopper

## Table of Contents
- [BackstopJS](#backstopjs)
- [Set up](#set-up)
  - [Cloning the repo.](#cloning-the-repo)
  - [DB-slicer](#db-slicer)
- [Testing](#testing)
  - [Running tests](#running-tests)
  - [Adding new tests](#adding-new-tests)
     - [Hopper Scenario](#hopper-scenario)
     - [Scenario Properties](#scenario-properties)
     - [DB-slicer for new accounts](#db-slicer-for-new-accounts)
  - [Deleting tests](#deleting-tests)
- [Approving](#approving)
- [Environment](#environment)
  - [Environment args](#environment-args)
  - [Adding Environments](#adding-environments)
- [Custom Scripts](#custom-scripts)
  - [npm scripts](#npm-scripts)
  - [onBefore scripts](#onbefore-scripts)
  - [onReady scripts](#onready-scripts)
- [Why "Hopper"](#why-hopper)

## BackstopJS
The Hopper repository makes use of the [BackstopJS github library.](https://github.com/garris/BackstopJS#readme)
Unlike Jest, which is used to write code driven tests, BackstopJS is a config driven library. However, the
Hopper repository makes use of typescript code to create the config file which BackstopJS runs on.

## Set up
### Cloning the repo.
1. First, run `git clone git@github.com:tailwind/hopper.git` within your desired parent directory.
2. Second, run `npm install` within the root of the Hopper directory.

### DB-slicer
If you want to run tests on the local environment you need to run DB-slicer.
1. Type `open ~/.zshrc` and paste the code `export NODE_OPTIONS=--max_old_space_size=4096`<br>
at the bottom of the file to allocate more memory to npm.
2. Run `tw db-slicer -c 55555555` to add the development testing account to your local machine.
3. Run `tw db-slicer -c 55555555` to add the business_no_auto_post account as well.<br>
> Note: If you are looking for instructions on DB-slicing a new account to use on a test, you can find those instructions under [DB-slicer for new accounts](#db-slicer-for-new-accounts)
## Testing
### Running tests
Commands to run:
<pre>
- All tests:             <b>npm run test</b>
- All Communities tests: <b>npm run test:communities</b>
- Homepage test:         <b>npm run test:homepage</b>
- Blog test:             <b>npm run test:blog</b>
</pre><br>
> Note: If you wish to write a custom script to run an individual test or a subset of the tests, see the [npm scripts](#npm-scripts) section.
### Adding new tests
1. Create a new file in the scenarios directory following the naming convention `twYourPageNameHere.ts`.<br>
The default scenario is as follows:
```ts
export const twYourPageNameHereScenario: HopperScenario = (environment) => {
  return {
      label: 'tailwind_your_page',
      url: getURLForEnvironment(environment, '/'),
      misMatchThreshold: 0.1,
  };
};
```
#### Hopper Scenario
- All scenarios must be of HopperScenario type.<br>
- Like how the Hopper repository uses typescript to generate the flat config file which 
BackstopJS runs on, the HopperScenarios generate the configs for the scenarios which Hopper uses to generate that single config file BackstopJS can run. 

#### Scenario Properties
- While label, url, and misMatchThreshold are the only scenario properties all tests must have, BackstopJS allows for many more as described [here](https://github.com/garris/BackstopJS/blob/master/README.md#advanced-scenarios) and listed below are scenario properties unique to Hopper.
```ts
BackstopJS properties overridden by Hopper:
postInteractionWait      // Takes array of selector, string and number values, "after interacting with hoverSelector or clickSelector (optionally accepts wait time in ms. Ideal for use with a click or hover element transition. available with default onReadyScript)" -BackstopJS
Hopper exclusive properties:
noLogin                  // Takes a boolean value to determine if the global onBefore script will run the login function.
loginAccount             // Takes a user credentials type string to select which account the test runs on.
```
- For instructions on the use of custom scripts for tests using the onBefore and onReady scenario properties see the [onBefore scripts](#onBefore-scripts) and [onReady scripts](#onReady-scripts) sections.

2. Add your test to the scenarios: HopperScenario[] array within the backstop.config.ts file as follows:
```ts
const scenarios: HopperScenario[] = [
  twHomepageScenario,
  .
  .
  .
  twYourPageNameHereScenario,
];
```
#### DB-slicer for new accounts
If your newly made test is using an account within the credentials file other than those already in use,
- `development` 
- `business_no_auto_post`<br>

Find the email of the account within the credentials file, log in to the Tailwind admin site, and enter the account email into the searchbar. You'll find an account name followed by the cust_id, directly above the email you entered after searching. For the development account it looks like:<br> `Test_Development (cust_id: 55555555)`.
1. Copy the cust_id number displayed.
2. If you wish to run the test on the local environment, run DB-slicer on your local machine as follows:<br> 
`tw db-slicer -c your_account's__cust_id`<br>
so for the development account with a cust_id of 5555555 you would enter: `tw db-slicer -c 55555555`
3. Add instructions within this readMe to the [DB-slicer](#db-slicer) section to run DB-slicer for this cust_id.<br>

### Deleting tests
In order to delete a test you must do three things:
1. Delete the file within the scenarios directory containing the HopperScenario type function `twYourPageNameHereScenario`
2. Delete the array element `twYourPageNameHereScenario` from the backstop.config.ts files `scenarios: HopperScenario[]` array.
3. Withing the root Hopper directory, find the folder "backstop_data". Within that, find the folder "bitmap_references". There find the file that corrosponds to your test. It should follow the naming convention,`backstop_default_tailwind_your_page_name_0_document_0_default_browser`
If one does not exist it likely means no reference image for your test had been approved, so you don't need to worry about deleting it.

## Approving
Commands to approve:
<pre>
- All tests:             <b>npm run approve</b>
- All Communities tests: <b>npm run approve:communities</b>
- Homepage test:         <b>npm run approve:homepage</b>
- Blog test:             <b>npm run approve:blog</b>
</pre><br>
> Note: If you wish to write a custom script to run an individual test or a subset of the tests, see the [npm scripts](#npm-scripts) section.
## Environment
### Environment args
Run tests on various environments using the argument "--env=", directly followed by the name of the environment, after your npm script. If you do not add args to specify the environment, the test will be run on the default qa-tests environment.<br>
Available environments include:
```
alpha
beta
staging
qa
qa2
qa3
qa-tests
local
production
```
For example, if you are wanting to run all tests on the production environment, in the terminal you would type:<br>
`npm run test -- --env=production`<br>
Or, if you wished to run the homepage test on the local environment you would type:<br>
`npm run test:homepage -- --env=local`

### Adding Environments
To add new environments, add the environment in two places. 
- Within the types folder there is an environment file. In that file add the name of the new environment to the array: <br>`validEnvironments = ['qa-tests', 'production', 'local', ... , 'newEnvironment'];`
- Next, in ScenarioHelpers file within the utils directory, add your environment, followed by its associated link, to the following array:
```ts
const environmentURLs: { [key in Environment]: string } = {
  'qa-tests': 'https://qa-tests-www.tailwindapp.com',
  .
  .
  .
  'newEnvironment': 'https://new-env-www.tailwindapp.com',
};
```

## Custom Scripts
### npm Scripts
Our npm scripts, found in package.json's scripts object, can be made to run test or approve for a select scenario or a subset of the scenarios. We accomplish this using the BackstopJS argument "--filter=" followed by the  scenario_label_regex (for test) or the image_filename_regex (for approve). 
- When adding new scripts, also add your scripts to the lists of scripts in the [Running tests](#running-tests) and [Approving](#approving) sections.
<br><br>
> Ex: The npm script for running test on the scenario "tailwind_your_page" using the command `npm run test:yourPage` is: <br> 
> `"test:yourPage": "npm run test -- --filter=tailwind_your_page",` <br> 
> Ex: The npm script for running approve on the scenario "tailwind_your_page" using the command `npm run approve:yourPage` is: <br> 
> `"test:yourPage": "npm run approve -- --filter=backstop_default_tailwind_your_page_0_document_0_default_browser.png",` <br> 
> Ex: The npm script for running test on two scenarios whose labels are "tailwind_your_page_0" and "tailwind_your_page_1" using the command `npm run test:yourNumberedPages` is:<br>
> `"test:yourNumberedPages": "npm run test -- --filter=tailwind_your_page_"`<br> 
> 
> This means `npm run test:yourNumberedPages` will always run only these two scenarios if no other scenario's label begins with "tailwind_your_page_".
For more information on the BackstopJS "--filter=" argument visit the [BackstopJS documenation.](https://github.com/garris/BackstopJS#generating-test-bitmaps)

### onBefore Scripts
- Global onBefore<br>
The onBefore script is a global default scenario proeperty that runs a script before all other BackstopJS scenario properties. Some Hopper exclusive scenario properties, like `noLogin` and `loginAccount` are exceptions, as they decide if the global onBefore will login and what account it will use, respectively. The global onBefore script, in the lifecycle folder, will run before all scenarios unless a custom onBefore script is specified.
- Custom onBefore<br>
Custom on before scripts, located within the customOnBefore folder, can be given to scenarios by a scenario property of "onBefore": followed by the string containing the relative path to the custom onBefore script.
> Ex: `onBeforeScript: '../../build/lifecycle/customScripts/customOnReady/onReadyHomepage.js',`
### onReady Scripts
- Global onReady<br>
The onReady script is a global default scenario proeperty that runs a script after some scenario properties and before others. See the backstopJS documentation [here](https://github.com/garris/BackstopJS/blob/master/README.md#advanced-scenarios) for more information on precedence. The global onReady script, in the lifecycle folder, will run for all scenarios unless a custom onReady script is specified. However, it is standard practice to import the global onReady script within each custom onReady script, like so:

>```ts
>import onReady from '../../onReady';
>
>const onReadyYourPage = async (page: Page, scenario: Scenario) => {
>	await onReady(page, scenario);
>};
>module.exports = onReadyYourPage;
>```
- Custom onReady<br>
Custom onReady scripts, located within the customOnReady folder, can be given to scenarios by a scenario property of "onReady": followed by the string containing the relative path to the custom onReady script.
> Ex: `onBeforeScript: '../../build/lifecycle/customScripts/customOnReady/onReadyHomepage.js',`

> ## Why "Hopper"?
> American Realist painter Edward Hopper once said “Great art is the outward expression
> of an inner life in the artist, and this inner life will result in his 
> personal vision of the world. ”
> The Hopper directory makes use of Visual Regression tests, checking pixel by pixel, 
> to assure the outward expression corrosponds to your personal vision.
