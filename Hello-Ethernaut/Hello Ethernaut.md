The goal of this level is to familiarise with how to interact with a smart contract through the user agent's developer console. 

After we have setup out Metamask Crypto Wallet and have aqcuired some RinkebyETH Ether, we click "Get New Instance", authorize the transaction through metamask and open the Developer Console in the challenge browser window.

In the Developer Console we follow the steps below, to solve the challenge:
```
> await contract.info()
<- 'You will find what you need in info1().'
> await contract.info1()
<- 'Try info2(), but with "hello" as a parameter.'
> await contract.info2("hello")
<- 'The property infoNum holds the number of the next info method to call.'
> await contract.infoNum()
<- o {negative: 0, words: Array(2), length: 1, red: null}length: 1negative: 0red: nullwords: (2) [42, empty]0: 42length: 2[[Prototype]]: Array(0)[[Prototype]]: Object
> await contract.info42()
<- 'theMethodName is the name of the next method.'
> await contract.theMethodName()
<- 'The method name is method7123949.'
> await contract.method7123949()
<- 'If you know the password, submit it to authenticate().'
> await contract.password()
<- 'ethernaut0'
```

After following the steps above, we click the "Submit Instance" button and authorize the transaction again to submit the challenge solution. A successful solution means that the transaction will be confirmed in the Activity log in our metamask wallet menu.



