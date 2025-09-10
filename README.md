# Academic-credential-verification
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract Project {
    
    struct Credential {
        address student;
        address institution;
        string degree;
        uint256 graduationYear;
        bool isValid;
    }
    
    struct Institution {
        string name;
        bool isAuthorized;
    }
    
    mapping(uint256 => Credential) public credentials;
    mapping(address => Institution) public institutions;
    mapping(address => bool) public authorizedInstitutions;
    
    uint256 public credentialCounter;
    address public admin;
    
    event CredentialIssued(uint256 indexed credentialId, address indexed student);
    event InstitutionRegistered(address indexed institution);
    
    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin");
        _;
    }
    
    modifier onlyAuthorized() {
        require(authorizedInstitutions[msg.sender], "Not authorized");
        _;
    }
    
    constructor() {
        admin = msg.sender;
    }
    
    function registerInstitution(address _institution, string memory _name) public onlyAdmin {
        require(_institution != address(0), "Invalid address");
        institutions[_institution] = Institution(_name, true);
        authorizedInstitutions[_institution] = true;
        emit InstitutionRegistered(_institution);
    }
    
    function issueCredential(
        address _student,
        string memory _degree,
        uint256 _graduationYear
    ) public onlyAuthorized {
        require(_student != address(0), "Invalid student");
        credentialCounter++;
        
        credentials[credentialCounter] = Credential({
            student: _student,
            institution: msg.sender,
            degree: _degree,
            graduationYear: _graduationYear,
            isValid: true
        });
        
        emit CredentialIssued(credentialCounter, _student);
    }
    
    function verifyCredential(uint256 _credentialId) public view returns (Credential memory, bool) {
        require(_credentialId <= credentialCounter && _credentialId > 0, "Invalid ID");
        Credential memory credential = credentials[_credentialId];
        bool isVerified = credential.isValid && authorizedInstitutions[credential.institution];
        return (credential, isVerified);
    }
}
##contract addresses <img width="1861" height="864" alt="Screenshot 2025-09-10 145617" src="https://github.com/user-attachments/assets/6346ec81-6bc6-447e-a202-51ea7fadc105" />
