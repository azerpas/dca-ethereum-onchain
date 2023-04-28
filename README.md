# Script to DCA on Ethereum (on-chain)

DCA (Dollar Cost Averaging) is an investment strategy where you buy a fixed amount of a certain asset at regular intervals, regardless of the price. This way you can avoid buying at the top of a bull run and you can take advantage of the dips.

This script allows you to DCA on Ethereum (on-chain) by swapping a fixed amount of a certain token for another token. It uses ChaCha20Poly1305 encryption to encrypt your mnemonic and store it in a file. The wallet is then used to sign the transactions. It will ask you for a password to encrypt your mnemonic once and to unlock your wallet every time you run the script.

Use CRON to run this script at regular intervals.

## Features
📈 Quote the swap price using [Uniswap V3 Quoter](https://docs.uniswap.org/contracts/v3/reference/periphery/lens/Quoter)   
👛 Encrypt your mnemonic using [ChaCha20Poly1305](https://docs.rs/orion/latest/orion/aead/index.html)

## Installation
💭 A later Installation guide for non-rust users will be added when the CI/CD pipeline is set up

For now, you can clone the repo and run the script using cargo
```sh
git clone git@github.com:azerpas/dca-ethereum-onchain.git
cd dca-ethereum-onchain
cargo run -- -h
```

[Example of running the script](#example)

## Usage
```
Usage: dca-onchain [OPTIONS] --amount-to-swap <AMOUNT_TO_SWAP> --token-in <TOKEN_IN> --token-out <TOKEN_OUT>

Options:
  -u, --rpc <RPC>
          HTTP URL of the RPC endpoint to connect to [default: https://mainnet.infura.io/v3/]
  -n, --chain-id <CHAIN_ID>
          Chain ID of the network to connect to (1 for mainnet, 3 for ropsten, 4 for rinkeby, 5 for goerli, 42 for kovan, for more checkout https://chainlist.org/) [default: 1]
  -a, --amount-to-approve <AMOUNT_TO_APPROVE>
          Amount of tokens to approve for the swap. Make sure to use the correct decimals, e.g 1 USDT = 1000000 as USDT has 6 decimals. If set to 0 it will approve the amount of tokens required for the swap. If the flag is not specified, it will skip the approval step
  -s, --amount-to-swap <AMOUNT_TO_SWAP>
          Amount of tokens to swap. If you don't use the 'd' (decimals) flag, make sure to use the correct decimals: e.g 1 USDT = 1000000 as USDT has 6 decimals. If you want to swap 0.5 tokens, you need to specify 0.5 * 10^decimals For example, if you want to swap 0.5 USDT, you need to specify 500000 (6 decimals) If you want to swap 0.5 WETH, you need to specify 500000000000000000 (18 decimals)
  -d, --fetch-decimals <FETCH_DECIMALS>
          Get the decimals of the token you want to swap from. If used, the given `amount_to_swap` and `amount_to_approve` will be multiplied by 10^`decimals` to get the correct amount. `decimals` are fetched from the token contract [possible values: true, false]
  -i, --token-in <TOKEN_IN>
          Address of the token to swap from. This is the token you want to sell. It must be a valid ERC20 token address (e.g USDT: 0xdAC17F958D2ee523a2206206994597C13D831ec7)
  -o, --token-out <TOKEN_OUT>
          Address of the token to swap to. This is the token you want to buy. It must be a valid ERC20 token address (e.g WETH: 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2)
  -h, --help
          Print help
  -V, --version
          Print version
```

### Example
Swap 1 UNI for some WETH on Goerli
```sh
cargo run -- -u "https://eth-goerli.g.alchemy.com/v2/{YOUR_API_KEY}" -n 5 -d true -a 1 -s 1 -i "0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984" -o "0xB4FBF271143F4FBf7B91A5ded31805e42b2208d6" 
```
- `-u` is the RPC endpoint to connect to. You can get an API key from an RPC provider like [Alchemy](https://www.alchemy.com/)
- `-n` is the chain ID of the network to connect to. 5 is for Goerli
- `-d` is used to fetch the decimals of the token you want to swap from. If used, the given `amount_to_swap` and `amount_to_approve` will be multiplied by 10^`decimals` to get the correct amount. `decimals` are fetched from the token contract. For example, if you want to swap 0.5 USDT, you need to specify 500000 (USDT having 6 decimals) without the `-d` flag. With the `-d` flag, you can specify 0.5 and the script will fetch the decimals from the USDT contract and multiply 0.5 by 10^6 to get the correct amount.
- `-a` is the amount of tokens to approve for the swap. Make sure to use the correct decimals as specified above or use the `-d` flag.
- `-s` is the amount of tokens to swap. Make sure to use the correct decimals as specified above or use the `-d` flag.
- `-i` is the address of the token to swap from. This is the token you want to sell. It must be a valid ERC20 token address, here it's UNI on Goerli
- `-o` is the address of the token to swap to. This is the token you want to buy. It must be a valid ERC20 token address, here it's WETH on Goerli 