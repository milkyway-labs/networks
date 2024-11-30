# MilkyWay Mainnet

MilkyWay is a modular restaking protocol that allows users to restake their tokens and receive rewards from services
distributing them.

## Details

|   Chain ID   |          `milkyway`          |
|:------------:|:----------------------------:|
| Launch date  |     2024-12-03T09:00:00Z     |
| Genesis file | [genesis.json](genesis.json) |
| Genesis hash |                              |

## Endpoints 🎯

|   Service   |                   URL                    |
|:-----------:|:----------------------------------------:|
| Public RPC  | `https://rpc.mainnet.milkyway.zone:443`  |
| Public LCD  | `https://lcd.mainnet.milkyway.zone:443`  |
| Public gRPC | `https://grpc.mainnet.milkyway.zone:443` |

## Join the network 📜

To set up a validator node for this network, please
follow [the guide on our website](https://docs.milkyway.zone/modular-restaking/guides/consensus/validator-node).

⚠ **This is a POA network** ⚠

We have decided to launch our mainnet as a POA network. This means that the only way you can obtain 1 MILK token that
can be self-delegated to join the consensus of the chain, is by asking our team. To give it to you, we will need an
account address with which we will create a vesting account containing such token.
Once that is done, you will be able to self-delegate it to the validator you will be running.

## Genesis details 📝

### Denoms

In the genesis file, you will find references to the following denoms:

* `umilk`: this is the MILK token that will be used as the staking token for the network.
  The initial supply of the token will be **20 units**, and each genesis validator will receive 1 token that will be
  vested for 1 year starting from 1st January 2025. The remaining tokens will be held by a foundation account and will
  be used to onboard future validators if needed. This mechanism ensures the creation of a Proof-of-Authority network
  enabled to onboard at most 20 validators.
* `ugas`: this is a gas token that will only be used during the chain setup phase. The initial supply will be of 100
  units, and those will be distributed to service operators such as IBC relayers in order for them to perform
  transactions without requiring the chain to run as a zero-fee network (which might expose it to DDoS attacks). Once
  the initial setup has been completed, the validator nodes will switch to using IBC-bridged assets as the fee tokens
  themselves, thus making the `ugas` token obsolete.

### IBC Denoms
Inside the genesis file, there are some IBC denoms that have been pre-computed.

#### Osmosis -> MilkyWay assets

The following IBC assets have been assumed to come from the Osmosis chain through channel `channel-0` and port
`transfer`:

**TIA**  
Osmosis denom: `ibc/D79E7D83AB399BFFF93433E54FAA480C191248FC556924A2A8351AE2638B3877`  
MilkyWay denom: `ibc/9ACD338BC3B488E0F50A54DE9A844C8326AF0739D917922A9CE04D42AD66017E`

**milkTIA**  
Osmosis denom: `factory/osmo1f5vfcph2dvfeqcqkhetwv75fda69z7e5c2dldm3kvgj23crkv6wqcn47a0/umilkTIA`  
MilkyWay denom: `ibc/16065EE5282C5217685C8F084FC44864C25C706AC37356B0D62811D50B96920F`

**stTIA**  
Osmosis denom: `ibc/698350B8A61D575025F3ED13E9AC9C0F45C89DEFE92F76D5838F1D3C1A7FF7C9`  
MilkyWay denom: `ibc/84FBEC4BBB48BD7CC534ED7518F339CCF6C45529DC00C7BFB8605C9EE7D68AFC`

Following the code that has been used to generate the above denoms:

```go
denoms := []struct {
		AssetName    string
		OsmosisDenom string
}{
   {
      AssetName:    "TIA",
      OsmosisDenom: "ibc/D79E7D83AB399BFFF93433E54FAA480C191248FC556924A2A8351AE2638B3877",
   },
   {
      AssetName:    "milkTIA",
      OsmosisDenom: "factory/osmo1f5vfcph2dvfeqcqkhetwv75fda69z7e5c2dldm3kvgj23crkv6wqcn47a0/umilkTIA",
   },
   {
      AssetName:    "stTIA",
      OsmosisDenom: "ibc/698350B8A61D575025F3ED13E9AC9C0F45C89DEFE92F76D5838F1D3C1A7FF7C9",
   },
}

for _, denom := range denoms {
   hash := sha256.Sum256([]byte(fmt.Sprintf("transfer/channel-0/%s", denom.OsmosisDenom)))

   println(fmt.Sprintf("**%s**", denom.AssetName))
   println(fmt.Sprintf("Osmosis denom: %s", denom.OsmosisDenom))
   println(fmt.Sprintf("MilkyWay denom: ibc/%X", hash))
   println()
}
```

#### Celestia -> MilkyWay assets

The following IBC assets have been assumed to come from the Osmosis chain through channel `channel-1` and port
`transfer`:

**TIA**  
Celestia denom: `utia `
MilkyWay denom: `ibc/F1183DB3D428313A6FD329DF18219F9D6B83257D07D292EA9EC1D877E89EC2B0`

Following the code that has been used to generate the above denoms:

```go
denoms := []struct {
		AssetName     string
		CelestiaDenom string
}{
   {
      AssetName:     "TIA",
      CelestiaDenom: "utia",
   },
}

for _, denom := range denoms {
   hash := sha256.Sum256([]byte(fmt.Sprintf("transfer/channel-1/%s", denom.CelestiaDenom)))

   println(fmt.Sprintf("**%s**", denom.AssetName))
   println(fmt.Sprintf("Celestia denom: %s", denom.CelestiaDenom))
   println(fmt.Sprintf("MilkyWay denom: ibc/%X", hash))
   println()
}
```

### IBC packet forwarding denoms

The following IBC assets have been assumed to be forwarded to the MilkyWay chain through the Osmosis chain:

**TIA**
Osmosis IBC denom trace:

* Base denom: `utia`
* Path: `transfer/channel-6994`
* Computed IBC denom: `ibc/D79E7D83AB399BFFF93433E54FAA480C191248FC556924A2A8351AE2638B3877`

MilkyWay IBC denom trace:

* Base denom: `utia`
* Path: `transfer/channel-0/transfer/channel-6994`
* Computed IBC denom: `ibc/6C349F0EB135C5FA99301758F35B87DB88403D690E5E314AB080401FEE4066E5`

**stTIA**  
Osmosis IBC denom trace:

* Base denom: `stutia`
* Path: `transfer/channel-326`
* Computed IBC denom: `ibc/698350B8A61D575025F3ED13E9AC9C0F45C89DEFE92F76D5838F1D3C1A7FF7C9`

MilkyWay IBC denom trace:

* Base denom: `stutia`
* Path: `transfer/channel-0/transfer/channel-326`
* Computed IBC denom: `ibc/8D4FC51F696E03711B9B37A5787FB89BD2DDBAF788813478B002D552A12F9157`

### Accounts

The genesis file will contain the details (or references) of the following accounts:

* `milk108zdtldyt6r98rlg6la6nvwczzxnh2mjajlj4g`: this is the Foundation account that will be used to hold the remaining
  tokens of the MILK token that have not been vested to the validators as well as the GAS tokens.
* `milk1t08mx78mvffdpqmd33gzslghra22ccfftc8cjq`: this is the account that will be enabled to deposit on behalf of users
  to their `x/liquidvesting` insurance fund. It will also be the account enabled to mint and burn locked tokens
  representations.