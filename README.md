# TCalculator
a Telegram Calculator Mini App that can perform basic mathematical operations like addition, subtraction, multiplication, and division

<u>Note that this project hasn't server side part</u>
### Info
- Related Telegram Bot: [@TCalculatorBot](https://t.me/TCalculatorBot)
- Mini App URL: [TCalculator](https://t.me/TCalculatorBot/TCalculator)

### Features

- Wrriten in [Vue Js](https://vuejs.org/)
- Perform calculations involving very large integers (up to 20 digits showing)
- Perform decimals at a high level of precision (up to 64 decimal places)

### Requirements

- Familiarity with the command line
- [Node.js](https://nodejs.org/) v16 or newer.

### Installation

- Install [Node.js](https://nodejs.org/) v16 or newer
- Clone the repo - `git clone https://github.com/ArashYounesi/TCalculator`
- Go to project directory - `cd project_folder`
- Install project dependencies — `npm install`.
- Launch the app — `npm run dev`, it will become available at [http://localhost:5173](http://localhost:5173/).

### Deployment - Lunch on Telegram
#### requirements 
- Telegram bot
- Server to deploy project on it
- Domian connected to server

In order to use this project as a [Telegram Mini App](https://core.telegram.org/bots/webapps) some requirements needs:

- Build project - `npm run build` (dist directory will created)
- Upload files in `dist` folder to server
- Create a new telegram bot using [BotFather](https://t.me/botfather) (`/newbot`)
- Create a new web app based on created bot using [BotFather](https://t.me/botfather) (`/newapp`)
- Lunch using link BotFather gives you

You can also edit your Mini App url, name, ... using [BotFather](https://t.me/botfather) (`/myapps`)
For detailed information about Telegram Bot and Mini App please visit [Telegram Mini App](https://core.telegram.org/bots/webapps)
### License

[MIT](https://choosealicense.com/licenses/mit/)
