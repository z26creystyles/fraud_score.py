# fraud_score.py
from web3 import Web3
import os

INFURA_URL = "https://sepolia.infura.io/v3/YOUR_INFURA_KEY"
PRIVATE_KEY = "YOUR_PRIVATE_KEY"
ACCOUNT = "YOUR_WALLET_ADDRESS"
CONTRACT_ADDRESS = "DEPLOYED_CONTRACT_ADDRESS"

w3 = Web3(Web3.HTTPProvider(INFURA_URL))

abi = [...]  # ABI from compiled contract

contract = w3.eth.contract(address=CONTRACT_ADDRESS, abi=abi)

nonce = w3.eth.get_transaction_count(ACCOUNT)

tx = contract.functions.lockTokens(
    "TOKEN_ADDRESS",
    1000000000000000000,  # 1 token (depends on decimals)
    int(w3.eth.get_block("latest").timestamp) + 3600
).build_transaction({
    "from": ACCOUNT,
    "nonce": nonce,
    "gas": 300000,
    "gasPrice": w3.to_wei("10", "gwei")
})

signed_tx = w3.eth.account.sign_transaction(tx, PRIVATE_KEY)
tx_hash = w3.eth.send_raw_transaction(signed_tx.rawTransaction)

print("Transaction sent:", w3.to_hex(tx_hash))