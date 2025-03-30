# Passcode

## Challenge Description
> ![image](https://github.com/user-attachments/assets/a0f5b4f7-5712-4ef8-b994-5627e8b9e07c)

## Solution
Since this was my first time doing a blockchain challenge, I seeked help from ChatGPT and followed a structured approach to solve it.

First, we need to set up the required environment variables as provided in the challenge description to interact with the blockchain:
```bash
RPC_URL=http://10.10.232.250:8545
API_URL=http://10.10.232.250
PRIVATE_KEY=$(curl -s ${API_URL}/challenge | jq -r ".player_wallet.private_key")
CONTRACT_ADDRESS=$(curl -s ${API_URL}/challenge | jq -r ".contract_address")
PLAYER_ADDRESS=$(curl -s ${API_URL}/challenge | jq -r ".player_wallet.address")
```

To determine if the contract has been exploited successfully, we run:
```bash
is_solved=$(cast call $CONTRACT_ADDRESS "isSolved()(bool)" --rpc-url ${RPC_URL})
echo "Check if solved: $is_solved"
```

![image](https://github.com/user-attachments/assets/e3512675-e695-4058-a19d-5f5d33f67dd6)

If the output is `false`, the contract remains unexploited

We need to interact with the smart contract. To find clues, we inspect the contract’s storage first:
```bash
cast storage $CONTRACT_ADDRESS 0 --rpc-url ${RPC_URL}
```

The output reveals a stored value in slot 0:
```
0x54484d7b776562335f6834636b316e675f636f64657d0000000000000000002c
```

![image](https://github.com/user-attachments/assets/d830012c-cbe1-4198-9af8-e5dbc46d18fc)

This appears to be a hex-encoded flag! Let's decode it.

To reveal the flag, we convert the hex value to ASCII:
```bash
echo -n "54484d7b776562335f6834636b316e675f636f64657d" | xxd -r -p
```

This successfully decodes to:
```
THM{web3_h4ck1ng_code}
```

![image](https://github.com/user-attachments/assets/109ce133-e424-4b65-a144-b43a7a2e5e98)

## Conclusion
By analyzing the contract’s storage, we discovered that the passcode (flag) was stored in plaintext at slot 0.

## Flag
THM{web3_h4ck1ng_code}

