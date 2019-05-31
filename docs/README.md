<div style="display: flex; direction: row; justify-content: center; margin-bottom: 32px;">
  <img src="./etherclear-logo.svg" alt="Etherclear logo" style="height: 220px; width: 540px" />
</div>


## What is Etherclear?

> A self-escrow to get your funds back, for safer P2P Ethereum transactions

Etherclear is a way to protect Ethereum users from sending ether and ERC-20 tokens irreversibly to the wrong address.

Plenty of people have lost their tokens by accidentally pasting or typing the wrong address. It shouldn’t be stressful to send tokens to an address you don’t know. It bothers us that this is such an easy and irreversible mistake to make with cryptocurrencies in general. 

We don’t yet know a good way to prevent this for every cryptocurrency. But we had to start somewhere, so we came up with a basic solution for P2P transactions in Ethereum, in the hope that we can prevent this happening to our ETH or ERC-20 tokens.

Here’s how this particular solution works. Etherclear uses a smart contract as a self-escrow. This contract takes funds, a hash, and a recipient address from a sender. The contract only releases the funds to the recipient address if it receives a valid request from that address. To be valid, the request must contain a passphrase that passes a hash check.

The hash check works as follows: when the dapp sends funds into the contract, it also stores a hash `keccak256(passphrase, recipient)` in the contract state. Of course, this hash is done on the client side, so the passphrase itself is not exposed publicly. When the recipient submits a transaction to retrieve funds, they enter the passphrase. The stored hash is compared against `keccak256(entered_passphrase, recipient)`, to confirm that the recipient sent the correct passphrase.

*Note: Etherclear requires a secure communication channel to send the passphrase. This would ideally be an encrypted email or chat service.*

## How To Use:

----
1. The sender creates a new payment in Etherclear and generates a passphrase for the payment.
2. The sender delivers the payment passphrase to the recipient through the secure communication channel.
3. The recipient enters the passphrase into Etherclear and retrieves the funds.
----
At any point in the process before step 3, the sender can choose to **cancel** the payment and withdraw their funds from the contract.

The sender can also specify a **hold time**, which acts as an *available time window* for the recipient to retrieve the funds, starting from when the payment is created. After this window has expired, the recipient can no longer retrieve funds for that payment. The purpose of this is mainly to prevent the passphrase from being cracked and used to withdraw the funds by an attacker with control of the account but not the communication channel, if a relatively short passphrase is used.

Etherclear’s targeted use cases are transferring large amounts in between your own wallets, and for large P2P payments to people you don’t know that well (and so you can’t be confident the address they gave you is correct). You can think of it as a kind of secondary authentication of an ETH address, double checking the recipient’s identity through the communication channel.

### User Guide
For a step by step guide to using Etherclear please see the [in depth users guide, here](https://docs.google.com/document/d/1UEobbwmL78Yhy6vjY4yGEdYThFIiHOieujM5TfM1dp4/edit).


## Usability Features/Road Map

### Relayer Service

Etherclear allows recipients send signed messages to a centralized relayer service, which submits that transaction to the contract and pays the gas fee on their behalf. In the future, we may set up a way for the sender to submit this signed message for the recipient.

### ENS Integration

We’re fans of ENS and its ability to provide clear human readable names for addresses. Etherclear recognizes ENS names for recipients and resolves any user address it sees.

**Other:**
- Contact lists
- Request for payment
- Subdomain provisioning

## Security
A request to receive funds must:
1. Be signed by the address that the sender specified.
2. Contain a valid passphrase that passes the hash check.

These conditions are enforced by the Etherclear smart contract.

The security of the method you use to communicate the passphrase to the recipient is also important. We recommend you use an encrypted chat or email service like WhatsApp, ProtonMail, or Signal.

## Disadvantages
### Sending tokens to centralized exchanges

This solution doesn’t work for sending tokens to centralized exchanges, because the recipient has to make a transaction to withdraw the funds to their account, and exchanges control the keys necessary to sign that transaction. This is not something exchanges would be willing to do. **However, if you send to an exchange address accidentally using Etherclear, you can always cancel and withdraw your money if you’re willing to pay the gas fee (that’s the point of the dapp)**. 

We’re hoping that this limitation won’t be as much of an issue when DEXs gain more traction, since the user interacts with them directly. We’re aware that many more people have the problem of sending funds to the wrong  exchange address rather than to the wrong person, so we’d love to hear any ideas about implementing something like this for exchanges.

### Communication channel needs to be secure

The communication channel has to be secure. This could potentially be an issue for users who aren’t security minded, or don’t know how to find a secure and trustworthy service. We hope that with the multiple encrypted chat and email solutions available today, this limitation won’t prevent users from using the app securely.

### User must trust smart contract

The user needs to place trust in the smart contract. To help make that less of an issue, we’ve published the smart contract and site code for anyone to read.

**The smart contract code is [here](https://github.com/etherclearapp/etherclear_contracts)**

## Fees

Etherclear charges a small fee  for each transaction, which consists of a constant base fee along with a variable payment fee that depends on the amount sent. The fee pays for the relayer service, as well as other development and hosting costs.

Currently, the base fee is 0.001 ETH, or $0.25 @ $260 / ETH, and the payment fee is set at 0.5%.

The fee doesn’t cover the cost of cancellations, because not every sender will cancel a payment.
