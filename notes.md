# Blockchain Notes

> This is my learning notes of Blockchain.
> 
> These notes just serve as a reminder of my confusing points and will not cover every detail of Blockchain mechanism.
>
> If you find something wrong, please let me know: swfeng98 _at_ gmail _dot_ com

## Contents
- [What is the process of mining?](#what-is-the-process-of-mining)
- [What does a block contain?](#what-does-a-block-contain)
- [Where are the bitcoin transactions stored?](#where-are-the-bitcoin-transactions-stored)
- [Are there any options for blockchain to store data?](#are-there-any-options-for-blockchain-to-store-data)
- [Reference](#reference)

--- 

## What is the process of mining?
Take Bitcoin as an example. Suppose the miner Joe is competing for the block 502425 (but failed).

1. Competing to validate

    In the world of Bitcoin, it takes approximately 10 minutes to validate a new block. Our miner Joe was competing to validate the block 502425, the previous one.

2. Searching for PoW and adding new transactions to the pool

    Unfortunately, someone else solved the problem before him. But, the end of one block's competition means the beginning of a new one. 

    As soon as the block 502425 was mined, Joe updated his local copy of the blockchain and starts to create a _candidate block_, the block 502426. While Joe's computer ( or node ) was searching for the _Proof of Work_ for the previous block, it was also listening for new transactions. Those new transactions are added to the _memory pool_ or _transaction pool_. This is where transactions wait until they can be included in a new block and validated.

    > A candidate block is a temporary block created using transactions selected from the memory pool.
    >
    > Each node then tries to add their candidate block to the blockchain through the process of mining.
    > 
    > Every block starts life as a candidate block, but only the ones that are successfully mined get added to the blockchain.

3. Creating the candidate block

    After finding that the current block has a valid Proof of Work, it starts constructing a candidate block by gathering the transactions in the transaction pool. It removes the transactions already present in the previous block, if there are any. The block is called a candidate block because it doesn't have a valid Proof of Work yet.

4. Coinbase transaction

    The first thing Joe's node does is creating the coinbase transaction. Very simply, this is his reward for mining the block. This transaction says => _Pay Joe's wallet address xxx BTC to reward him for finding a valid Proof of Work_. This transaction is different from the other ones because the bitcoins in the reward are created from nothing. They do not come from someone's wallet. Joe's node also calculates the transaction fees in the block.

        Joe's reward = Reward for mining block + transactions fees

    In this case, we can see that the block reward is 12.5 BTC ( Block Reward in left column ) and the transactions fees are equal to 4.86507997 BTC ( Transaction fees in left column ).

        12.5 + 4.86507997 = 17.36507997 BTC

    You can see the details of this transaction in the transaction part list . 

5. Constructing the block header

    A block header contains:
    - the previous block hash
    - the merkle tree root
    - data for the mining competition
        - version
        - timestamp
        - target: Proof of Work algorithm target for this block
        - nonce: the counter used for the Proof of Work algorithm


6. Mining

    The mining processing can be drawn as a picture below:

    ![](https://res.cloudinary.com/practicaldev/image/fetch/s--GNqcAqzL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q63cjkwbd8ktp3l1ogvj.jpg)

    Actually, mining is finding the nonce, the only input that changes every time we run the hash function(SHA256). It is very easy to prove that the nonce found indeed produces a valid hash. All the informations are available, everyone can run the hash function and confirm if the hash is valid or not. Because it is also impossible to predict what the nonce will be, it also act as a proof that the miner worked to get a valid hash ( Hence => _Proof-of-Work_ ).

7. Adjusting the target

    In Bitcoin, a block is mined every 10 minutes or so. 
    
    If the difficulty stays the same in the long term, while computer power increases, it will take less and less time to mine a block. To make sure this doesn't happen, the Proof of Work target is a dynamic parameter. 
    
    In the Bitcoin world, the target is adjusted every 2016 blocks. Then, we check the amount of time it took to mine those 2016 blocks. It should have taken 20160 minutes ( 2016 * 10 minutes ). The difficulty increases or decreases depending on the time it took to mine those blocks.

8. Succeeding in adding a block to the chain 

    After approximately 10 minutes of ~~tedious~~ computing, it discovers a valid hash. We can see the nonce used was 2469953656 ( Nonce in left column ).

    Joe's node immediately transmits the block to all its peers. They need to validate the new block before propagating it to its peers. The valid data should satisfy:
    - Block header hash is less than the target
    - Block size is within acceptable limits
    - Block timestamp is less than two hours in the future.
    - The first transaction is a coinbase transaction ( and only the first )
    - The coinbase transaction has a valid reward.
    - All transactions within the blocks are valid ( also have a checklist on their own )

    
--- 

## What does a block contain?
Take Bitcoin [Block #502426](https://www.blockchain.com/btc/block/00000000000000000020c60222099aaebc6e7795784f74628ec640b223d3d339) as an example.

There are three main sections:
- Summary
- Hashes
- Transactions

|Summary||
|:--|--|
|Number Of Transactions	|3189|
|Output Total|	12,402.76465986 BTC|
|Estimated Transaction Volume|	746.71850375 BTC|
|Transaction Fees|	4.86507997 BTC|
|Height|	502426 (Main Chain)|
|Timestamp	|2018-01-03 21:12:39|
|Received Time|	2018-01-03 21:12:39|
|Relayed By	|ViaBTC|
|Difficulty|	1,931,136,454,487.72|
|Bits	|402690497|
|Size	|1044.953 kB|
|Weight	|3992.801 kWU|
|Version	|0x20000000|
|Nonce	|2469953656|
|Block Reward|	12.5 BTC|

#### Number of transactions

It means this block contains 3189 pieces of transaction records. Actually you can find them on the link above.

#### Timestamp
Seconds from Unix Epoch. When the block was created.

#### Bits & Difficulty
Bits field 402690497 expresses the Proof-of-Work target as a coeffieint/exponent format and serves as a threshold. 

If we convert this number in hexadecimal, we get this: 0x180091C1.The first two hexadecimals digits ( 18 ) are the exponent, the next six ( 0091C1 ) are the coefficient. Here is the formula used to calculate the target from this:

\[  target = coefficient * 28 * (exponent - 3) \]

#### Nonce
This field is initialized to zero and increases by one. The goal is to find a value for the nonce that will results in hash lower than the target. So, the mining node will try billions or trillions of nonce values before it gets a valid hash.

|Hashes||
|:--|--|
|Hash	|00000000000000000020c60222099aaebc6e7795784f74628ec640b223d3d339|
|Previous Block|0000000000000000002e0350f057215ca847eb62d1422695f912a6866472340e|
|Merkle |root0fdde62cdb108811e0bf04cd16a634742140ebef4e5306005e5b50caa66b893c|
#### Hash

This is created by hashing the block header twice with the SHA256 algorithm

#### Previous Block

Remember that every block uses the previous block's hash to construct its own hash.

#### Merkle Tree

A merkle tree is constructed by recursively hashing pairs of nodes ( in this case, transactions ), until there is only one hash, called the root or merkle root. You can click [here](https://dev.to/damcosset/blockchain-what-is-in-a-block-48jo) for a more visualized explanation.

--- 

## Where are the bitcoin transactions stored?
There are two types of nodes in blockchain - **full node** and **lightweight node**.

Full nodes needs to have a copy of the entire blockchain, so every transaction and block that has ever taken place on the blockchain must be downloaded.

Lightweight nodes verify transactions using a method called simplified payment verification (SPV). SPV allows a node to verify if a transaction has been included in a block, without having to download the entire blockchain. With SPV, full nodes serve lightweight nodes by allowing them to connect and transmit their transactions to the network, and will notify them when a transaction affects them. A lightweight node need only download the headers of all blocks on the blockchain, which means that download and storage requirements are significantly less intensive than that of a full node.

--- 

## Are there any options for blockchain to store data?

Decentralized storage options are:
- Storing everything in blockchain itself
- Peer to peer file system, such as IPFS
- Decentralized cloud file storages, such as Storj, Sia, Ethereum Swarm, etc.
- Distributed Databases, such as Apache Cassandra, Rethink DB, etc.
- BigChainDB
- Ties DB

1. Storing everything in blockchain itself
Storing everything in blockchain is the simplest solution

    **_Drawback_**: 
    - Transactions to blockchain are slow to confirm. 
    - The immutability is the strength of blockchain that gives it high robustness but it is a weakness for a data storage. User may change their profile or replace their photo, still all the previous data will sit in blockchain forever and can be seen by anyone.

2. Peer to peer file system, such as IPFS
InterPlanetary File System(IPFS) allows to share files on client computers and unites them in the global file system. The technology is based on BitTorrent protocol and Distributed Hash Table.

    **_Advantages_**:
    -  It is really peer to peer - to share anything first put it on your own computer. It will be downloaded only if anyone needs it.
    -  It is content addressable, so it is impossible to forge content by the given address. Popular files can be downloaded very quickly thanks to BitTorrent protocol.

    **_Drawbacks_**:
    - You should stay online if you want to share your files. At least before someone becomes interested and wants to download them from you. 
    - It serves only static files, they can not be modified or removed once uploaded. And you can not search these files by their meaningful content.

3. Decentralized cloud file storages, such as Storj, Sia, Ethereum Swarm, etc.
From the user’s point of view these storages are just cloud storages like Dropbox, for example. The difference is that the content is hosted on user’s computers who offer their hard drive space for rent, rather than in datacenters.

    **_Advantages_**:
    - You don’t need to stay online to share your files anymore. Just upload the file and it is available in the cloud. These storages are highly reliable, fast enough, have enormous capacity.

    **_Drawbacks_**:
    - They serve static files only.
    - No content search (since they are built on the rented hardware, they are not free).

4. Distributed Databases, such as Apache Cassandra, Rethink DB, etc.
    Since we need to store structured data and seek for advanced query capabilities we may look at the distributed noSql databases.

    Why noSql? Because strict transactional SQL databases can not be truly distributed due to the restrictions of the CAP-theorem. To make a database distributed we must sacrifice either consistency or availability. NoSQL databases choose availability over consistency replacing it with so called “eventual consistency” where all the database nodes in the network become consistent some time later. 

    **_Advantages_**:
    - fast
    - scalable
    - fault tolerant
    - support rich query language

    **_Drawbacks_**: 
    - They are not Byzantine-proof. All the nodes of the cluster fully trust each other. So any malicious node can destroy the whole database.
5. BigChainDB
    BigChainDB is build upon RethinkDB cluster(an NoSQL database mentiond above). BigChainDB uses it to store all the blocks and transactions. That is why it shows such a high throughput - it is the one of the underlying noSQL database. All the BigChainDB nodes are connected to the cluster and have full write access to the database. 
    
    **_Advantages_**:
    - enormous data capacity 
    - really fast transactions
    - high throughput

    **_Drawbacks_**:
    -  The whole BigChainDB is not byzantine-proof! Any malicious BDB node can destroy the RethinkDB cluster. ( BigChainDB may be good for a private blockchain. )
6. Ties DB
    The TiesDB inherits the majority of features from the underlying noSQL databases and adds byzantine fault tolerance and incentives. With these features it can become a public database and enable feature-rich applications on Ethereum and other blockchains with smart contracts. 

    The database is writable by any user. But the users are identified by their public key and all the requests are signed. Once created, record remembers its creator who becomes an owner of the record. After that the record can be modified only by the owner. 
    
    Everyone can read all records, because the database is public. All the permissions are checked on request and replication. Additional permissions can be managed via a smart contract.

--- 

## Reference
- [Blockchain Wiki](https://en.wikipedia.org/wiki/Blockchain)
- [An adorable App Demo of Blockchain](https://medium.freecodecamp.org/how-does-blockchain-really-work-i-built-an-app-to-show-you-6b70cd4caf7d)
- [What is in a block](https://dev.to/damcosset/blockchain-what-is-in-a-block-48jo)
- [A detailed explanation of the mining process](https://dev.to/damcosset/blockchain-what-is-mining-2eod)
- [Where are the bitcoin transactions stored](https://www.quora.com/Where-is-the-blockchain-ledger-of-bitcoin-transactions-stored-and-how-is-it-being-backed-up)
- [How can blockchain be used as a database to store data?](https://www.quora.com/How-can-blockchain-be-used-as-a-database-to-store-data)
    




