// ✅ 1. Import crypto
const crypto = require('crypto');
const readline = require('readline');

// ✅ 2. Define the Block class
class Block {
    constructor(index, timestamp, data, previousHash = '') {
        this.index = index;
        this.timestamp = timestamp;
        this.data = data;
        this.previousHash = previousHash;
        this.hash = this.calculateHash();
        this.nonce = 0;ghbh
    }

    calculateHash() {
        return crypto.createHash('sha256')
            .update(this.index + this.timestamp + JSON.stringify(this.data) + this.previousHash + this.nonce)
            .digest('hex');
    }

    mineBlock(difficulty) {
        while (this.hash.substring(0, difficulty) !== Array(difficulty + 1).join("0")) {
            this.nonce++;
            this.hash = this.calculateHash();
        }
        console.log(`✅ Block mined: ${this.hash}`);
    }
}

// ✅ 3. Define the Blockchain class
class Blockchain {
    constructor() {
        this.chain = [this.createGenesisBlock()];
        this.difficulty = 2;
    }

    createGenesisBlock() {
        return new Block(0, new Date().toISOString(), "Genesis Block", "0");
    }

    getLatestBlock() {
        return this.chain[this.chain.length - 1];
    }

    addBlock(newBlock) {
        newBlock.previousHash = this.getLatestBlock().hash;
        newBlock.mineBlock(this.difficulty);
        this.chain.push(newBlock);
    }

    isChainValid() {
        for (let i = 1; i < this.chain.length; i++) {
            const current = this.chain[i];
            const previous = this.chain[i - 1];

            if (current.hash !== current.calculateHash()) {
                return false;
            }

            if (current.previousHash !== previous.hash) {
                return false;
            }
        }
        return true;
    }
}

// ✅ 4. CLI Input: Interact with blockchain from terminal
const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});

let myChain = new Blockchain();

function addBlockCLI() {
    rl.question('Enter transaction data: ', (input) => {
        myChain.addBlock(new Block(myChain.chain.length, new Date().toISOString(), { transaction: input }));
        console.log("✅ Block added!\n");
        console.log(JSON.stringify(myChain, null, 4));
        rl.question('➕ Do you want to add another block? (yes/no): ', (answer) => {
            if (answer.toLowerCase() === 'yes') {
                addBlockCLI();
            } else {
                console.log("\n🔒 Blockchain Final State:");
                console.log(JSON.stringify(myChain, null, 4));
                console.log("✅ Chain valid? ", myChain.isChainValid());
                rl.close();
            }
        });
    });
}

// ✅ Start the CLI
addBlockCLI();
