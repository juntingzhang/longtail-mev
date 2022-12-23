Examples of Ethereum Longtail MEV Strategies
========================================
This file strictly focuses on strategies related to earning an incentive provided by a protocol in exchange for performing upkeep or other tasks (e.g. compounding)\
\
These strategies are less contested then short tail strategies and have lower bribes, however there is a limit on what you're able to earn.
\

Convex Finance Earmarking
-------------------------

Convex Finance offers CRV incentives (1% of a pools pending CRV rewards) to anyone who calls the `earmarkRewards()` function.  
  
**Example**:
1. Determine the number of pools by calling the `poolLength()` function on the [Convex Finance: Booster Contract](https://etherscan.io/address/0xf403c135812408bfbe8713b5a23a04b3d48aae31)
2. For each pool, determine the curve gauge contract by calling `poolInfo()` with the pool id. 
3. Call `claimable_tokens()` on each pools gauge contract with the Convex Finance Voter Proxy address ([0x989AEb4d175e16225E39E87d0D97A3360524AD80](https://etherscan.io/address/0x989AEb4d175e16225E39E87d0D97A3360524AD80)) to determine the amount of pending CRV. The incentive you will receive is 1% of this.
4. Determine which pools are profitable to call `earmarkRewards()` for. To be competitive with this startegy you will need to call `earmarkRewards()` for multiple pools in a single transaction, which requires a custom smart contract.

A recent example of this strategy in use can be found [here](https://etherscan.io/tx/0xa40ba58e1cc3b2f0d9e53710db496ebf1fa7a22ab2df101e482c51757d4a8d22) where the caller earns $3 in profit.

Aura Finance Earmarking
-------------------------

Aura Finance is a fork of Convex Finance, and offers the same 1% incentive for calling the `earmarkRewards()` function.

Note that the incentive is paid in BAL instead of CRV.

**Contracts:**
* Booster: [0xa57b8d98dae62b26ec3bcc4a365338157060b234](https://etherscan.io/address/0xa57b8d98dae62b26ec3bcc4a365338157060b234)
* Voter Proxy: [0xaF52695E1bB01A16D33D7194C28C42b10e0Dbec2](https://etherscan.io/address/0xaF52695E1bB01A16D33D7194C28C42b10e0Dbec2)

Llama Airforce Compounding
-------------

Llama Airforce offers [vaults](https://llama.airforce/#/union/pounders) where users can deposit their funds and have their staking rewards automatically compounded for them.

They have decentralized compounding by offering an incentive (a percentage of pending rewards) to whoever calls `harvest()` on each vaults vault contract. (Contracts can be viewed [here](https://llama.airforce/#/code))

The incentive is given in the form of whatever the deposit token is (e.g. for the cvxCRV vault, the incentive is given in cvxCRV)

Unfortunately, the newer vaults have permissioned harvesting, where only an address set by the contract owner is able to harvest the vault.

A recent example of this strategy in use can be found [here](https://etherscan.io/tx/0x3b5be437ff7dd499a8258f22874eaf7cca0ed70304c5577d01a878cc63d7f07b) where the uCRV vault is harvested.

Aladdin Harvesting
--------------------

One of [Aladdin DAOs](https://aladdin.club/) products is Concentrator, which offers autocompounding vaults similar to Llama airforce.\
\
Like the other strategies listed, Aladdin DAO offers a percentage of pending rewards to the user who calls the harvest/compound function. \
\
Incentives are paid in [Aladdin cvxCRV](https://etherscan.io/address/0x2b95a1dcc3d405535f9ed33c219ab38e8d7e0884) which is autocompounding staked cvxCRV.\
**Contracts:**
* Aladdin cvxCRV [0x2b95A1Dcc3D405535f9ed33c219ab38E8d7e0884](https://etherscan.io/address/0x2b95a1dcc3d405535f9ed33c219ab38e8d7e0884) - call `harvest()` here
* Concentrator Vault [0x3Cf54F3A1969be9916DAD548f3C084331C4450b5](https://etherscan.io/address/0x3cf54f3a1969be9916dad548f3c084331c4450b5) - call `harvest()` here while providing a pool id

The Concentrator vault contract is similar to the Convex Finance Booster contract, in that you should check the number of pools with `poolLength()` and determine the pending rewards for each pool using `poolInfo()`. 

OUSD Harvesting
-----------
[Origin Dollar](https://www.ousd.com/) offers users a rebasing stable coin that uses DeFi strategies to generate yield.\
\
They provide 1% of pending rewards to any user that harvests these strategies, paid in USDT.\
\
**Notes:**
* There are multiple strategies to be harvested, and allocation to each strategy is changed over time. New strategies can be added, a current list of strategies can be found in their [documentation](https://docs.ousd.com/smart-contracts/registry)

They have [a guide for incentivized harvesting](https://docs.ousd.com/guides/incentivized-harvesting-guide) in their documentation with more information\
\
In [this recent example](https://etherscan.io/tx/0x4ae91264660f1b3d18545279bc392fc2458cd12af3753c76d7e2d9ef7d550e5f) of the strategy being executed, you can see that the caller spends $9 in gas fees and receives $16 in USDT back.

Crypto Kitty Breeding
----------
[Crypto Kitties](https://etherscan.io/address/0x06012c8cf97bead5deae237070f9587f8e7a266d) is an NFT game where you are able to breed kitties.\
\
When a user wants to breed one of their kitties, they pay a fee (currently 0.008 ether). This will impregnate their kitten, and cause it to give birth after a certain amount of time has passed.\
\
However, giving birth does not happen automatically. The aforementioned 0.008 ether fee is given to the user who calls the `giveBirth()` function on the Crypto Kitties contract in exchange for triggering the birth. 

**Notes:**
* The birth fee is changed according to network usage by the `cooAddress` on the Crypto Kitties contract. You can see these changes [here](https://etherscan.io/address/0x09191d18729da57A83A9AfC8Ace0c8D7D104E118)
* This strategy is unfortunately not currently profitable. This is because there is a [privelleged birther](https://etherscan.io/address/0xAb5622d7Da96c571c6aBe08e4B85E462eB666e4F) who has 5 blocks to call the `getBirth()` function before it is opened up to be called by anyone else. This privellged address consistently calls `getBirth()` before their 5 block advantage is up. 

Bean Sunrise
-----------
Every hour, the [Beanstalk Protocol](https://bean.money/) requires a function, `sunrise()` to be called in order to perform upkeep.\
\
Sunrise currently rewards the user with 27 BEAN (an algorothimic stablecoin) for calling the function. This reward increases every block that the function isn't called. \
\
[Recent transaction as an example](https://etherscan.io/tx/0xa3c4c9147fd3e4fa75ea8ec1ef42538e7f401472fce91a48ee58fb05d7c10ce0)

StakeDAO
----------

StakeDAO offers many different products, many of which have an incentive built in for calling a function.

**StakeDAO Strategies:**
1) StakeDAO offers liquid locked tokens as a product. When a user deposits tokens, the tokens are not locked in the same transaction as this costs additional gas. Instead, StakeDAO allows for anyone to trigger the lock after a deposit is complete and earn a % of the deposit.
2) StakeDAO allows for users to stake LP tokens to earn additional yield from the boost given by locked tokens. When a user deposits, their deposit is not staked in the same transaction as this costs additional gas. Again, StakeDAO allows anyone to stake the tokens and earn a % of the deposit.

Example:
[User deposits](https://etherscan.io/tx/0x2290194dce040ffe1117b3978afca4ed675e538c5fdd33019fdd44e7296cf01f) -> [Bot stakes the LP tokens and takes 0.1% of the deposit as a fee](https://etherscan.io/tx/0x6dc89deec4264ef137b842db1449e4cb23d50d558caa1e725a0f0ebdd18cef9e)\
\
3) StakeDAO allows for any user to harvest their strategies and take a percentage of the rewards as a fee. This can be quite profitable for strategies where rewards are only able to be harvested once per week. Here is a [recent example](https://etherscan.io/tx/0x19f4426b1a5284990ffb56b340dc416e1c39b88fbe2f94bdcb869c771fbd70ee) where a bot paid $26 in transaction fees and earned $220. \
\
They have a [nice dashboard](https://lockers.stakedao.org/manage-dao) where you can view all of this.

Bent Finance Harvesting
-------------
[Bent Finance](https://app.bentfinance.com/) offers users the option to stake Curve LP tokens, and gives a percentage of pending rewards to the user who harvests them.

As of the writing of this, Bent Finance does not have very high TVL, so it will likely only be profitable to harvest some of the pools once every several days. 

VSDS Advance
----------
Every 8 hours any user can call the `advance()` function on the [VSDS Contract](https://etherscan.io/address/0x05bab9017705d32a8a0bb2ec06a78fa0fa20f1a4) to earn 100 VSD.

Beefy Finance
--------
[Beefy Finance](https://beefy.com/) offers autocompounding vaults that give a percentage of pending rewards to whoever calls `harvest()`.\
\
**Notes:**
* This harvest fee is available on the 17 other chains that Beefy Finance is on, however their profit is far less then the other strategies listed above (cents instead of dollars)
* As of the writing of this, there is little TVL in the Ethereum vaults which means that they will not be profitable to harvest often.  
