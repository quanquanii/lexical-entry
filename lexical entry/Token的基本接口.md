
# 代币的基本接口

代币是数字资产，它们可以在区块链上进行创建、转移和管理。代币通常遵循特定的标准接口，使它们在不同的应用和平台上具有互操作性。下面是代币的基本接口和示例代码。

## ERC-20 标准接口

ERC-20是以太坊上最常见的代币标准，定义了代币合约的基本接口和功能。以下是ERC-20代币的主要接口函数：

- `balanceOf(address _owner)`：查询地址 `_owner` 拥有的代币余额。

- `transfer(address _to, uint256 _value)`：将代币从合约调用者的地址转账给 `_to` 地址。

- `approve(address _spender, uint256 _value)`：允许地址 `_spender` 代表合约调用者花费不超过 `_value` 代币。

- `transferFrom(address _from, address _to, uint256 _value)`：从地址 `_from` 转账代币到地址 `_to`，前提是地址 `_from` 已经授权了转账。

以下是一个简单的ERC-20代币合约示例，包括上述接口函数：

```solidity
pragma solidity ^0.8.0;

contract MyToken {
    string public name = "My Token";
    string public symbol = "MTK";
    uint8 public decimals = 18;
    uint256 public totalSupply = 1000000 * 10**uint256(decimals);

    mapping(address => uint256) balances;
    mapping(address => mapping(address => uint256)) allowances;

    constructor() {
        balances[msg.sender] = totalSupply;
    }

    function balanceOf(address _owner) public view returns (uint256 balance) {
        return balances[_owner];
    }

    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(_to != address(0));
        require(balances[msg.sender] >= _value);

        balances[msg.sender] -= _value;
        balances[_to] += _value;

        emit Transfer(msg.sender, _to, _value);

        return true;
    }

    function approve(address _spender, uint256 _value) public returns (bool success) {
        allowances[msg.sender][_spender] = _value;

        emit Approval(msg.sender, _spender, _value);

        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
        require(_to != address(0));
        require(balances[_from] >= _value);
        require(allowances[_from][msg.sender] >= _value);

        balances[_from] -= _value;
        balances[_to] += _value;
        allowances[_from][msg.sender] -= _value;

        emit Transfer(_from, _to, _value);

        return true;
    }
    
    event Transfer(address indexed _from, address indexed _to, uint256 _value);
    event Approval(address indexed _owner, address indexed _spender, uint256 _value);
}
```

这个示例合约包括了ERC-20标准接口的实现，允许代币的转账、授权和查询余额。

## ERC-721 标准接口

ERC-721代币是一种非同质化代币，每个代币都具有唯一性。ERC-721代币合约包括以下核心接口函数：

- `balanceOf(address _owner)`：查询地址 `_owner` 拥有的ERC-721代币数量。

- `ownerOf(uint256 _tokenId)`：查询ERC-721代币的所有者。

- `transferFrom(address _from, address _to, uint256 _tokenId)`：从地址 `_from` 转移ERC-721代币到地址 `_to`。

以下是一个简单的ERC-721代币合约示例，包括上述接口函数：

```solidity
pragma solidity ^0.8.0;

contract MyNFT {
    mapping(uint256 => address) private tokenOwners;
    mapping(address => uint256) private ownedTokensCount;

    function balanceOf(address _owner) public view returns (uint256) {
        return ownedTokensCount[_owner];
    }

    function ownerOf(uint256 _tokenId) public view returns (address) {
        return tokenOwners[_tokenId];
    }

    function transferFrom(address _from, address _to, uint256 _tokenId) public {
        require(_from == msg.sender || msg.sender == approved[_tokenId]);
        require(_to != address(0));

        tokenOwners[_tokenId] = _to;
        ownedTokensCount[_from] -= 1;
        ownedTokensCount[_to] += 1;

        emit Transfer(_from, _to, _tokenId);
    }

    // 其他实现函数...
    
    event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
    mapping(uint256 => address) public approved;
}
```

这个示例合约实现了ERC-721标准接口，允许非

同质化代币的所有权转移。

## ERC-1155 标准接口

ERC-1155是一种多代币标准，支持同质化和非同质化代币。它包括以下核心接口函数：

- `balanceOf(address _owner, uint256 _id)`：查询地址 `_owner` 拥有的特定代币（根据 `_id` 区分）数量。

- `balanceOfBatch(address[] calldata _owners, uint256[] calldata _ids)`：查询多个地址拥有的多个代币的数量。

- `safeTransferFrom(address _from, address _to, uint256 _id, uint256 _value, bytes calldata _data)`：从地址 `_from` 安全地将代币转移给地址 `_to`。

以下是一个简单的ERC-1155代币合约示例，包括上述接口函数：

```solidity
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";

contract MyMultiToken is ERC1155 {
    constructor() ERC1155("https://myapi.com/api/token/{id}.json") {
        _mint(msg.sender, 0, 10, "");
        _mint(msg.sender, 1, 20, "");
    }
}
```

这个示例合约继承了OpenZeppelin的ERC1155合约，实现了ERC-1155标准接口，允许创建多种代币。

## 总结

代币的基本接口取决于其类型和标准，如ERC-20、ERC-721和ERC-1155。这些接口定义了代币的基本功能，包括查询余额、转移代币、授权转移等。在实际开发中，开发者可以根据不同的需求选择合适的代币标准和接口，以创建各种类型的数字资产。
