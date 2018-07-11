

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





### Similar vulnerabilities

#####  Malaysia coins(Xmc)

https://etherscan.io/address/0x8eeb73befd9285d686d6cf7649ed657514a714cb#code

```javascript
function transferAny(address[] addresses, uint256 _value) {
    require(_value <= balances[msg.sender]);
    for (uint i = 0; i < addresses.length; i++) {
        balances[msg.sender] -= _value;
        balances[addresses[i]] += _value;
        Transfer(msg.sender, addresses[i], _value);
    }
}
```

#####  MP3 Coin(MP3)

https://etherscan.io/token/0x5ad6dc0a267693c8a14ac9ff2a29c7d63a3d96c2

```javascript
function distribute(address[] _addresses, uint[] _amounts) public returns(bool success) {
    require(_addresses.length < 256 && _addresses.length == _amounts.length);
    uint totalAmount;
    for (uint a = 0; a < _amounts.length; a++) {
        totalAmount += _amounts[a];
    }
    require(totalAmount > 0 && balances[msg.sender] >= totalAmount);
    balances[msg.sender] -= totalAmount;
    for (uint b = 0; b < _addresses.length; b++) {
        if (_amounts[b] > 0) {
            balances[_addresses[b]] += _amounts[b];
            Transfer(msg.sender, _addresses[b], _amounts[b]);
        }
    }
    return true;
}
```

##### Neo Genesis Token(NGT)

https://etherscan.io/address/0x8505185d59ca2b1381a727c3429098c62b18e71a#code

```javascript
function multipleTransfer(address[] _to, uint256 _value) public returns(bool success) {
    require(_value * _to.length > 0);
    require(balances[msg.sender] >= _value * _to.length);
    balances[msg.sender] -= _value * _to.length;
    for (uint256 i = 0; i < _to.length; ++i) {
        balances[_to[i]] += _value;
        Transfer(msg.sender, _to[i], _value);
    }
    return true;
}
```

##### WeMediaChain(WMC)

https://etherscan.io/address/0x47427a4921bdf2b3ac75ecf53193a5265af4a12f#code

```javascript
function batchTransfer(address[] _receivers, uint256 _value) public {
    uint cnt = _receivers.length;
    uint256 amount = uint256(cnt) * _value;
    require(cnt > 0 && cnt <= 10);
    require(_value > 0 && balanceOf[msg.sender] >= amount);
    require(!frozenAccount[msg.sender]);

    balanceOf[msg.sender] -= amount;
    for (uint i = 0; i < cnt; i++) {
        balanceOf[_receivers[i]] += _value;
        Transfer(msg.sender, _receivers[i], _value);
    }
}
```

##### Rocket Coin(XRC)

https://etherscan.io/address/0x6fc9c554c2363805673f18b3a2b1912cce8bfb8a

```javascript
function multiTransfer(address[] _addresses, uint[] _amounts) public returns(bool success) {
    require(_addresses.length <= 100 && _addresses.length == _amounts.length);
    uint totalAmount;
    for (uint a = 0; a < _amounts.length; a++) {
        totalAmount += _amounts[a];
    }
    require(totalAmount > 0 && balances[msg.sender] >= totalAmount);
    balances[msg.sender] -= totalAmount;
    for (uint b = 0; b < _addresses.length; b++) {
        if (_amounts[b] > 0) {
            balances[_addresses[b]] += _amounts[b];
            Transfer(msg.sender, _addresses[b], _amounts[b]);
        }
    }
    return true;
}
```

##### GlobeCoin(GLB)

https://etherscan.io/address/0xfe4b3f29ad96f0422b873d47a9848e9920f9ded7#code

```javascript
function transfer_tokens_after_ICO(address[] addresses, uint256 _value) {
    require(_value <= balances[msg.sender]);
    for (uint i = 0; i < addresses.length; i++) {
        balances[msg.sender] -= _value;
        balances[addresses[i]] += _value;
        Transfer(msg.sender, addresses[i], _value);
    }
}
```

