### YiTongCoin



https://etherscan.io/address/0xdf07fa1b102c00124e96f18ea612bbbe553f50e1#code


```javascript
    /// @notice Create `mintedAmount` tokens and send it to `target`
    /// @param target Address to receive the tokens
    /// @param mintedAmount the amount of tokens it will receive
    function mintToken(address target, uint256 mintedAmount) onlyOwner public {
        balanceOf[target] += mintedAmount;
        totalSupply += mintedAmount;
        Transfer(0, this, mintedAmount);
        Transfer(this, target, mintedAmount);
    }
```



The YiTongCoin could be arbitrary minted by its creator in function mintToken(). The balanceOf[target] and mintedAmount are a defined as uint, so oprator '+' would definitely result in an integer overflow.



Simulated on Remix:

![](./1.png)

The owner of the contract could mintToken arbitary amout of (for example 0x8000000000000000000000000000000000000000000000000000000000000000 Wei) subconcurrency YiTongCoin to an arbitary user.



![](./2.png)



If the owner of the contract mintToken another 0x8000000000000000000000000000000000000000000000000000000000000000 YiTongCoin to the user again,  integer overflow happened which make balanceOf this user to be 0.

And actually the owner of the contract could control the balance of an arbitary user to be an aribitary value. 

 