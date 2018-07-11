

### SHARKTECH(SKT)

https://etherscan.io/address/0xf4ac238121585456DeE1096fED287F4d8906D519#code

```javascript
    function batchTransfer(address[] _receivers, uint256 _value) public returns (bool) { 
        uint256 cnt = _receivers.length;
        uint256 amount = cnt * _value;
        if(amount == 0) return false;
        if(balanceOf[msg.sender] < amount) return false;
        balanceOf[msg.sender] -= amount;
        for (uint i = 0; i < cnt; i++) {
            balanceOf[_receivers[i]] += _value;
            Transfer(msg.sender, _receivers[i], _value);
            }
        return true;
    }
```

There exists an integer overflow in function `batchTransfer()`. This function could be called by any user. The contract calculate the total amount of tokens to transfer at first, and then checks that the  amount should not  be zero. But actually the check is useless.

An attacker could transfer tokens to 2 address and 0x8000000000000000000000000000000000000000000000000000000000000001 for each. And result in an integer overflow:

```javascript
0x8000000000000000000000000000000000000000000000000000000000000001 * 0x2  == 0x2
```

This means the two receivers would get this amount of token while the sender spent only 2 tokens. Thus any attacker that has more than one address cuold use this vulnerability to mint tokens without owner's awareness and make himself rich of tokens. Also the attacker could set the balance of any user to any value.

