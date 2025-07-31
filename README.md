# mint-bot-base
from pathlib import Path

import zipfile



# Folder structure for the repo

repo_name = "mint-bot-base"

base_path = Path(f"/mnt/data/{repo_name}")

base_path.mkdir(parents=True, exist_ok=True)



# File contents

files = {

    "mint.py": """import json

import os

from web3 import Web3

from dotenv import load_dotenv



load_dotenv()



RPC_URL = os.getenv("RPC_URL")

PRIVATE_KEY = os.getenv("PRIVATE_KEY")

ADDRESS = Web3.to_checksum_address(os.getenv("ADDRESS"))

GAS_LIMIT = 300000



with open("config.json") as f:

    config = json.load(f)



web3 = Web3(Web3.HTTPProvider(RPC_URL))



def mint_nft(contract_address, mint_function, value_wei):

    contract_address = Web3.to_checksum_address(contract_address)

    contract = web3.eth.contract(address=contract_address, abi=config["abi"])



    nonce = web3.eth.get_transaction_count(ADDRESS)

    txn = contract.functions[mint_function]().build_transaction({

        "from": ADDRESS,

        "value": value_wei,

        "gas": GAS_LIMIT,

        "gasPrice": web3.eth.gas_price,

        "nonce": nonce,

    })



    signed_txn = web3.eth.account.sign_transaction(txn, private_key=PRIVATE_KEY)

    tx_hash = web3.eth.send_raw_transaction(signed_txn.rawTransaction)

    print("Minting... Tx Hash:", tx_hash.hex())



if __name__ == "__main__":

    mint_nft(config["contract_address"], config["mint_function"], int(config["value_wei"]))

""",



    "config.json": """{

  "contract_address": "0xYourNFTContractAddressHere",

  "mint_function": "mint",

  "value_wei": "0",

  "abi": [

    {

      "inputs": [],

      "name": "mint",

      "outputs": [],

      "stateMutability": "payable",

      "type": "function"

    }

  ]

}

""",



    ".env.example": """RPC_URL=https://base.llamarpc.com

PRIVATE_KEY=your_private_key_here

ADDRESS=your_wallet_address_here

""",



    "requirements.txt": """web3

python-dotenv

""",



    "README.md": """# Mint Bot for NFTs on Base



This bot automatically mints free or paid NFTs using a specified smart contract.



## 🛠 Setup



1. Clone this repo and install dependencies:


2. Create `.env` file based on `.env.example` with your wallet details.



3. Edit `config.json` with:

   - Contract address

   - Mint function name (usually `mint`)

   - Value in wei if paid mint



## ▶️ Run the Bot




## 📌 Note



- Works on Base mainnet/testnet

- Gas fee will be deducted, ensure balance in wallet

"""

}



# Write files

for filename, content in files.items():

    file_path = base_path / filename

    file_path.write_text(content)



# Zip the folder

zip_path = Path(f"/mnt/data/{repo_name}.zip")

with zipfile.ZipFile(zip_path, 'w') as zipf:

    for file in base_path.rglob('*'):

        zipf.write(file, file.relative_to(base_path.parent))



zip_path.name  # Return only the filename for reference
