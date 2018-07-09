### Substratum(SUB)


https://etherscan.io/address/0x12480e24eb5bec1a9d4369cab6a80cad3c0a377a#code


```javascript
    function mintToken(address target, uint256 mintedAmount) onlyOwner {
        balanceOf[target] += mintedAmount;
        totalSupply += mintedAmount;
        Transfer(0, this, mintedAmount);
        Transfer(this, target, mintedAmount);
    }

```

The contract of Substratum(SUB) token is called MyAdvancedToken.

#### Vulnerability 1

The SUB token could be arbitrary minted by its creator in function mintToken(). The `balanceOf[target]` and `mintedAmount` are a defined as uint, so oprator '+' would definitely result in an integer overflow.

The owner of the contract could use `mintToken()` to arbitary amout of (for example 0x8000000000000000000000000000000000000000000000000000000000000000 Wei) subconcurrency SUB to an arbitary user.

If the owner of the contract `mintToken()` another 0x8000000000000000000000000000000000000000000000000000000000000000 SUB to the user again,  integer overflow happened which make `balanceOf` this user to be 0.

And actually the owner of the contract could control the balance of an arbitary user to be an aribitary value. 

#### Vulnerability 2

```javascript
function sell(uint256 amount) {
	if (balanceOf[msg.sender] < amount ) throw;        // checks if the sender has enough to sell
    	balanceOf[this] += amount;                         // adds the amount to owners balance
    	balanceOf[msg.sender] -= amount;                   // subtracts the amount from sellers balance
	if (!msg.sender.send(amount * sellPrice)) {        // sends ether to the seller. Its important
    	throw;                                         // to do this last to avoid recursion attacks
	} else {
    	Transfer(msg.sender, this, amount);            // executes an event reflecting on the change
	}
}
```

This contract could be used to trade SUB tokens using ETHs. However there exists a integer overflow in function `sell()`.

The owner of the contract could set `sellPrice` and `buyPrice` using `setPrices()` function. For example, after some transactions, the owner could set sellPrice to 0x8000000000000000000000000000000000000000000000000000000000000000. When some user wanted to sell 2 SUBs for ETHs back，`amount * sellPrice` equals 0. Thus the seller gave out 2 SUBs but none ETHs was retrieved. The contract still kept it, which makes this token unsafe.