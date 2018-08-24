---
title: "Solution to Homework #1: Writing a Smart Contract"
---

1. Which data has be stored in the blockchain and what data types should be used?
* an ```uint8``` data type to store the karma value
* an ```uint256``` data type to store the time
* a ```bool``` data type to store the initialization status

2. What functions have to be implemented in the smart contract? (No detailed implementation is required here. Input and output are sufficient.)

* a ```updateKarma(address holder)``` function to calculate and update the karma of holder
* a ```sendKarma(address recipient)``` function to send karma to a recipient
* an ```initialize()``` function to initialize the sender's account

3. Try to implement the smart contract in Solidity using the [Remix Solidity IDE][remix].

```javascript
pragma solidity ^0.4.24;    // specifies the version of the solidity compiler

contract Karma is mortal{

    struct Holder{
        bool initialized;
        uint8 karma;
        uint256 lastUpdated;
    }
    
    mapping (address => Holder) public karmaHolders;    // map each ethereum address to a Holder struct and make this mapping publically accessible
    
    function initialize() public payable {
        require(!isInitialized(msg.sender), "The address of the sender cannot be initialized already.");
        
        karmaHolders[msg.sender].initialized = true;
        karmaHolders[msg.sender].karma = 25;    // starting value as an incentive for using the app
        karmaHolders[msg.sender].lastUpdated = now;
    }

    function isInitialized(address _address) public view returns (bool) {
    	// This function is not mandatory and shows how to return a boolean value.
    	// it has a view modifier which an thus only accesses the current contract state without changing it. In this case, no gas is charged for the function call.
        return karmaHolders[_address].initialized;
    }
    
    function sendKarma(address recipient) external payable {
        require(msg.sender != recipient, "The address of the sender and the recipient cannot be identical.");
        
        updateKarma(recipient);    // checks the initialization of recipient internally
        if(karmaHolders[recipient].karma < 255) {
            karmaHolders[recipient].karma += 1;
        }
    }
    
    function updateKarma(address holder) public payable {
        require(isInitialized(holder),"The address of the holder must be initialized first.");
            
        uint256 karmaDecay = (now-karmaHolders[holder].lastUpdated)/(1 minutes);	//seconds and days are also available as constants
        
        if (karmaDecay > uint256(karmaHolders[holder].karma)) {
            karmaHolders[holder].lastUpdated = now; 
            karmaHolders[holder].karma = 0;
            
        } else if (karmaDecay > 0) { // important to prevent stop of the karmaDecay by frequent updating
            karmaHolders[holder].lastUpdated = now; 
            karmaHolders[holder].karma -= uint8(karmaDecay);
        }
    }
    
    function updateKarma() public payable {
    	// this function is provided as syntactic sugar to update the sender's address
        updateKarma(msg.sender);
    }
}
```

## Advanced
The modifier ```mortal``` is a self-defined contract that has to be implemented before the ```Karma``` contract to inherit its functions:
```javascript
pragma solidity ^0.4.24;
contract mortal is owned {
    // This contract inherits the `onlyOwner` modifier from
    // `owned` and applies it to the `close` function, which
    // causes that calls to `close` only have an effect if
    // they are made by the stored owner.
    function close() public onlyOwner {
        selfdestruct(owner);
    }
}
```
The ```owend``` modifier is another self-defined contract implemented before the ```mortal```:
```javascript
pragma solidity ^0.4.24;
contract owned {
    constructor() public { owner = msg.sender; }
    address owner;

    // This contract only defines a modifier but does not use
    // it: it will be used in derived contracts.
    // The function body is inserted where the special symbol
    // `_;` in the definition of a modifier appears.
    // This means that if the owner calls this function, the
    // function is executed and otherwise, an exception is
    // thrown.
    modifier onlyOwner {
        require(
            msg.sender == owner,
            "Only owner can call this function."
        );
        _;
    }
}
```
