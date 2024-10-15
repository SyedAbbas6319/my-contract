// SPDX-License-Identifier: MIT
pragma solidity ^0.8.6;

contract StudentLoanSystem {
    address public admin;
    uint256 public numApplications;

    enum LoanStatus {
        Pending,
        Approved,
        Denied
    }

    struct LoanApplication {
        string name;
        string fatherName;
        uint cnic;
        string bankAccount;
        uint loanAmount;
        uint gpa;
        uint semester;
        uint guardianIncome;
        uint numLoans;
        LoanStatus status;
    }

    mapping(uint256 => LoanApplication) public loanApplications;

    event LoanApplicationSubmitted(
        address indexed student,
        uint256 applicationId
    );
    event LoanApproved(
        address indexed student,
        uint256 applicationId,
        string message
    );
    event LoanDenied(
        address indexed student,
        uint256 applicationId,
        string message
    );
    event LoanDisbursed(
        address indexed student,
        uint256 applicationId,
        uint256 amount
    );

    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin can execute this");
        _;
    }

    constructor() {
        admin = msg.sender;
    }

    function getBalance() external view returns (uint) {
        return address(this).balance;
    }

    receive() external payable {}

    function applyForLoan(
        string memory _name,
        string memory _fatherName,
        uint _cnic,
        string memory _bankAccount,
        uint _loanAmount,
        uint _gpa,
        uint _semester,
        uint _guardianIncome,
        uint _numLoans
    ) external {
        require(
            loanApplications[numApplications].status == LoanStatus.Pending,
            "You already have a pending application"
        );

        loanApplications[numApplications] = LoanApplication({
            name: _name,
            fatherName: _fatherName,
            cnic: _cnic,
            bankAccount: _bankAccount,
            loanAmount: _loanAmount,
            gpa: _gpa,
            semester: _semester,
            guardianIncome: _guardianIncome,
            numLoans: _numLoans,
            status: LoanStatus.Pending
        });

        emit LoanApplicationSubmitted(msg.sender, numApplications);
        numApplications++;
    }

    function processLoanApplication(
        uint256 _applicationId,
        bool _approval
    ) external onlyAdmin {
        LoanApplication storage application = loanApplications[_applicationId];

        require(
            application.status == LoanStatus.Pending,
            "Application is not pending"
        );

        if (_approval) {
            application.status = LoanStatus.Approved;
            emit LoanApproved(
                msg.sender,
                _applicationId,
                "Application approved. Awaiting fund disbursement."
            );
            disburseFunds(payable(msg.sender), application.loanAmount);
        } else {
            application.status = LoanStatus.Denied;
            emit LoanDenied(
                msg.sender,
                _applicationId,
                "Application denied by admin."
            );
        }
    }

    function disburseFunds(address payable _student, uint256 _amount) internal {
        require(
            address(this).balance >= _amount,
            "Insufficient funds in the contract"
        );
        (bool success, ) = _student.call{value: _amount}("");
        require(success, "Fund disbursement failed");
        emit LoanDisbursed(_student, numApplications - 1, _amount); // Assuming _applicationId is the index of the loanApplications array
    }

    function checkLoanApprovalCriteria(
        uint _gpa,
        uint _semester,
        uint _guardianIncome,
        uint _numLoans
    ) internal pure returns (bool) {
        if (_gpa < 30) {
            return false;
        }
        if (_semester < 3) {
            return false;
        }
        if (_guardianIncome >= 35000) {
            return false;
        }
        if (_numLoans >= 2) {
            return false;
        }
        return true;
    }

    function getAllApplications()
        external
        view
        returns (LoanApplication[] memory)
    {
        uint256 count = 0;
        for (uint256 i = 0; i < numApplications; i++) {
            if (loanApplications[i].status == LoanStatus.Pending) {
                count++;
            }
        }

        LoanApplication[] memory applications = new LoanApplication[](count);

        uint256 index = 0;
        for (uint256 i = 0; i < numApplications; i++) {
            if (loanApplications[i].status == LoanStatus.Pending) {
                LoanApplication memory application = loanApplications[i];
                applications[index] = application;
                index++;
            }
        }

        return applications;
    }
}
