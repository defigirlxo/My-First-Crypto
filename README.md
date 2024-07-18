# Launch your first crypto token on Hedera in just 5 minutes using only Javascript
This is a repo with all the code you need to launch a crypto token using only JavaScript. Follow these steps to launch your crypto token on Hedera:
## 1. Set up your environment:
Create a new directory and navigate to it with the following command in your terminal:

``` mkdir My-First-Token && cd My-First-Token```

Initialise nodes.js by running the following command:

```npm init -y```

Install the Hedera Javascript SDK & dotenv by running the following command:

```npm install --save @hashgraph/sdk dotenv```

Create an index.js file

```touch index.js```

## Step 2: Create a Testnet Account

Create a Hedera Testnet account through the [developer portal](https://portal.hedera.com/register). Take note of your DER encoded private key and your account ID.

## Step 3: Create your .env file

Assign your account ID and DER encoded private key that you generated through the Hedera portal to the following variables:

```jsx
MY_ACCOUNT_ID=0.0.1234
MY_PRIVATE_KEY=302e020100300506032b657004220420ed5a93073.....
```
## Step 4: Configure your client and launch your token!

``` jsx
require("dotenv").config(); // Load environment variables from .env file
const { Hbar, Client, AccountId, PrivateKey, TokenCreateTransaction } = require("@hashgraph/sdk");

async function environmentSetup() {
  // Grab your Hedera testnet account ID and private key from the .env file
  const myAccountId = process.env.MY_ACCOUNT_ID, myPrivateKey = process.env.MY_PRIVATE_KEY;
  
  // Throw an error if environment variables are not found
  if (!myAccountId || !myPrivateKey) throw new Error("Environment variables MY_ACCOUNT_ID and MY_PRIVATE_KEY must be present");

  // Create your Hedera Testnet client and set the operator
  const client = Client.forTestnet();
  client.setOperator(AccountId.fromString(myAccountId), PrivateKey.fromStringDer(myPrivateKey))
        .setDefaultMaxTransactionFee(new Hbar(100)) // Set default max transaction fee
        .setDefaultMaxQueryPayment(new Hbar(50)); // Set max payment for queries

  // Create a new token with specified properties
  const transaction = new TokenCreateTransaction()
    .setTokenName("MyFirstCrypto") // Set token name
    .setTokenSymbol("MFC") // Set token symbol
    .setTreasuryAccountId(AccountId.fromString(myAccountId)) // Set treasury account
    .setInitialSupply(5000) // Set initial supply of the token
    .setAdminKey(PrivateKey.fromStringDer(myPrivateKey)) // Set admin key
    .freezeWith(client); // Freeze the transaction for signing

  // Sign the transaction and submit to the Hedera network
  const signedTransaction = await transaction.sign(PrivateKey.fromStringDer(myPrivateKey));
  const transactionResponse = await signedTransaction.execute(client);

  // Request and obtain the receipt of the transaction
  const receipt = await transactionResponse.getReceipt(client);

  // Get and print the new token ID from the receipt
  const tokenId = receipt.tokenId;
  console.log("The new token ID is " + tokenId);
}

// Execute the environment setup function and catch any errors
environmentSetup().catch(console.error);
```

### Run your code to launch your token!


```jsx
node index.js
```
