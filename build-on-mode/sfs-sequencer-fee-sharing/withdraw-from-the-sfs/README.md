---
description: In this section you will learn different ways of withdrawing from the SFS.
---

# Withdraw from the SFS

{% hint style="info" %}
There is a minimum to claim the SFS earned fees and it's <mark style="color:orange;">`0.01 ETH`</mark>
{% endhint %}

There are 3 ways of claiming your funds:

1. In one click from the [developer dashboard](https://app.mode.network/developers/) <mark style="color:green;">(Recommended)</mark>
2. Directly from a wallet that holds the NFT
3. Programmatically from a smart contract that should also hold the NFT

The SFS contract works as a balance sheet. It contains all the mappings tracking which contracts are linked to which SFS NFTs. All balances are kept in the contract, until the users withdraw them.\
\
The revenue of the SFS is calculated by an offchain component that distributes the fees at the end of an epoch. You will not see your balance immediately going up after registering your contract because this off-chain component updates the balances every first day of the month on mainnet and every 24 hours on testnet.

To withdraw, you must hold the SFS NFT to be allowed to get the revenue. If not, the transaction will revert. It doesn’t matter if it’s an EOA (Externally Owned Account) or a smart contract, any of them must have the NFT in their balance in order to withdraw the funds.\
\
This is the withdrawal function:

{% code fullWidth="true" %}
```solidity
    /// @notice Withdraws earned fees to `_recipient` address. Only callable by NFT owner.
    /// @param _tokenId token Id
    /// @param _recipient recipient of fees
    /// @param _amount amount of fees to withdraw
    /// @return amount of fees withdrawn
    function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)
        public
        onlyNftOwner(_tokenId)
        returns (uint256)
    {
        uint256 earnedFees = balances[_tokenId];

        if (earnedFees == 0 || _amount == 0) revert NothingToWithdraw();
        if (_amount > earnedFees) _amount = earnedFees;

        balances[_tokenId] = earnedFees - _amount;

        emit Withdraw(_tokenId, _recipient, _amount);

        Address.sendValue(_recipient, _amount);

        return _amount;
    }
```
{% endcode %}

Note that the function has the <mark style="color:orange;">`onlyNftOwner`</mark> modifier which reverts the transaction if the entity calling the function is not the owner of the NFT.
