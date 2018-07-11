### Decentralized Accessible Content Chain(DACC)

https://etherscan.io/token/0xd6ab82d6eac69b7cdb5f900bf690e5f3fbe307aa

```javascript
    function airdrop(address[] _DACusers,uint256[] _values) isRunning public {
        require(_DACusers.length > 0);
        require(_DACusers.length == _values.length);
        uint256 amount = 0;
        uint i = 0;
        for (i = 0; i < _DACusers.length; i++) {
            require(amount + _values[i] >= amount);
            amount += _values[i];  
        }
        require(balanceOf[msg.sender] >= amount);
        balanceOf[msg.sender] -= amount;
        for (i = 0; i < _DACusers.length; i++) {
            require(balanceOf[_DACusers[i]] + _values[i] >= balanceOf[_DACusers[i]]);
            balanceOf[_DACusers[i]] += _values[i];
            emit Transfer(msg.sender, _DACusers[i], _values[i]);
        }
    }
```

There exists an integer overflow in function `airdrop()`. This function could be called by any user as batch transfer. The contract calculate the total amount of tokens to transfer at first, and then checks that the  amount should be not excceed the amount the caller had. But actually the check is useless.

An attacker could transfer tokens to 2 address and 0x8000000000000000000000000000000000000000000000000000000000000000 for each. And result in an integer overflow:

```javascript
0x8000000000000000000000000000000000000000000000000000000000000000 + 0x8000000000000000000000000000000000000000000000000000000000000000 == 0x0
```

This means the two receivers would get this amount of token while the sender spent none tokens. Thus any attacker that has more than one address cuold use this vulnerability to mint tokens without owners' awareness and make himself rich of tokens.



