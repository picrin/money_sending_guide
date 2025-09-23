# Send real money with Python in 49 currencies

This guide shows how to send real money (such as USD, EUR, GBP, [and 46 other currencies](https://wise.com/help/articles/2571907/what-currencies-can-i-send-to-and-from)) using the Python programming language. Money is sent using bank transfers between bank accounts, and currencies are converted at competitive rates for international transfers (so one can hold money in e.g. EUR and pay it out in e.g. USD).

This guide is ideal for technical founders, who can quickly use the source code provided to build their own collection of in-house scripts for automated processing of invoices, automating payroll or automating other financial processes in their startup. I will publish a less "techy" and more "productised" approach suitable for non-technical founders in future editions of this newsletter, so make sure to subscribe!

This guide provides Python code in a Jupyter notebook and requires creation of an account with the fintech [Wise](https://wise.com), which handles Know Your Customer (KYC) and Anti Money Laundering (AML) checks and provides compliance with money transmission regulations. An account can be created by residents of [eligible regions and countries](https://wise.com/help/articles/2813542/where-do-i-need-to-live-to-hold-money-with-wise). It usually takes less than 24 hours to open an account.

An advantage of this solution over Plaid, Stripe and similar providers is that Wise is much cheaper to use, and many transfers do not even trigger a commission of any sort: your recipient gets the exact amount you sent them. US bank transfers are an exception due to Wise's cost of using the underlying US banking infrastructure, and there are small currency conversion fees for international transfers. Overall Wise provides a rock-solid financial product, which even allows users to earn interest on their balances. I used Wise as my main "bank account" in my most recent startup, and I'm always happy to recommend it!

# Set up your Wise account

Follow Wise's [onboarding process](https://wise.com/register). Choose "Business account", which is also available for freelancers. Wise doesn't usually require a proof of business entity from freelancers, which makes this step relatively straightforward. You must choose "Business account" because payment automation features (which we require) are not available for personal accounts. You will be asked to prove your identity and you will need to wait (usually less than 24 hours) for it to be verified.

Once you are verified, top up your Wise balance with some real money (just go to the balance in your preferred currency and click "Add"). 10 USD/GBP/EUR or equivalent should be enough for testing. You can use a bank transfer, a debit/credit card or ApplePay to top up. American bank transfers are pretty slow, so if that's where you live maybe use that debit card instead. British bank transfers are the fastest, although European bank transfers are sometimes also fast.

# Configure the Wise API token

First, create a local copy of this repository:

`git clone https://github.com/picrin/money_sending_guide.git`

Next, create a Wise API token.

Log in to Wise and choose "Integration and tools" in the account settings, then choose "API tokens", and finally choose "Add new token". Create a "Full access" token, copy the token string and paste it in place of `<your_token_goes_here>` in your local copy of the `.env` file.

Do not commit changes to your .env file or otherwise share your Wise API token as this can cause theft of your money.

# Set up the private key

We need to generate a private key for Strong Customer Authentication (SCA). This is a bit more tricky, as Wise requires a private key in a different format than our script. Luckily `openssl` has all the required key handling functionality.

Generate a private key (in PKCS#8 format by default):

`openssl genpkey -algorithm RSA -out private.pem -pkeyopt rsa_keygen_bits:2048`

Export public key for Wise (in the required PKCS#8 format):

`openssl pkey -in private.pem -pubout -out public.pem`

Verify that file starts with `-----BEGIN PUBLIC KEY-----` accepted by Wise.

Copy the public key into Wise by navigating to "Integration and tools" > "API tokens" > "Manage public keys".

Convert private key for the Python script (to PKCS#1 format):

`openssl pkey -in private.pem -out private_rsa.pem -traditional`

Verify that the file starts with `-----BEGIN RSA PRIVATE KEY-----`, which works with the script expecting PKCS#1.

Do not commit or share your private keys, as this could publicly leak them causing theft of your money.

# Create a virtualenv and install dependencies

We are now ready to transfer real money with Python!

Create a virtual environment

`virtualenv venv`

Activate it

`source venv/bin/activate`

and install dependencies

`pip install -r requirements.txt`

Start the jupyter server

`jupyter notebook`

And open the web browser to run the `send_money.ipynb` Python script that transfers a small amount of money to the author of this guide :)

