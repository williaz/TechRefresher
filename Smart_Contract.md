
- state variables are stored permanently in the blockchain? So creating a dynamic array of structs can be useful for storing structured data in your contract, kind of like a database.
- declare an array as public, and Solidity will automatically create a getter method for it.
  - Other contracts would then be able to read from, but not write to, this array.


### Function
- It's convention (but not required) to start function parameter variable names with an underscore (_) in order to differentiate them from global variables. 
- memory keyword: pass the fucntion argument by value
- In Solidity, functions are public by default.
- it's convention to start private function names with an underscore (_)
- in Solidity, the function declaration contains the type of the return value
- view function: meaning it's only viewing the data but not modifying it
- pure functions: which means you're not even accessing any data in the app

- Events are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.

```solidity
contract ERC721 {
  event Transfer(address indexed _from, address indexed _to, uint256 indexed _tokenId);
  event Approval(address indexed _owner, address indexed _approved, uint256 indexed _tokenId);

  function balanceOf(address _owner) external view returns (uint256); 
  // takes an address, and returns how many tokens that address owns.


  function ownerOf(uint256 _tokenId) external view returns (address);
  // takes a token ID (in our case, a Zombie ID), and returns the address of the person who owns it.
  
  function transferFrom(address _from, address _to, uint256 _tokenId) external payable;
  function approve(address _approved, uint256 _tokenId) external payable;
}

```

#### mapping, import, require, storage, memory, interface
```sol
mapping (address => uint) public accountBalance;

import "./someothercontract.sol";

contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}

NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);
uint num = numberContract.getNum(msg.sender);

(,,c) = multipleReturns();
```
- msg.sender: refers to the address of the person (or smart contract) who called the current function.
  - Using msg.sender gives you the security of the Ethereum blockchain — the only way someone can modify someone else's data would be to steal the private key associated with their Ethereum address.
- In Solidity, function execution always needs to start with an external caller. A contract will just sit on the blockchain doing nothing until someone calls one of its functions. So there will always be a msg.sender

- require makes it so that the function will throw an error and stop executing if some condition is not true

- Solidity doesn't have native string comparison, so compare their keccak256 hashes to see if the strings are equal)

- Storage refers to variables stored permanently on the blockchain. Memory variables are temporary, and are erased between external function calls to your contract. Think of it like your computer's hard disk vs RAM.
  - Most of the time you don't need to use these keywords because Solidity handles them by default. State variables (variables declared outside of functions) are by default storage and written permanently to the blockchain, while variables declared inside functions are memory and will disappear when the function call ends.

#### internal, external, address
- internal is the same as private, except that it's also accessible to contracts that inherit from this contract. (Hey, that sounds like what we want here!).

- external is similar to public, except that these functions can ONLY be called outside the contract — they can't be called by other functions inside that contract.

- address == onject


### Ownable, 
- onlyOwner is such a common requirement for contracts that most Solidity DApps start with a copy/paste of this Ownable contract, and then their first contract inherits from it.

- A function modifier looks just like a function, but uses the keyword modifier instead of the keyword function. And it can't be called directly like a function can — instead we can attach the modifier's name at the end of a function definition to change that function's behavior.

- modifier == Annotation
```sol
  modifier onlyOwner() {
    require(isOwner());
    _;
  }
```
- Each individual operation has a gas cost based roughly on how much computing resources will be required to perform that operation (e.g. writing to storage is much more expensive than adding two integers). The total gas cost of your function is the sum of the gas costs of all its individual operations.

### Gas
- if you have multiple uints inside a struct, using a smaller-sized uint when possible will allow Solidity to pack these variables together to take up less storage. 
- You'll also want to cluster identical data types together (i.e. put them next to each other in the struct) so that Solidity can minimize the required storage space
- view functions don't cost any gas when they're called **externally** by a user.
- One of the more expensive operations in Solidity is using storage — particularly writes.
- 
### now, seconds, minutes, hours, days, weeks and years; calldata
- The variable now will return the current unix timestamp of the latest block (the number of seconds that have passed since January 1st 1970). 
  -  lead to the "Year 2038" problem, when 32-bit unix timestamps will overflow and break a lot of legacy systems. 
- calldata is somehow similar to memory, but it's only available to external functions.


- We have visibility modifiers that control when and where the function can be called from: private means it's only callable from other functions inside the contract; internal is like private but can also be called by contracts that inherit from this one; external can only be called outside the contract; and finally public can be called anywhere, both internally and externally.

- We also have state modifiers, which tell us how the function interacts with the BlockChain: view tells us that by running the function, no data will be saved/changed. pure tells us that not only does the function not save any data to the blockchain, but it also doesn't read any data from the blockchain. Both of these don't cost any gas to call if they're called externally from outside the contract (but they do cost gas if called internally by another function).

- Then we have custom modifiers, can define custom logic to determine how they affect a function.

### payable
- It is important to note that you cannot transfer Ether to an address unless that address is of type address payable. But the _owner variable is of type uint160, meaning that we must explicitly cast it to address payable.

```sol
uint randNonce = 0;
uint random = uint(keccak256(abi.encodePacked(now, msg.sender, randNonce))) % 100;
randNonce++;
uint random2 = uint(keccak256(abi.encodePacked(now, msg.sender, randNonce))) % 100;

// use an oracle to access a random number function from outside of the Ethereum blockchain.
```








