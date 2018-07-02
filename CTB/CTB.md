### CTB


https://etherscan.io/address/0xb57e2ec276460a993393ca1bb2bdae6c8170c73a#code



```javascript
  /// @notice Create `mintedAmount` tokens and send it to `target`
  /// @param target Address to receive the tokens
  /// @param mintedAmount the amount of tokens it will receive
  function mintToken(address target, uint256 mintedAmount) onlyOwner {
      balanceOf[target] += mintedAmount;
      totalSupply += mintedAmount;
      Transfer(0, this, mintedAmount);
      Transfer(this, target, mintedAmount);
  }
```



The CTB token could be arbitrary minted by its creator in function mintToken(). The  balanceOf[target] and mintedAmount are a defined as uint, so oprator '+' would definitely result in an integer overflow.



Simulated on Remix:

![](./1.png)

The owner of the contract could mintToken arbitary amout of (for example 0x8000000000000000000000000000000000000000000000000000000000000000 Wei) subconcurrency CTB to an arbitary user.



![](./2.png)



If the owner of the contract mintToken another 0x8000000000000000000000000000000000000000000000000000000000000000 CTB to the user again,  integer overflow happened which make balanceOf this user to be 0.

And actually the owner of the contract could control the balance of an arbitary user to be an aribitary value. 

 