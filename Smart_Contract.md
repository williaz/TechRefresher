
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

#### internal, external
- internal is the same as private, except that it's also accessible to contracts that inherit from this contract. (Hey, that sounds like what we want here!).

- external is similar to public, except that these functions can ONLY be called outside the contract — they can't be called by other functions inside that contract.

- address == onject








