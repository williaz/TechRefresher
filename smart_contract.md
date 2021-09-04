
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










