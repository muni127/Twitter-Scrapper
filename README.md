# Twitter-Scraper
Scrape and stream in real-time Twitter for content matching the search query provided. 
No api authentication required.


# Installing

[Download](https://nodejs.org/en/download/) and install Node.js on your computer then clone this project to a folder on your file system.

# Usage

Run the following from the project's folder.

```shell
npm start
```
### Result
```shell
[v_v] Accordo Bot: initialising

[0_0] Accordo Bot: Scanning http://twitter.com/i/search/timeline?f=tweets&q=accordo.com%20OR%20url%3Aaccordo.com%20-%22accordo%20com%22&src=typd&include_entities=true&include_available_features=true&max_position=

...

[X_X] Accordo Bot: Item blacklisted Id: 1083714710372008000
 L->  Illegal phrase matched: "accordo.com."
 
...

[^_^] Accordo Bot: Saved item: 1055576833087823872

[o_O] Accordo Bot: Collecting images for item: 1055572621268414464

[o_O] Accordo Bot: Collecting images for item: 1055572621268414464

...

[^_^] Accordo Bot: Results processed
[^_^] Accordo Bot: Scrape complete

[v_v] Accordo Bot: Sleeping for 10000 ms
```
All content will be saved to the "results" folder where it was executed, each seperated by the bot's name, created time and Twitter Id.

# Bot search parameters
Set the robot's query parameters in server.ts
```ts
import * as Configs from './configs';
import * as FileSystem from 'fs-extra';

import { ScraperBot } from './scraper-bot';
import { TwitterUtils } from './twitter';

console.log('\x1Bc'); // clear the console

// create result storage folder if not already exists
if (!FileSystem.existsSync(Configs.appConfigs.saveLocation)) {
    FileSystem.mkdirSync(Configs.appConfigs.saveLocation);
}

// create a bot with a name 'Accordo Bot' that scrapes all content that mentions 'accordo.com'
// the bot will save images as well as data collected
// very easy to add extra functionalities such as saving videos 
let bot = new ScraperBot('Accordo Bot');
bot.query = TwitterUtils.generateSearchQuery({
    term: 'accordo.com OR',
    operators: [
        {
            operator: 'url:',
            value: 'accordo.com'
        },
        {
            operator: '-',
            value: '"accordo com"'
        },
    ]
});
// eliminate incorrect urls matched
bot.blacklistedUrlPhrases = ['accordo-com', 'accordo.com.'];
bot.start();

// the bots are asynchorous so you can have multiple running at the same time
// scraping different search parameters.
// Bot names need to be unique. This helps sorting results.
let bot2 = new ScraperBot('Accordo Group Bot');
bot2.query = TwitterUtils.generateSearchQuery({
    operators: [
        {
            operator: '@',
            value: 'accordogroup'
        }
    ]
});
bot2.start();
```
## Results
``` shell
[0_0] Accordo Bot: Scanning http://twitter.com/i/search/timeline?f=tweets&q=accordo.com%20url%3Aaccordo.com%20-%22accordo%20com%22&src=typd&include_entities=1&include_available_features=1&max_position=

[0_0] Accordo Group Bot: Scanning http://twitter.com/i/search/timeline?f=tweets&q=%40accordogroup&src=typd&include_entities=1&include_available_features=1&max_position=thGAVUV0VFVBaCwKfpjMDS4R0WgMCtjZrA0pgeEjUAFQAlAAA%3D
```
## Learn more about how to build Twitter's basic query [here](https://developer.twitter.com/en/docs/tweets/rules-and-filtering/overview/standard-operators)

# Configuration
Change the bot's storage location and stream interval in ./src/configs.ts
```ts
/**
 *  Stores all bot configurations
 *  Secrets should not be included in file !!!
 */
export const appConfigs = {
    twitterSearchUrl: 'http://twitter.com/i/search/timeline',
    saveLocation: './results', // root location for saving bot results
    stream_interval_ms: 10000 // stream every 10 seconds
};
```
# Compiling to EXE for any environment
Recommended to install [pkg](https://github.com/zeit/pkg)
```shell
npm install -g pkg
tsc
```
Go to the bin folder after the .js files have been complied in the ./bin folder
```shell
pkg server.js
```
This will generate three executables. Linux, Macos and Windows
