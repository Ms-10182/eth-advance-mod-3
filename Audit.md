# Smart Contract Audit Report for StorageVictim.sol
Contract: old_Storage_victim.sol (Rewritten to new_Storage_victim.sol)

Solidity Version: 0.4.23 (Rewritten to 0.8.0)

Date: 2024-06-16

Author: Mayank Sharma

### 1. Introduction
This report outlines the security issue and bugs of smart contract written in Solidity 0.4.23 which may cause potential loss  The smart contract is re-written in solidity 0.8.0 with security improvements and bug fixes. Slither is being used for analysis and bug fixing.

### 2. Contract Functionality
The StorageVictim contract allows users to store an amount associated with their address. It provides functions for storing (store), retrieving (getStore), and getting the contract owner (getOwner).

### 3. Security Findings
1) Warnings <br>
i) Warning: Defining constructors as functions with the same name as the contract is deprecated.<br>
    fix: Use ```constructor(...) { ... }```<br><br>
ii) Warning: Variable is declared as a storage pointer.<br>
    fix: Use an explicit `storage` keyword to silence this warning.<br><br>
iii) Warning:  Warning: Uninitialized storage pointer.
    fix: use use ```memory``` keyword 

2) Issue: In the store function, a local variable str of type Storage is declared without initialization. This leads to an "uninitialized pointer" situation where str.user points to the storage location of owner.<br>
Severity: High. This vulnerability can be exploited to overwrite the contract owner's address with the address of a malicious user.<br>
Fixes :Initialize Storage Variable.
Solution: Modify the store function to explicitly initialize the str variable before using its members:
```javascript
function store(uint _amount) public {
       Storage memory str = Storage(msg.sender,_amount);
       storages[msg.sender] = str;
   }
```
4. Rewritten Contract (Solidity 0.8.0)

```javascript
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract StorageVictim {

   address public owner;
   
   struct Storage {
       address user;
       uint amount;
   }

   mapping(address => Storage) public storages;

   constructor() {
       owner = msg.sender;
   }

   function store(uint _amount) public {
       Storage memory str = Storage(msg.sender,_amount);
       storages[msg.sender] = str;
   }
   
   function getStore() public view returns (address, uint) {
       Storage memory str = storages[msg.sender];
       return (str.user, str.amount);
   }
   
   function getOwner() public view returns (address) {
       return owner;
   }
}
```

5. Conclusion

The vulnerability and bugs founds in the smart contract(0.4.x) is solved by applying the fixes provided in <b>```3. Security Findings```</b>. The rewritten contract in Solidity 0.8.0 contains all the fixes.

7. Recommendations
Always initialize storage variables before using their members.
Use up-to-date Solidity versions to benefit from security improvements and best practices.
Use different tools to audi the contract before final deployment.
