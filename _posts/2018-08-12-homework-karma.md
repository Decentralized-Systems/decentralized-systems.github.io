---
layout: post
title:  "Homework: Writing a Smart Contract"
date:   2018-08-12 18:00:00 +0200
categories: homework
---

Imagine a decentralized social media app in which the users can send each other Karma. In this app, Karma is a symbol for appreciation and gratitude. 

* A user account is associated with a relative karma balance ranging from 0 to 255.
* A Karma value of 0 symbolizes neutral karma whereas the value of 255 symbolizes the best possible karma.\
* If user A sends karma to user B, the karma value of user B is incremented by a fixed amount whereas the value of user A does not change.\
* If a user has not recieved new karma for a fixed period of time, the karma value should decay exponentially towards zero over time .

The mechanics described above need to be translated into a smart contract.

1. Which data has be stored in the blockchain and what data types should be used?
2. What functions have to be implemented in the smart contract? (No detailed implementation is required here. Input and output are sufficient.)
3. Try to implement the smart contract using the [Remix Solidity IDE][remix].

[remix]: https://remix.ethereum.org/
