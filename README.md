🏦 TimeLock Piggy Bank ⏳

A simple Ethereum smart contract that allows users to deposit ETH which can only be withdrawn after a specific unlock time. Great for self-saving, gifting, or time-locked vaults.

🔐 Features

⏳ Time-locked deposits per user

💰 ETH held securely until user-defined unlock time

🔓 Only the depositor can withdraw, and only after time has passed

👥 Multi-user: each address can have one active deposit

📜 Smart Contract
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

    function withdraw() external {
        Deposit memory userDeposit = deposits[msg.sender];
        require(userDeposit.amount > 0, "No deposit found");
        require(block.timestamp >= userDeposit.unlockTime, "Cannot withdraw yet");

        uint amount = userDeposit.amount;
        delete deposits[msg.sender];

        payable(msg.sender).transfer(amount);
        emit Withdrawn(msg.sender, amount);
    }

    function timeLeft() external view returns (uint) {
        if (block.timestamp >= deposits[msg.sender].unlockTime) {
            return 0;
        }
        return deposits[msg.sender].unlockTime - block.timestamp;
    }
}

🚀 Deployment

You can deploy this contract using:

Remix IDE

Hardhat / Foundry / Truffle

Any EVM-compatible network (Ethereum, Sepolia, Polygon, etc.)

🧪 Example Usage

Deposit
Call deposit(uint _unlockTime) and send ETH.
Example: deposit(1730000000) — sets unlock time in Unix timestamp (e.g., future date).

Withdraw
After unlock time passes, call withdraw() to get your ETH back.

Check Time Left
Call timeLeft() to see how many seconds remain.

🛡️ Security Notes

Each address can only have one active deposit at a time.

Withdrawals are only allowed after the specified unlock time.

Contract is simple and gas-efficient, but does not support interest, ownership, or emergency withdrawals.

🧾 License

MIT © 2025
