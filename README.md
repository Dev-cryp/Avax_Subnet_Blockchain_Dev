# Avalanche Subnet: Building a Defi Protocol Clone using ERC20 

Create and deploy your very own blockchain network using an Avalanche Subnet. This project involves setting up a custom blockchain environment and deploying smart contracts for tokens or vaults. You’ll also develop a simple DeFi-based platform to demonstrate the functionality and potential of your new blockchain network. 🌐🚀 This project is introduced by MetaCrafters and utilizes the Avalanche framework.

### Avalanche Subnet Introduction (Avalanche Documentation)[https://docs.avax.network/subnet]

An Avalanche Subnet is a custom blockchain network built on the Avalanche platform. Subnets allow you to create your own blockchain environment with its own rules, consensus mechanism, and virtual machine, while still benefiting from Avalanche's high throughput and low latency.

### Installation and Deployment Steps

1. **Create an Avalanche Subnet**
   - **Command:** `avalanche subnet create test`
   - **Description:** Initializes a new Avalanche Subnet with the name `test`. This command sets up the basic configuration for your subnet.

2. **Deploy the Subnet**
   - **Command:** `avalanche subnet deploy test`
   - **Description:** Deploys the newly created subnet. This step makes the subnet operational and accessible.

3. **Get Configuration and Implement on MetaMask**
   - **Instructions:** Obtain the subnet configuration details (such as RPC URL, chain ID) from the deployment output or Avalanche dashboard.
   - **MetaMask Setup:**
     1. Open MetaMask and click on the network dropdown.
     2. Select "Add Network."
     3. Enter the configuration details you obtained (Network Name, New RPC URL, Chain ID, Currency Symbol, and Block Explorer URL if available).

4. **Deploy Contracts using Remix**
   - **Instructions:**
     1. Go to [Remix IDE](https://remix.ethereum.org/).
     2. Connect Remix to MetaMask by selecting the “Injected Web3” provider in Remix’s environment settings.
     3. Ensure MetaMask is connected to your Avalanche Subnet.
     4. Write or import your smart contracts in Remix.
     5. Compile and deploy your contracts using Remix.
    
### Token Contract 'ERC20' Explanation 

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract ERC20 {

    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "test";
    string public symbol = "vp";
    uint8 public decimals = 18;

	event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```

This contract is a basic implementation of an ERC20 token in Solidity. The ERC20 standard defines a common interface for ERC20 tokens, which are widely used fungible tokens on the Ethereum blockchain. Here’s a detailed explanation of each component and functionality of the contract:

### Contract: `ERC20`

#### State Variables:
- **`uint public totalSupply`:** 
  - Tracks the total number of tokens that have been issued.
  
- **`mapping(address => uint) public balanceOf`:** 
  - Maps addresses to their respective token balances.
  
- **`mapping(address => mapping(address => uint)) public allowance`:** 
  - Maps an owner address to a spender address and the amount of tokens the spender is allowed to transfer on behalf of the owner.

- **`string public name`:**
  - The name of the token (set to "test").

- **`string public symbol`:**
  - The symbol of the token (set to "vp").

- **`uint8 public decimals`:**
  - The number of decimal places for the token (set to 18).

#### Events:
- **`event Transfer(address indexed from, address indexed to, uint value)`:**
  - Emitted when tokens are transferred from one address to another.

- **`event Approval(address indexed owner, address indexed spender, uint value)`:**
  - Emitted when an owner approves a spender to use a certain amount of tokens on their behalf.

#### Functions:

- **`function transfer(address recipient, uint amount) external returns (bool)`:**
  - Allows the caller to transfer `amount` tokens to the `recipient` address.
  - Updates the balance of the sender and the recipient.
  - Emits the `Transfer` event.
  - Returns `true` if the transfer was successful.
  
  **Example:**
  ```solidity
  balanceOf[msg.sender] -= amount;
  balanceOf[recipient] += amount;
  emit Transfer(msg.sender, recipient, amount);
  ```

- **`function approve(address spender, uint amount) external returns (bool)`:**
  - Allows the caller to approve `spender` to withdraw up to `amount` tokens from their account.
  - Updates the allowance mapping.
  - Emits the `Approval` event.
  - Returns `true` if the approval was successful.
  
  **Example:**
  ```solidity
  allowance[msg.sender][spender] = amount;
  emit Approval(msg.sender, spender, amount);
  ```

- **`function transferFrom(address sender, address recipient, uint amount) external returns (bool)`:**
  - Allows a spender to transfer `amount` tokens from the `sender` address to the `recipient` address, provided that the spender is allowed to transfer up to that amount.
  - Updates the balances and allowances accordingly.
  - Emits the `Transfer` event.
  - Returns `true` if the transfer was successful.
  
  **Example:**
  ```solidity
  allowance[sender][msg.sender] -= amount;
  balanceOf[sender] -= amount;
  balanceOf[recipient] += amount;
  emit Transfer(sender, recipient, amount);
  ```

- **`function mint(uint amount) external`:**
  - Allows the caller to create `amount` new tokens and add them to their own balance.
  - Increases the `totalSupply` by the `amount`.
  - Emits a `Transfer` event from the zero address to the caller.
  
  **Example:**
  ```solidity
  balanceOf[msg.sender] += amount;
  totalSupply += amount;
  emit Transfer(address(0), msg.sender, amount);
  ```

- **`function burn(uint amount) external`:**
  - Allows the caller to destroy `amount` tokens from their own balance.
  - Decreases the `totalSupply` by the `amount`.
  - Emits a `Transfer` event from the caller to the zero address.
  
  **Example:**
  ```solidity
  balanceOf[msg.sender] -= amount;
  totalSupply -= amount;
  emit Transfer(msg.sender, address(0), amount);
  ```

### Token Vault Contract Explanation 

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

interface IERC20 {
    function totalSupply() external view returns (uint);

    function balanceOf(address account) external view returns (uint);

    function transfer(address recipient, uint amount) external returns (bool);

    function allowance(address owner, address spender) external view returns (uint);

    function approve(address spender, uint amount) external returns (bool);

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}

contract Vault {
    IERC20 public immutable token;

    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function _mint(address _to, uint _shares) private {
        totalSupply += _shares;
        balanceOf[_to] += _shares;
    }

    function _burn(address _from, uint _shares) private {
        totalSupply -= _shares;
        balanceOf[_from] -= _shares;
    }

    function deposit(uint _amount) external {
        /*
        a = amount
        B = balance of token before deposit
        T = total supply
        s = shares to mint

        (T + s) / T = (a + B) / B 

        s = aT / B
        */
        uint shares;
        if (totalSupply == 0) {
            shares = _amount;
        } else {
            shares = (_amount * totalSupply) / token.balanceOf(address(this));
        }

        _mint(msg.sender, shares);
        token.transferFrom(msg.sender, address(this), _amount);
    }

    function withdraw(uint _shares) external {
        /*
        a = amount
        B = balance of token before withdraw
        T = total supply
        s = shares to burn

        (T - s) / T = (B - a) / B 

        a = sB / T
        */
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        _burn(msg.sender, _shares);
        token.transfer(msg.sender, amount);
    }
}
```

This contract is a simple implementation of a vault that holds ERC20 tokens and allows users to deposit and withdraw tokens in exchange for shares. It is built using Solidity and follows the ERC20 standard for token interactions. Here's a breakdown of its components and functionality:

### Interface: `IERC20`

The `IERC20` interface defines the standard functions and events for ERC20 tokens. It includes:
- **Functions:**
  - `totalSupply()`: Returns the total number of tokens in circulation.
  - `balanceOf(address account)`: Returns the balance of the specified account.
  - `transfer(address recipient, uint amount)`: Transfers tokens to the specified recipient.
  - `allowance(address owner, address spender)`: Returns the amount of tokens that `spender` is allowed to spend on behalf of `owner`.
  - `approve(address spender, uint amount)`: Approves `spender` to spend `amount` tokens on behalf of the caller.
  - `transferFrom(address sender, address recipient, uint amount)`: Transfers tokens from `sender` to `recipient` on behalf of the caller.

- **Events:**
  - `Transfer`: Emitted when tokens are transferred from one address to another.
  - `Approval`: Emitted when the allowance of a spender is changed.

### Contract: `Vault`

This contract implements a basic vault for ERC20 tokens, allowing users to deposit tokens and receive shares in return, or withdraw tokens by burning their shares. 

- **State Variables:**
  - `IERC20 public immutable token`: The ERC20 token that the vault will interact with. This is immutable and set during contract deployment.
  - `uint public totalSupply`: The total number of shares issued by the vault.
  - `mapping(address => uint) public balanceOf`: A mapping to track the number of shares owned by each address.

- **Constructor:**
  - `constructor(address _token)`: Initializes the vault with the ERC20 token address. This address is set once during deployment and cannot be changed.

- **Private Functions:**
  - `_mint(address _to, uint _shares)`: Increases the `totalSupply` and the caller's share balance.
  - `_burn(address _from, uint _shares)`: Decreases the `totalSupply` and the caller's share balance.

- **Public Functions:**
  - `deposit(uint _amount)`: Allows users to deposit tokens into the vault.
    - If the vault has no shares (`totalSupply == 0`), the user receives shares equal to the deposit amount.
    - Otherwise, the number of shares minted is proportional to the amount of tokens deposited relative to the vault's current token balance.
    - The function transfers tokens from the user's address to the vault's address using `transferFrom`.

  - `withdraw(uint _shares)`: Allows users to withdraw tokens from the vault by burning their shares.
    - Calculates the amount of tokens to withdraw based on the number of shares and the vault's current token balance.
    - The function burns the specified number of shares and transfers the corresponding amount of tokens to the user.

### Mathematical Formulas

- **Deposit Calculation:**
  ```plaintext
  s = (a * T) / B
  ```
  Where:
  - `a` = amount to deposit
  - `T` = total supply of shares
  - `B` = current balance of tokens in the vault
  - `s` = shares to mint

- **Withdraw Calculation:**
  ```plaintext
  a = (s * B) / T
  ```
  Where:
  - `a` = amount to withdraw
  - `s` = shares to burn
  - `B` = current balance of tokens in the vault
  - `T` = total supply of shares

The `Vault` contract allows users to deposit ERC20 tokens in exchange for shares and withdraw tokens by redeeming their shares. It maintains the proportionality between the shares and the underlying token balance, ensuring that users can deposit and withdraw tokens fairly based on their shareholding in the vault.
