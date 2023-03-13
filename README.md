# How to register dApp to Cardano + submit CIP-72 format metadata 

## Set up cardano-cli
[https://github.com/piotr-iohk/cardano-up](https://github.com/piotr-iohk/cardano-up)


## Set up wallet

1. Create Payment Keys\
`$ cardano-cli address key-gen --verification-key-file payment.vkey --signing-key-file payment.skey`
2. To generate a stake key pair\
`$ cardano-cli stake-address key-gen --verification-key-file stake.vkey --signing-key-file stake.skey`
3. Create Wallet Address\
`$ cardano-cli address build --payment-verification-key-file payment.vkey --stake-verification-key-file stake.vkey --out-file payment.addr (--mainnet | --testnet-magic NATURAL)`
4. Request Test ADA at [https://docs.cardano.org/cardano-testnet/tools/faucet](https://docs.cardano.org/cardano-testnet/tools/faucet)\
NB transaction fee it's a variable amount, something around 190.000 lovelace
5. Retrieves the node’s current pool parameters\
`$ cardano-cli query protocol-parameters --out-file protocol.json (--mainnet | --testnet-magic NATURAL)`
6. Query tip\
`$ cardano-cli query tip (--mainnet | --testnet-magic NATURAL)`
7. Install packages\
`$ yarn install`
8. Generate key pair\
`$ yarn generate-key-pair`\
Copy `publicKey` and `secretKey` to `.env` file (see step 9)
9. Copy .env_example to .end and edit it accordingly\
`$ cp .env_example .env`
10. Copy `cip26_example.yml` to `cip26.yml` and edit it accordingly
11. Launch **cip26-cli** and follow the instructions\
`$ yarn start`

### Testnet-magic numbers
- 9: Devnet. 	`--testnet-magic=9`
- 2: Preview. 	`--testnet-magic=2`
- 1: Preprod. 	`--testnet-magic=1`


### Useful commands

Ref.: [https://docs.cardano.org/development-guidelines/use-cli](https://docs.cardano.org/development-guidelines/use-cli)

Query UTXO
```
$ cardano-cli query utxo \
  --address $(< payment.addr) \
  (--mainnet | --testnet-magic NATURAL)

Output:
                           TxHash                                 TxIx        Amount
--------------------------------------------------------------------------------------
99b8fcac018828b6f6b29eb55211ac5e69a8651736c4c3d6280ca4524fd41778     0        9998003445 lovelace + TxOutDatumNone

```

To create draft transaction:
```
$ cardano-cli transaction build-raw \
--tx-in 99b8fcac018828b6f6b29eb55211ac5e69a8651736c4c3d6280ca4524fd41778#0 \
--tx-out $(cat payment.addr)+0 \
--metadata-json-file metadata.json \
--fee 0 \
--out-file tx.draft
```

To calculate the transaction fee: 
```
$ cardano-cli transaction calculate-min-fee \
--tx-body-file tx.draft \
--tx-in-count 1 \
--tx-out-count 1 \
--witness-count 1 \
--byron-witness-count 0 \
(--mainnet | --testnet-magic NATURAL) \
--protocol-params-file protocol.json
```

Then we calculate the total amount of our wallet minus the calculated fee as the total output amount\
`$ expr 9998003445 - 180285 = 9997823160`

To build final transaction:
```
$ cardano-cli transaction build-raw \
--tx-in 99b8fcac018828b6f6b29eb55211ac5e69a8651736c4c3d6280ca4524fd41778#0 \
--tx-out $(cat payment.addr)+9997823160 \
--metadata-json-file metadata.json \
--fee 180285 \
--out-file tx.draft
```

To sign the transaction:
```
$ cardano-cli transaction sign \
--tx-body-file tx.draft \
--signing-key-file payment.skey \
(--mainnet | --testnet-magic NATURAL) \
--out-file tx.signed
```

To submit the transaction:\
`$ cardano-cli transaction submit --tx-file tx.signed (--mainnet | --testnet-magic NATURAL)`

### Testnets faucet
[https://docs.cardano.org/cardano-testnet/tools/faucet](https://docs.cardano.org/cardano-testnet/tools/faucet)



https://www.shorturl.at/




** 001 **
BLOCK:0536732 █ TX:65 █ META   { label: 1667, content: {"GITHUB":"https://github.com/lucadonazzon/cip72/metadata/001/cip72.json","rootHash":"62479bde5c1209b769373d235f1c95fb719e3505c9b4c15509e0a6fab834419e","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"12806bc4d317bad140c82020a11d9b824f78391a440c6a9bd4692f9e98f16bfd","s":"60fd4f3da3db5717f0fc49c4e6b22f0cd91c12ac7503121c62d93566b6177206"},"subject":"FakeNMKR-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }

https://github.com/lucadonazzon/cip72/blob/main/metadata/001/cip72.json
shorturl.at/dqC28
BLOCK:0539136 █ TX:71 █ META   { label: 1667, content: {"metadata":["shorturl.at/dqC28"],"rootHash":"9128f4aec38d0034b8643328e7889fc7624f4db048066b51115c764174452264","schema_version":"0.0.1","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"50f3063601240b0029b9661ecee6f80a23774f6cb35cb3a5ac7db5fbc8e7f093","s":"c4d51d2ac6a534724eb144e59034b472aeee5edc2ec15b9001f96f202eec3204"},"subject":"FakeNMKR-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }

** 002 **
BLOCK:0536758 █ TX:29 █ META   { label: 1667, content: {"GITHUB":"https://github.com/lucadonazzon/cip72/metadata/002/cip72.json","rootHash":"a259d8facd95e2fc20fb9017237dc3f311e6ca7ac4ba30dae24bac7fbe75c088","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"981ba0a9f0b9599ef6a6d9ad1530bac070c5c1b6283b97ea5ed08e14e6de95ec","s":"1e2fd2c5b0c96fd1b2471b62e2ab7537795b72d9f27008bd7c9e9fefd45b8c08"},"subject":"FakeRayWallet-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }

https://github.com/lucadonazzon/cip72/blob/main/metadata/002/cip72.json
shorturl.at/yET03
BLOCK:0539144 █ TX:29 █ META   { label: 1667, content: {"metadata":["shorturl.at/yET03"],"rootHash":"b820f38acf06085220d19d3b7da0062d8a27fb0113b12c3885afffbbc67354f8","schema_version":"0.0.1","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"a6f004d6d810f363ff866046cad9a66f422004a80f2b4ba9c24f7a8723d004fc","s":"e186556e3d72660fdfc313a18e972e7f26321f833c689e8f021407dd7f8f3d0f"},"subject":"FakeRayWallet-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }


** 003 **
BLOCK:0536770 █ TX:03 █ META   { label: 1667, content: {"GITHUB":"https://github.com/lucadonazzon/cip72/metadata/003/cip72.json","rootHash":"686bf8311543301e9f0eef90b96f3dc1d42a4331c737616d2ef972e630ad56a3","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"12837f5503d459df29cee8161ad2a177a3531bffa9fdebde11f38116338bcd97","s":"0936af9d8f150d550cfe364e8f33c35a7023ecebeed4fb2629ef163dafe56500"},"subject":"FakeArtano-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }

https://github.com/lucadonazzon/cip72/blob/main/metadata/003/cip72.json
shorturl.at/vQRV9
BLOCK:0539150 █ TX:31 █ META   { label: 1667, content: {"metadata":["shorturl.at/vQRV9"],"rootHash":"686bf8311543301e9f0eef90b96f3dc1d42a4331c737616d2ef972e630ad56a3","schema_version":"0.0.1","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"657d1930be8da77a03f5c1fa784affbf7d08fbfc9648581bfb5e974b52f107f6","s":"6c1bc8448e52c850e19a76e9729e30e1a116d7b0d4c1cf998116dfc9254e3802"},"subject":"FakeArtano-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }

** 004 **
BLOCK:0536817 █ TX:00 █ META   { label: 1667, content: {"GITHUB":"https://github.com/lucadonazzon/cip72/metadata/004/cip72.json","rootHash":"e778b782c79a2b2961dbfdc579485721a1e225b174016269d30226add46707b5","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"fe4493f7e0f09acdd94d192232191e84be4ab92bc41ffff4054af305b840d7ec","s":"8581aa5fb8fdb3cc7f002b0f941265d626f3c68076eea9e5aabaf6d617897701"},"subject":"FakeBlockfrost-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }

https://github.com/lucadonazzon/cip72/blob/main/metadata/004/cip72.json
shorturl.at/vQRV9

** 005 **
BLOCK:0536829 █ TX:60 █ META   { label: 1667, content: {"GITHUB":"https://github.com/lucadonazzon/cip72/metadata/005/cip72.json","rootHash":"ba99831cce7a987eb922af91f523eac374dcdd14a49914a05b8b79e1af3b6485","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"48b52088b04c9a3a22956756fc48cf37b5798c2e262a42bfb0c94566c206af5c","s":"d6888e50b86c12ea2fca49e2b95df21f0d7ebaa259f9ca9240070d5c1655340f"},"subject":"FakeJpgStore-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }

** 006 **
BLOCK:0536845 █ TX:27 █ META   { label: 1667, content: {"GITHUB":"https://github.com/lucadonazzon/cip72/metadata/006/cip72.json","rootHash":"27c01b27778ac6781417bb05912514fb405b4299d8c263a96875381386789227","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"16fbfc8dc9bb8d78a9459f9b1dc57ca9376b903d2b0293fe5e05dd39ddfce3ba","s":"8d01e60747513f56f5206c5256d8611673072c00cba6c3d1cc417ca348a99f04"},"subject":"FakeSundaySwap-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }

** 007 **
BLOCK:0537448 █ TX:32 █ META   { label: 1667, content: {"GITHUB":"https://github.com/lucadonazzon/cip72/metadata/007/cip72.json","rootHash":"8a02864cea954b322e7ece9038ae12767b497c351b214978245e468e0ecb5119","signature":{"algo":"Ed25519−EdDSA","pub":"9b4139c173fd74898d4a562ef38adf56d49985fc749fe41149bad9a74006e55c","r":"f82ad4a5d90d34a854388f05c8e84af4a4c002247f6451f0d87b6162ec1c1731","s":"f5d91e779d13be5f7407d855ae0bde631a216628850b16caa0e3e60262db8005"},"subject":"FakeNMKR-003","type":{"action":"REGISTER","releaseName":"My first release","releaseNumber":"0.0.1"}} }
