# SynMargin

{% hint style="success" %}
**SynMargin: A**
{% endhint %}

<figure><img src="../../.gitbook/assets/SynMarge (1).png" alt=""><figcaption></figcaption></figure>

Undercollateralized loans allow users to take out undercollateralized loans. This is a feature that is decentralized and allow instant borrowing of amounts greater than the collateral used.

For example, through DeBank’s undercollateralized loan option, a user can use a $1,000 collateral to take out $1,500 to $4,000 in loan.

This loan option is perfect for those who do short-term trading or flipping of digital assets or arbitrage trading on decentralized exchanges.

This feature only allows the use of stable coins as collateral and the borrowing of stable coins.

\


Feature Architecture and Definitions

&#x20;From the architecture as shown in the figure above, there are 4 main part of the undercollateralized loan mechanism, they are as follows:

DeBank Vault: The DeBank Vault is a single vault where borrowable assets from DeBank are saved. From DeBank Vault, assets  can be sent to the access vault and the access vault can send funds back to the DeBank Vault but funds in the debank vault cannot be directly accessed from the access vault.

&#x20;Access Vault:  is the vault (smart contract) where funds to be borrowed are accessed from. Meaning borrowers can sign borrow functions transactions, repay functions transactions in the access vault from their BAA.&#x20;

NOTE: Only BUSD and USDT can be borrowed or used as collateral on BSC and USDC and USDT on Polygon

BAA (borrowers Access Account) is a smart contract that gets deployed when a user sends a borrow transaction confirmation. Every borrower has a BAA assigned to his/her wallet address.

When a user borrows, since the collateral is less than the amount borrowed, the funds borrowed remain in the BAA (The borrower cannot withdraw it to his wallet).&#x20;

The borrower can complete trades with the funds in his/her BAA as the BAAs are allowed to do trade transactions (signed by borrower’s wallet) with dexs like pancakeswap.&#x20;

DexAR which is the Decentralized exchange access router. This is the smart contract that specifies the decentralized exchanges that the BAA supports and can access and it also specifies the actual assets that can be traded on from the BAA

\


Summary:

* An admin of DeBank can initiate a transaction to send funds from DeBank Vault to the access vault and vice versa.
* The BAA for each borrower can call borrow, repay functions from the access vault and this transactions are signed from the borrowers address assigned to the specific BAA. i.e, wallet addresses of borrowers cannot directly call those functions but can do so through the BAA.
* Funds borrowed are deposited in the BAA of the borrower.
* The BAA is connected to Decentralized exchanges so that a borrower can call trade/swap functions in the BAA to carryout trades in the BAA, all trades are done within the BAA and signed by the user.
* The user can only withdraw profits from trades or balance to his address
* An admin manager address can call a liquidate transaction for a borrower is the Liquidation treashold condition becomes true thereby triggering an immediate liquidation of the loan.
* If liquidation function is called and executed, the amount of the loan plus fees (interest) plus liquidation fee is sent in same transaction or a transaction that follows immediately after liquiation to the access vault.
* User can then withdraw with is left of his collateral or can take another loan or add more collateral to take another loan.

Summary example of How it works:

When a user wants to take out an undercollateralized loan, the following steps are taken:

1. If this is the first time,  the user needs to click on “Create an undercollateralized loan account”. This action will deploy a BAA that will be linked to the users wallet so it is a transaction which will be signed in wallet.  This step is skipped if this is not the first time.
2. The user then deposits his collateral.&#x20;
3. The user then sees how much he can borrow, he can then borrow max that amount.
4. The amount is deposited in the BAA account and the user can make trading on supported decentralized exchanges and trade supported assets.

Algorithm

Borrow amount:

With collateral “C” deposited to access vault, the user can borrow the following amount:

Amount (A) = BP × C

BP = borrowing power which is a parameter set in smart contract by admin.

For example:

&#x20;None holders of ETNA, BP = 1.5, &#x20;

Holders of ETNA, BP =2,&#x20;

Bronze staker, BP = 2.5

Silver staker BP = 3, &#x20;

Gold staker, BP = 3.5

Platinum stake and LP stakers, BP = 4

This applies if stakes in a minimum stake in any of this pools.

MS= minimum stake is a parameter in smart contract

For example, MS=10,000

If a user who staked 5000  and then deposited 1,000 BUSD, then the amount he can borrow is default for holders since it is less than MS

Amount = BP × C = 2 × 1000 = 2000 BUSD

If a user holds etna in wallet but also stake in silver 15, 000 ETNA and in platinum 20,000 ETNA, Then the user will be able to borrow the max category.

If C for this user is C=1000,

Then the user can borrow:

Amount = BP × C = 4 × 1000 = 4000BUSD

Borrow interest:

Borrow interest is calculated daily and is expected to be paid within every 24 – 48 hours.

Interest = Amount Borrowed (A) × daily Rate (r)&#x20;

Where r = is parameter in smart contract and set to a value.

For above borrower, if r = 0.15%

Then daily interest is I = 4000 × 0.15/100 = 6 BUSD

There is a parameter in smart contract MN which is maximum number of daily interest accumulation allowed.

If MN = 2, then in two days interest accumulates (which is 12  busd in this example), the user can still trade and withdraw profit. If the accumulation goes above MN×I, then the user is unable to trade or withdraw profits until the accumulated interest is paid.

Interest paid receiver:&#x20;

70% goes to the access vault and the remaining 30% goes to an admin wallet address.

The 70% is a parameter that can be set and the admin address is also a parameter that can be set.

Liquidation algorithm:

With Collateral C=1000BUSD,   Amount borrowed A= 4000

The following parameters are set in smart contract:

NF negative factor, and Liquidation Factor = LF&#x20;

Negative Volatility Threshold NVT = C×NF

And Liquidation Fee Value (LFV) = C × LF

If NF=0.6 and LF = 0.2

Then, NVT = 600, and LFV = 200.

NVT is the value by which a borrowers loan balance will drop to trigger a liquidation.

For example, with the loan A=4000, if after several trading, the balance dropped to A = 3400, then this will trigger a liquidation.

To prevent this, the user needs to balance the amount by either paying some portion of the loan back or by depositing some funds into the account to increase his/her balance.

There is a parameter DNP: drop notification point which is a percentage.

When the loan amount drops by DNP×NVT, the user gets notified

For example, DNP = 0.7

If the drop in balance is 0.7×NVT = 420, then the user gets notified. DNP can be a parameter set by the user to specify where he/she wants to be notified.

There will be a balance account function that allows the user to deposit some funds to his account to bring his/her balance back up

And there is a repay loan that allows the user to pay back a portion or all the loan&#x20;

These two options can be used to prevent liquidation.

How liquidation:

A manager address carries out the liquidation automatically (micro service)

If the drop of NVT is reached, the manager address automatically trade the asset back to the asset borrowed and send it to the access vault, the drop +liquidation fee+ any outstanding interest is then deducted from the collateral to balance the loan

\


Liquidation occurs when:

&#x20;Borrower’s account balance in BAA ≤ Amount Borrowed – NVT&#x20;

BAA configuration:

\
The following is a detailed description of the protocol
