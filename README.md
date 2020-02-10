# CyanoBridge

A tool to help mobile dApps communicate with related provider. It wraps the dapi for mobile dapps and send request to 

the provider(native client).

> Notice: Those dapps are opened in the webview of native app, for example ONTO.

## How to use

CyanoBridge can be used as CommonJS/ES6 module or directly referencing in html.

### Install CommonJS/ES module

```
npm install cyanobridge
```

### Import CommonJS

```
var client = require('cyanobridge').client
client.registerClient();
```

### Import ES6 module

```
import { client } from 'cyanobridge'
client.registerClient();
```

### Web require

The file to be referenced is under the `./lib` folder.

```
<script src="./lib/browser.js"></script>

var client = CyanoMobile.client;
client.registerClient();
```


## Usage

Here is a [demo app](https://github.com/ontio-cyano/mobile-dapp-demo).

# Documentation

## 1. Initialisation

### 1.1 Register client

> Please register it at the begining of your project. You can set the client as a global variable.

```
import { client } from 'cyanobridge'
client.registerClient();
```

### 1.2 Get provider

You can use this api to see if there is a provider.

```
import { client } from 'cyanobridge'


try {
    const res = await client.api.provider.getProvider();
    console.log(res)
} catch(err) {
    console.log(err)
}
```



## 2. Account and Identituy

### 2.1 getAccount

Requst the account from cyano provider.

#### Parameters:

`params` Optional. Defines the name and icon of the dapp.

#### Returns:

```
{
    action: 'getAccount',
    "version": "v1.0.0",
    error: 0,
    desc: 'SUCCESS',
    result: '' // User's address in base58 format
}
```



#### Example:

```
import { client } from 'cyanobridge'

const params = {
    dappName: 'My dapp',
    dappIcon: '' // some url points to the dapp icon
}

try {
    const res = await client.api.asset.getAccount(params);
    console.log(res)
} catch(err) {
    console.log(err)
}

```

### 2.2 getIdentity

Request the identity from the cyano provider.

#### Parameters:

`params` Optional. Defines the name and icon of the dapp.

#### Returns:

```
{
    action: 'getIdentity',
    "version": "v1.0.0",
    error: 0,
    desc: 'SUCCESS',
    result: '' // User's ONT ID
}
```



#### Example:

```
const params = {
    dappName: 'My dapp',
    dappIcon: '' // some url points to the dapp icon
}
try {
   const res = await client.api.identity.getIdentity(params);
    console.log(res)
} catch(err) {
    console.log(err)
}
```

### 2.3 Transfer assets

Transfer ONT/ONG

#### Parameters:

`params` 

#### Returns:

```
{
    action: 'invoke',
    "version": "v1.0.0",
    error: 0,
    desc: 'SUCCESS',
    result: '' // Transaction hash
}
```



#### Example:

```
const params = {
    from: 'AdLUBSSHUuFaak9j169hiamXUmPuCTnaRz',// Sender's address
    to: 'AXK2KtCfcJnSMyRzSwTuwTKgNrtx5aXfFX', // Receiver's address
    asset: 'ONG', // Asset type. ONT or ONG
    amount: 1000000000,// Amount to send.The value can be 'number' or 'string'
    gasPrice: 500,
    gasLimit: 20000
}
try {
   const res = await client.api.asset.transfer(params);
    console.log(res)
} catch(err) {
    console.log(err)
}
```



### 2.4 Register ONT ID

Register ONT ID. 

> The ONT ID should be 'did:ont' + user's address
> The register would fail if the ONT ID has been registered.

#### Parameters:

`params` 

#### Returns:

```
{
    action: 'invoke',
    "version": "v1.0.0",
    error: 0,
    desc: 'SUCCESS',
    result: '' // Transaction hash
}
```



#### Example:

```
const params = {
    ontid: 'did:ont:AdLUBSSHUuFaak9j169hiamXUmPuCTnaRz',// ONT ID to register
    publicKey: '0212f54726f1eb9322519b4afafb7c38394f32807866ff1989a93e6c423cff1186', // Publickey of ONT ID
    payer: 'AdLUBSSHUuFaak9j169hiamXUmPuCTnaRz', // Payer of the transaction
    gasPrice: 500,
    gasLimit: 20000
}
try {
   const res = await client.api.identity.registerOntId(params);
    console.log(res)
} catch(err) {
    console.log(err)
}
```

### 2.5 getDDO

Query the DDO of ONT ID.

#### Parameters:

`params` 

#### Returns:

```
{
    action: 'invoke',
    "version": "v1.0.0",
    error: 0,
    desc: 'SUCCESS',
    result: '' // Serialized DDO.Should use ontology's sdk to deserialzie it to get plain 				  // object.
}
```



#### Example:

```
const ontid = 'did:ont:AdLUBSSHUuFaak9j169hiamXUmPuCTnaRz',// ONT ID
try {
   const res = await client.api.identity.getDDO(ontid);
    console.log(res)
} catch(err) {
    console.log(err)
}
```



## 3. login

Request to sign a message and get the signature from cyano provider.

#### Parameters:

Parameter is  a JSON object. It contains:

`type` Define use account or identity to sign message

`dappName` dApp's name

`dappIcon` A url that points to the dApp's icon.

`message` Message to sign.

`expired` Timestamp for expire date.

`callback` Callback url for dapp.

#### Returns:

```
{
	"action": "login",
	"version": "v1.0.0",
	"params": {
		"type": "ontid or account",
		"user": "did:ont:AUEKhXNsoAT27HJwwqFGbpRy8QLHUMBMPz",
		"message": "helloworld",
		"publickey": "0205c8fff4b1d21f4b2ec3b48cf88004e38402933d7e914b2a0eda0de15e73ba61",
		"signature": "01abd7ea9d79c857cd838cabbbaad3efb44a6fc4f5a5ef52ea8461d6c055b8a7cf324d1a58962988709705cefe40df5b26e88af3ca387ec5036ec7f5e6640a1754"
	}
}
```



#### Example:

```
const params = {
    type: 'account',// account or identity that will sign the message
    dappName: 'My dapp', // dapp's name
    dappIcon: 'http://mydapp.com/icon.png', // some url that points to the dapp's icon
    message: 'test message', // message sent from dapp that will be signed by native client
    expired: new Date('2019-01-01').getTime(), // expired date of login
    callback: '' // callback url of dapp
}
let res;
try {
    res = await client.api.message.login(params);
    console.log(res)
}catch(err) {
    console.log(err)
}
// verify signature here

```



## 4. Invoke smart contract

Request to inovke some smart contract methods with cyano provider.

#### Parameters:

Parameter are as below:

`scriptHash` Script hash of smart contract

`operation` Method to invoke

`args` Array of parameters. Each parameter should contain `type` and `value`

All supported parameters  see [Supported parameters list](#supported-parameters-list)

`gasPrice` Gas price

`gasLimit` Gas limit

`payer` base58 format address of the payer.

`config` Optional config.

> `config` can contains 
>
> {	
>
> ​	login: bool // define if user logined
>
> ​	message:  string // some message to show in cyano provider
>
> ​	url: string // callback url for dapp
>
> }

#### Returns:

```
{
	"action": "invoke",
	"version": "v1.0.0",
	"error": 0,
	"desc": "SUCCESS",
	"result": "txhash" // Transaction hash
}
```

> dApp then use the Ontology restful api to query the event of this invokation.  Restful api details are [here](http://dev-docs.ont.io/#/docs-en/API/02-restful_api)

#### Example:

```
const scriptHash = 'cd948340ffcf11d4f5494140c93885583110f3e9';
const operation = 'test'
const args = [
    {
        type: 'String',
        value: 'helloworld'
    }
]
const gasPrice = 500;
const gasLimit = 20000;
const payer = 'AecaeSEBkt5GcBCxwz1F41TvdjX3dnKBkJ'
const config = {
    "login": true,
    "message": "invoke smart contract test",
    "url": ""  
}
const params = {
          scriptHash,
          operation,
          args,
          gasPrice,
          gasLimit,
          payer,
          config
        }
try {
   const res = await client.api.smartContract.invoke(params);
   } catch(err) {
    console.log(err)
}

```

## 5. InvokeRead smart contract

Request to pre-exe some smart contract methods with cyano provider.The parameters are similar with invoke smart contract.

#### Example:

```
const scriptHash = 'b5a1f2cd4e27b7453111a2f5eb737714ead8fded';
      const operation = 'balanceOf';
      const args = [{
          "name": "account",
          "type" : 'Address',
          "value": "AQf4Mzu1YJrhz9f3aRkkwSm9n3qhXGSh4p"
        }]
        const gasPrice = 500;
        const gasLimit = 20000;
        const config = {
          "login": true,
          "message": "invoke read smart contract test",
          "url": ""
        }
        const params = {
          scriptHash,
          operation,
          args,
          gasPrice,
          gasLimit,
          config
        }
        try{
          const res = await client.api.smartContract.invokeRead(params);
          console.log('dapp receive: ' + JSON.stringify(res));
          this.invokeReadRes = JSON.stringify(res);
        }catch(err) {
          console.log(err);
        }
```

## 6. Invoke smart contract without password

Request to invoke some smart contract method without password. The provider will ask user to enter password to sign  transaction at the first time. User can invoke the same smart contract method without password after that.

#### Example:

```
const scriptHash = 'cd948340ffcf11d4f5494140c93885583110f3e9';
const operation = 'test'
const args = [
    {
        type: 'String',
        value: 'helloworld'
    }
]
const gasPrice = 500;
const gasLimit = 20000;
const payer = 'AecaeSEBkt5GcBCxwz1F41TvdjX3dnKBkJ'
const config = {
    "login": true,
    "message": "invoke smart contract test",
    "url": ""  
}
const params = {
          scriptHash,
          operation,
          args,
          gasPrice,
          gasLimit,
          payer,
          config
        }
try {
   const res = await client.api.smartContract.invokePasswordFree(params);
   } catch(err) {
    console.log(err)
}
```

## 7. Helper methods for generating QrCode

### 7.1 login

#### Parameters:

Parameter is a `JSON` object:

```
{
    "type": "ontid or account",
    "dappName": "dapp Name",
    "dappIcon": "dapp Icon",
    "message": "helloworld",
    "expire": 1546415363, // QR Code expire time
    "callback": "http://101.132.193.149:4027/blockchain/v1/common/test-onto-login"
 }
```

#### Return:

Return JSON string to generate QrCode.

#### Example:

```
const params = {
    "type": "account",
    "dappName": "dapp Name",
    "dappIcon": "dapp Icon",
    "message": "helloworld",
    "expire": 1546415363, // QR Code expire time
    "callback": "http://101.132.193.149:4027/blockchain/v1/common/test-onto-login"
}
const result = client.api.qrcode.login(params)
// use result to generate QrCode
```

### 7.2 Invoke smart contract

#### Parameters:

Parameter is a `JSON` object:

```
"params": {
		"login": true, // Define if the invoke needs login status
		"callback": "http://101.132.193.149:4027/invoke/callback",	// Url of callback	
		"qrcodeUrl": 		"http://101.132.193.149:4027/qrcode/AUr5QUfeBADq6BMY6Tp5yuMsUNGpsD7nLZ"
		//Url of parameters
	}
```

#### Return:

Return JSON string to generate QrCode.

#### Example:

```
const params = {
		"login": true,
		"callback": "http://101.132.193.149:4027/invoke/callback",		
		"qrcodeUrl": "http://101.132.193.149:4027/qrcode/AUr5QUfeBADq6BMY6Tp5yuMsUNGpsD7nLZ"
	}
const result = client.api.qrcode.invoke(params);
```

## 8 State Channel

### 8.1 Login

This api is used for login State channel client. The provider will require user to input the password of the account, and return a new private key for login.

#### Example:

````
const result = client.api.stateChannel.login();

````

## Supported parameters list

#### Integer

```
{
    type: "Integer",
    value: 10
}
```

#### Long

```
{
    type: 'Long',
    value: '12345678901234567'
}
```

#### String

```
{
    type: 'String',
    value: 'hello world'
}
```

#### ByteArray

```
{
    type: 'ByteArray',
    value: 'abababab'
}
```

#### Address

```
{
    type: 'Address',
    value: 'AXK2KtCfcJnSMyRzSwTuwTKgNrtx5aXfFX'
}
```

### Array

```
{
    type: 'Array',
    value: [
        {
            type: 'String',
            value: 'hello world'
        }
    ]
}
```

#### Map

```
{
    type: 'Map',
    value: {
        key1: {
            type: 'Integer',
            value: 100
        }
    }
}
```



# Build

## Required Tools and Dependencies

* Node
* Npm

## Install

```
npm install cyanobridge --save
```

## Development build

```
npm run build:dev
```

The packaged result will be in `/lib`

## Production build

```
npm run build:prod
```

The packaged result will be in `/lib`

