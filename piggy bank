// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract TimeLockPiggyBank {
    struct Deposit {
        uint amount;
        uint unlockTime;
    }

    mapping(address => Deposit) public deposits;

    event Deposited(address indexed user, uint amount, uint unlockTime);
    event Withdrawn(address indexed user, uint amount);

    // Deposit ETH with a specific unlock time
    function deposit(uint _unlockTime) external payable {
        require(msg.value > 0, "Must send ETH");
        require(_unlockTime > block.timestamp, "Unlock time must be in the future");
        require(deposits[msg.sender].amount == 0, "Existing deposit found");

        deposits[msg.sender] = Deposit({
            amount: msg.value,
            unlockTime: _unlockTime
        });

        emit Deposited(msg.sender, msg.value, _unlockTime);
    }

    // Withdraw after unlock time
    function withdraw() external {
        Deposit memory userDeposit = deposits[msg.sender];
        require(userDeposit.amount > 0, "No deposit found");
        require(block.timestamp >= userDeposit.unlockTime, "Cannot withdraw yet");

        uint amount = userDeposit.amount;
        delete deposits[msg.sender];

        payable(msg.sender).transfer(amount);
        emit Withdrawn(msg.sender, amount);
    }

    // View time remaining for your deposit
    function timeLeft() external view returns (uint) {
        if (block.timestamp >= deposits[msg.sender].unlockTime) {
            return 0;
        }
        return deposits[msg.sender].unlockTime - block.timestamp;
    }
}
