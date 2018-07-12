### BANK_SAFE


https://etherscan.io/address/0x561EAc93C92360949Ab1f1403323E6db345CBF31#code

```javascript
function Collect(uint _am)
    public
    payable
    {
        if(balances[msg.sender]>=MinSum && balances[msg.sender]>=_am)
        {
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                Log.AddMessage(msg.sender,_am,"Collect");
            }
        }
    }

```

There exists a reentrancy vulnerability in above function `Collect()`. An attacker could use this vulnerability to stole all of the ETH remains in this smart contract.

Statement `balances[msg.sender]-=_am;` is at the behind of `call.value()`, which is the root cause of this vulnerability. Instead a development method called `check-effects-interactions` should be implemented.



### Similar bugs

##### PIGGY_BANK

https://etherscan.io/address/0xe610af01f92f19679327715b426c35849c47c657#code

```javascript
function Collect(uint _am)
    public
    payable
    {
        if(Accounts[msg.sender]>=MinSum && _am<=Accounts[msg.sender] && block.number>putBlock)
        {
            if(msg.sender.call.value(_am)())
            {
                Accounts[msg.sender]-=_am;
                LogFile.AddMessage(msg.sender,_am,"Collect");
            }
        }
    }
```



##### Accrual_account

https://etherscan.io/address/0x251848c3Fc50274f5Fda449919F083Dc937B48B2#code

```javascript
function FundTransfer(uint _am, bytes32 _operation, address _to, address _feeToAdr) 
    payable
    {
       if(msg.sender != address(this)) throw;
       if(_operation=="In")
       {
           FundsMove(msg.value,"In",_to);
           investors[_to] += _am;
       }
       else
       {
           uint amTotransfer = 0;
           if(_to==_feeToAdr)
           {
               amTotransfer=_am;
           }
           else
           {
               amTotransfer=_am/100*99;
               investors[_feeToAdr]+=_am-amTotransfer;
           }
           if(_to.call.value(_am)()==false)throw;
           investors[_to] -= _am;
           FundsMove(_am, "Out", _to);
       }
    }
```



#####  Private_Bank

https://etherscan.io/address/0xb5e1b1ee15c6fa0e48fce100125569d430f1bd12#code

```javascript
function CashOut(uint _am)
    {
        if(_am<=balances[msg.sender])
        {
            
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                TransferLog.AddMessage(msg.sender,_am,"CashOut");
            }
        }
    }
```



##### MONEY_BOX

https://etherscan.io/address/0xbe4041d55db380c5ae9d4a9b9703f1ed4e7e3888#code

```javascript
function Collect(uint _am)
    public
    payable
    {
        var acc = Acc[msg.sender];
        if( acc.balance>=MinSum && acc.balance>=_am && now>acc.unlockTime)
        {
            if(msg.sender.call.value(_am)())
            {
                acc.balance-=_am;
                LogFile.AddMessage(msg.sender,_am,"Collect");
            }
        }
    }
```



##### PENNY_BY_PENNY

https://etherscan.io/address/0x96EDBe868531BD23a6C05e9d0C424ea64fb1B78B#code

```javascript
function Collect(uint _am)
    public
    payable
    {
        var acc = Acc[msg.sender];
        if( acc.balance>=MinSum && acc.balance>=_am && now>acc.unlockTime)
        {
            if(msg.sender.call.value(_am)())
            {
                acc.balance-=_am;
                Log.AddMessage(msg.sender,_am,"Collect");
            }
        }
    }
```



##### ETH_FUND

https://etherscan.io/address/0x941d225236464a25eb18076df7da6a91d0f95e9e#code

```javascript
function CashOut(uint _am)
    public
    payable
    {
        if(_am<=balances[msg.sender]&&block.number>lastBlock)
        {
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                TransferLog.AddMessage(msg.sender,_am,"CashOut");
            }
        }
    }
```



##### PrivateDeposit

https://etherscan.io/address/0x7a8721a9d64c74da899424c1b52acbf58ddc9782#code

```javascript
function CashOut(uint _am)
    {
        if(_am<=balances[msg.sender])
        {            
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                TransferLog.AddMessage(msg.sender,_am,"CashOut");
            }
        }
    }
```



##### BountyHunt

https://etherscan.io/address/0xb5766f61911f8b520b0e938aae100834aa3048c6#code

```javascript
function claimBounty() preventTheft {
    uint balance = bountyAmount[msg.sender];
    if (msg.sender.call.value(balance)()) {
      totalBountyAmount -= balance;
      bountyAmount[msg.sender] = 0;
    }   
  }
```



##### ManagedAccount

https://etherscan.io/address/0xd2e16a20dd7b1ae54fb0312209784478d069c7b0#code

````javascript
function refund() noEther {
        if (now > closingTime && !isFueled) {
            if (extraBalance.balance >= extraBalance.accumulatedInput())
                extraBalance.payOut(address(this), extraBalance.accumulatedInput());

            if (msg.sender.call.value(weiGiven[msg.sender])()) {
                Refund(msg.sender, weiGiven[msg.sender]);
                totalSupply -= balances[msg.sender];
                balances[msg.sender] = 0;
                weiGiven[msg.sender] = 0;
            }
        }
    }
````



##### ETH_VAULT

https://etherscan.io/address/0x8c7777c45481dba411450c228cb692ac3d550344#code

```javascript
function CashOut(uint _am)
    public
    payable
    {
        if(_am<=balances[msg.sender])
        {
            
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                TransferLog.AddMessage(msg.sender,_am,"CashOut");
            }
        }
    }
```



##### COIN_BOX

https://etherscan.io/address/0xdd17afae8a3dd1936d1113998900447ab9aa9bc0#code

```javascript
function Collect(uint _am)
    public
    payable
    {
        var acc = Acc[msg.sender];
        if( acc.balance>=MinSum && acc.balance>=_am && now>acc.unlockTime)
        {
            if(msg.sender.call.value(_am)())
            {
                acc.balance-=_am;
                LogFile.AddMessage(msg.sender,_am,"Collect");
            }
        }
    }
```



##### Private_accumulation_fund

https://etherscan.io/address/0xa5D6acCc5695327F65Cbf38DA29198DF53EFDcf0#code

```javascript
function CashOut(uint _am)
    public
    payable
    {
        if(_am<=balances[msg.sender])
        {
            
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                TransferLog.AddMessage(msg.sender,_am,"CashOut");
            }
        }
    }
```



##### DEP_BANK

https://etherscan.io/address/0xaae1f51cf3339f18b6d3f3bdc75a5facd744b0b8#code

```javascript
function Collect(uint _am)
    public
    payable
    {
        if(balances[msg.sender]>=MinSum && balances[msg.sender]>=_am)
        {
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                Log.AddMessage(msg.sender,_am,"Collect");
            }
        }
    }
```



##### PRIVATE_ETH_CELL

https://etherscan.io/address/0x4e73B32ed6c35F570686b89848E5F39F20ECC106#code

```javascript
function Collect(uint _am)
    public
    payable
    {
        if(balances[msg.sender]>=MinSum && balances[msg.sender]>=_am)
        {
            if(msg.sender.call.value(_am)())
            {
                balances[msg.sender]-=_am;
                Log.AddMessage(msg.sender,_am,"Collect");
            }
        }
    }
```

