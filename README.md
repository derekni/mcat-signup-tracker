# mcat-signup-bot
Bot hosted on EC2 instance to keep checking for MCAT location availabilities. Sends texts and calls to a specified number through Twilio when an appointment is available.

## How to use

To use, first create a Twilio account, and get a Twilio phone number as well. Create a `secrets.js` file that contains the following parameters:
```
exports.TWILIO_ACCOUNT_SID = ...
exports.TWILIO_AUTH_TOKEN = ...
exports.twilio_number = ...
```

Create query objects that you want to search (which include an address, date in the format of "Saturday 25th of March 2023", center indices from the search results, and phone numbers to text and call when there is an opening). Create a Bot from `bot.js`, and pass in the query objects as a list.

#### Running locally

To run this bot locally, you can simply use `node run_bot.js`, but this will only run as long as your computer is constantly on.

#### Setting up an EC2 instance to run this bot (recommended)

You can also run this bot on an EC2 instance, which will allow the bot to continuously run without requiring you to have it on your computer all the time.

##### Creating EC2 instance

- Create an AWS account, go to the EC2 dashboard, and click "Launch instance".
- Fill in the required fields on the page (make sure you choose free tier!)
  - Name: <any name you want for your instance>
  - Application and OS Images: use Ubuntu Server 22.04 LTS
  - Instance type: t2.micro
  - Key pair: create a key pair using RSA with `.pem` format, and save this to your computer
  - Network settings: default (you can change the SSH traffic to only allow traffic from your IP if you want to be safer)
  - Configure storage: default
- Click 'launch instance'
- After the instance has launched, right-click on the instance and click "Connect"
- Click on the "SSH client" tab of instance connecting, and follow the instructions to SSH into the instance in Terminal
  - On the first time using SSH to this instance, it will ask if you are sure. Type in 'yes' and bypass the prompt.
  
##### Transferring bot files to EC2 instance

- Transfer files from local computer using an SFTP software (I use Cyberduck)
  - Fill in server using public DNS (of format `ec2-...-amazonaws.com`)
  - Username is `ubuntu`
  - SSH private key is the `pem` key made earlier
- Transfer the files `bot.js`, `package.json`, `query.js`, `secrets.js`, and `run_bot.js`

##### Installing node and running the bot

- Install node:
'''
$ sudo apt-get update
$ sudo apt-get install nodejs
$ sudo apt-get install npm
$ sudo ln -s `which nodejs` /usr/bin/node
'''
- Install the node packages from `package.json`:
'''
$ npm install
'''
- Install Chromium, which is needed for Puppeteer to run and create a browser page
'''
$ npm install chromium -g
'''
- Install the node module "forever", which will allow you to keep the process running after you've logged out of your EC2 instance
'''
$ sudo npm install forever -g
'''
- Run the bot by calling `forever start run_bot.js`
- Clear runs by calling `forever stopall` and check existing runs by calling `forever list`
