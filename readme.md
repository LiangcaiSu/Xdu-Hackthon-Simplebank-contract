#### # 基于以太坊的Simplebank智能合约
#### 需要准备如下的工具
- Ethereum Wallet 
- Browser-Solidity
#### 部署智能合约的主要步骤
1. 搭建geth环境
2. 配置创世块文件ssdw.json
``` javascript 
{
    "nonce": "0x0000000000000042",     
    "timestamp": "0x00",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "extraData": "0x00",     
    "gasLimit": "0x8000000",     
    "difficulty": "0x400",
    "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "coinbase": "0x3333333333333333333333333333333333333333",     
    "alloc": {
     },
     "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    }
}
``` 
>为了提高挖矿的速度，我们将diffculty的值调整为0x400 

3. 启动私有链节点并创建账号，称之为0节点
4. 挖矿
>执行miner.start(),当出现“Successfully seaded new block”时，说明挖矿成功，此时可以去Ethereum Wallet 上账户中币是否增多。
5. 重复2、3步骤建立节点1，并连通节点0和1，建成节点群
> 请确保所使用的创世块文件ssdw.json一致
6. 在以太坊上部署智能合约
>  在节点0中，打开Ethereum Wallet后，点“CONTRACTS”，进入智能合约管理界面，点击“DEPOLY NEW CONTRACT”,开始部署智能合约，选择部署智能合约的账号，并输入智能合约的代码。
值得注意的是，部署后必须进行挖矿智能合约才能被确认。

7. 在其他节点上运行智能合约
> 我们需要在节点0中点“Copy address”，即拷贝该智能合约的地址，然后点“Show Interface”将智能合约的JSON接口全部拷贝出来，在需要运行智能合约的节点1中打开Ethereum-Wallet，打开“CONTRACTS”界面点击“WATCH CONTRACTS”添加这个智能合约。
#### SimpleBank 智能合约的功能
在SimpleBank 智能合约中，任何人都可以向账户进行存款，不过只有账户拥有者才能从账户中进行提款，以及检查余额。

```solidity
pragma solidity ^0.4.18;

/*
 * This is a simplebank contract
 */

contract SimpleBank {
  struct Transaction {
    address fromAddress;
    address toAddress;
    uint amount;
  }
/*建立一个owner，并给予权限*/
  address owner;
  Transaction[] transactions;

  modifier onlyOwner() {
    require(owner == msg.sender);
    _;
  }

  function SimpleBank() public {
    owner = msg.sender;
  }

  function deposit(address _fromAddress, uint _depositAmount) public {
    Transaction memory transaction = Transaction(_fromAddress, owner, _depositAmount);
    transactions.push(transaction);
  }

  function withdraw(address _toAddress, uint _withdrawAmount) public onlyOwner {
    require(balance() >= _withdrawAmount);

    Transaction memory transaction = Transaction(owner, _toAddress, _withdrawAmount);
    transactions.push(transaction);
  }

  function balance() public view returns (uint) {
    uint paidIn = 0;
    uint paidOut = 0;

    for (uint i = 0; i < transactions.length; i++) {
      if (transactions[i].toAddress == owner) {
        paidIn += transactions[i].amount;
      }

      if (transactions[i].fromAddress == owner) {
        paidOut += transactions[i].amount;
      }
    }

    return paidIn - paidOut;
  }

  function() public {
    revert();
  }
}
```
#### 贡献者（队伍）

```
队伍名称：当我们谈论大佬我们在谈些什么
coder：
Dang Feng（党枫）
Suliangcai（苏良才）
Sunxieyang（孙燮阳）
以上均来自2017级软院大一
*Thanks to all those who helped us with our project, including, but not limited to, all the seniors who provided relevant information.
*在此感谢为我们项目提供帮助的所有人，包括但不限于提供相关资料的所有前辈们
```

