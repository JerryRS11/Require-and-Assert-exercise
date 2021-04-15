# Require-and-Assert-exercise
Understand how require and assert work within the code 
pragma solidity 0.6.6;
contract identity {
    struct Person {
        string name;
        uint age;
        uint height;
        bool senior;
    }
    address public owner;
 // Define the owner as default to use it later   
    constructor () public {
        owner = msg.sender;
    }
// Create a mapping to structure the database and create a dictionary of the list of people    
    mapping (address => Person) private people;
    address [] private creators;
// Create a function that get's users information (public function)
function createPerson (string memory name, uint age, uint height) public{
    require (age<150, "age must be below 150");
        Person memory newPerson;
        newPerson.name = name;
        newPerson.age = age;
        newPerson.height = height;
// Create an if condition of users age to see if he/she is senior or not
        if(age >=65) {
            newPerson.senior = true;
        }
        else {
            newPerson.senior = false;
        }
//Create a link between the public and private function
        insertPerson (newPerson);
// Create a push to add people to the list and use assert to validate people's invariance
        creators.push (msg.sender);
// Pack (abi.encodePacked) and hash (keccak256) to get an hexadecimal string
        assert(
            keccak256 (
                abi.encodePacked (
                    people[msg.sender].name, 
                    people[msg.sender].age, 
                    people[msg.sender].height, 
                    people[msg.sender].senior)) == 
            keccak256 (
                abi.encodePacked(
                    newPerson.name, 
                    newPerson.age, 
                    newPerson.height, 
                    newPerson.senior)
                       )
              );
    }
    
//Create a function that can add people to the list (private function)
// The address creator is the one who inputs the information
    function insertPerson (Person memory newPerson) private {
        address creator = msg.sender;
        people [creator] = newPerson;
    }
    
//Create a function that can get only user's information and not everyone's information
    function getPerson () public view returns (string memory name, uint age, uint height, bool senior) {
        address creator = msg.sender;
        return (people[creator].name, people[creator].age,people[creator].height, people[creator].senior);
    }
    
//Create a function that allows the owner to delete a person registered in the system
// Validate that users information is = 0 using assert()
    function deletePerson (address creator) public {
        require (msg.sender == owner);
        delete people [creator];
        assert (people[creator].age == 0);
    }

// Create a function that can create a address of registered people
    function getCreators (uint index) public view returns (address) {
    require (msg.sender == owner, "caller must be owner");
    return creators [index];
    }
}
