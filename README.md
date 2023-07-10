import hashlib
import datetime

# Define a basic Block class
class Block:
    def __init__(self, index, timestamp, data, previous_hash):
        self.index = index
        self.timestamp = timestamp
        self.data = data
        self.previous_hash = previous_hash
        self.hash = self.calculate_hash()

    def calculate_hash(self):
        data_string = str(self.index) + str(self.timestamp) + str(self.data) + str(self.previous_hash)
        return hashlib.sha256(data_string.encode()).hexdigest()

# Define the Blockchain class
class Blockchain:
    def __init__(self):
        self.chain = [self.create_genesis_block()]

    def create_genesis_block(self):
        return Block(0, datetime.datetime.now(), "Genesis Block", "0")

    def get_latest_block(self):
        return self.chain[-1]

    def add_block(self, new_block):
        new_block.previous_hash = self.get_latest_block().hash
        new_block.hash = new_block.calculate_hash()
        self.chain.append(new_block)

    def is_chain_valid(self):
        for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i - 1]

            if current_block.hash != current_block.calculate_hash():
                return False

            if current_block.previous_hash != previous_block.hash:
                return False

        return True

# Define a basic cryptocurrency class
class Cryptocurrency:
    def __init__(self, name, symbol):
        self.name = name
        self.symbol = symbol
        self.blockchain = Blockchain()
        self.pending_transactions = []

    def create_transaction(self, sender, receiver, amount):
        self.pending_transactions.append({
            'sender': sender,
            'receiver': receiver,
            'amount': amount
        })

    def mine_pending_transactions(self, miner):
        block = Block(len(self.blockchain.chain), datetime.datetime.now(), self.pending_transactions, self.blockchain.get_latest_block().hash)
        self.blockchain.add_block(block)
        self.pending_transactions = []
        print("Block mined successfully!")
        print(f"Miner: {miner}")
        print(f"Blockchain length: {len(self.blockchain.chain)}")
        print(f"Block hash: {block.hash}")
        print("")

    def print_blockchain(self):
        for block in self.blockchain.chain:
            print(f"Block {block.index}:")
            print(f"Timestamp: {block.timestamp}")
            print(f"Data: {block.data}")
            print(f"Previous Hash: {block.previous_hash}")
            print(f"Hash: {block.hash}")
            print("")

# Create an instance of the cryptocurrency
my_coin = Cryptocurrency("MyCoin", "MYC")

# Create transactions
my_coin.create_transaction("Alice", "Bob", 10)
my_coin.create_transaction("Bob", "Charlie", 5)

# Mine pending transactions
my_coin.mine_pending_transactions("Miner1")

# Print blockchain
my_coin.print_blockchain()

# Create another transaction
my_coin.create_transaction("Charlie", "Alice", 3)

# Mine pending transactions again
my_coin.mine_pending_transactions("Miner2")

# Print blockchain again
my_coin.print_blockchain()
