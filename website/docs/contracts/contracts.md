---
sidebar_position: 1
title: Contracts Intro
---

# Smart Contracts
---

## Communicating With Contracts

Interacting with smart contract can be a pretty straight forward process. Most calls to a smart contract are structured similarly, whether they are reading from or writing to the contracts.

One of the more important aspects of talking to smart contract, is knowing which functions are allowed to be called. Most of this is determined by the function visibility.

:::note Visibilities:

  * Public &rarr; Any address can call or modify
  * External &rarr; Any address, except the contract itself, can cal. The `this` keyword can be used allowing a contract to call it's own *external* functions, however, this pertains more to contract development than interaction.
  * Internal &rarr; Only the contract itself, and any contracts inheriting the said contract, can call.
  * Private &rarr; Only the contract itself can call.

:::

:::tip Note

All internal/private functions, in the **LegendaryLabs** contracts are indicated with a `_` prefixing the function name.

:::
  




<br/>


### Front End
---

In order to talk to the **LegendaryLabs** contract *ethers* will need to be imported.

``` js title="import ethers.js"
import { ethers } from "ethers";
```


:::info Todo

## Create Module For ethers Logic:

I will create a module to fit into our front end in order to simplify and abstract away some of the more *ethers* specific logic.

:::

#### Currently:

The smart contracts addresses will need to be imported from the current config file. 

``` js title="import contract addresses"
import {
  legendsLaboratory,
  legendsNFT,
  legendsToken,
  legendRejuvenation,
  legendsMarketplace,
  legendsMatchingBoard,
  laboratoryGovernor
} from 'contract_config/contract-config'
```

:::tip Note

New contract addresses can be generated by running `yarn hh:load`.

:::

The ABIs for the smart contracts will need importing as well. These come from the `artifacts/` directory.

``` js title="import contract ABIs"
import LegendsLaboratory from '../../artifacts/contracts/lab/LegendsLaboratory.sol/LegendsLaboratory.json'
import LegendsNFT from '../../artifacts/contracts/legend/LegendsNFT.sol/LegendsNFT.json'
import LegendToken from '../../artifacts/contracts/token/LegendToken.sol/LegendToken.json'
import LegendRejuvenation from '../../artifacts/contracts/lab/LegendRejuvenation.sol/LegendRejuvenation.json'
import LegendsMarketplace from '../../artifacts/contracts/marketplace/LegendsMarketplace.sol/LegendsMarketplace.json'
import LegendsMatchingBoard from '../../artifacts/contracts/matching/LegendsMatchingBoard.sol/LegendsMatchingBoard.json'
import LaboratoryGovernor from '../../artifacts/contracts/token/LaboratoryGovernor.sol/LaboratoryGovernor.json'
```

:::tip Note

The `artifacts/` directory is included in the `.gitIgnore` and will need to be regenerated after cloning the repo or modifying the contracts. This can be done by running `yarn hh:com`.

:::

A `provider` & `signer` variable is needed to tell the contracts who the call is coming from `provider`, and verify the call is authorized `signer`.

Function calls that only *read* data from a contract require a `provider` and should cost (0) gas. 

Function calls that are *writing* to a contract are required to have a `signer`, as they must authorize the gas fee.

``` js title="provider & signer variables"
const provider = new ethers.providers.Web3Provider(window.ethereum)
const signer = provider.getSigner()
```

The `contract` object is then defined as such:

``` js title="contract object"
const contract = {
  lab: {
    read: new ethers.Contract(legendsLaboratory, LegendsLaboratory.abi, provider),
    write: new ethers.Contract(legendsLaboratory, LegendsLaboratory.abi, signer),
  },
  token: {
    read: new ethers.Contract(legendsToken, LegendToken.abi, provider),
    write: new ethers.Contract(legendsToken, LegendToken.abi, signer),
  },
  nft: {
    read: new ethers.Contract(legendsNFT, LegendsNFT.abi, provider),
    write: new ethers.Contract(legendsNFT, LegendsNFT.abi, signer),
  },
  rejuvenation: {
    read: new ethers.Contract(legendRejuvenation, LegendRejuvenation.abi, provider),
    write: new ethers.Contract(legendRejuvenation, LegendRejuvenation.abi, signer),
  },
  marketplace: {
    read: new ethers.Contract(legendsMarketplace, LegendsMarketplace.abi, provider),
    write: new ethers.Contract(legendsMarketplace, LegendsMarketplace.abi, signer),
  },
  matchingBoard: {
    read: new ethers.Contract(legendsMatchingBoard, LegendsMatchingBoard.abi, provider),
    write: new ethers.Contract(legendsMatchingBoard, LegendsMatchingBoard.abi, signer),
  },
  governor: {
    read: new ethers.Contract(laboratoryGovernor, LaboratoryGovernor.abi, provider),
    write: new ethers.Contract(laboratoryGovernor, LaboratoryGovernor.abi, signer),
  },
}
```



You are now ready to interact with the **Legendary Labs** contracts. As previously mentioned, the majority of calls are structured identically to one another. Simply swap out the contract-function name and include the correct parameters and you should be good to go.

All functions calling the smart contracts will need to be wrapped in an `if` statement verifying the client has a web3 wallet accessible.

``` js title="web3 wrapper"
async function callSmartContract() {
  if (typeof window.ethereum !== 'undefined') {
    // logic
  }
}
```

An additional variable will sometimes be needed if sending in an `address` parameter as `msg.sender`.

``` js title="account variable"
const [account] = await window.ethereum.request({ method: 'eth_requestAccounts' })
```

``` js title="read from a contract"
async function fetchURI() {
  if (typeof window.ethereum !== 'undefined') {
    const legendURI = await contract.nft.read.fetchLegendURI(legendId)
  }
}
```

``` js title="write to a contract"
async function createPromoEvent() {
  if (typeof window.ethereum !== 'undefined') {
    await contract.lab.write.createPromoEvent(eventName, duration, isUnrestricted, maxTickets, skipIncubation)
  }
}
```


:::info Todo

## Develop API:

I plan to create an API for the front end to use, to further simplify the interactions between the smart contract and front end. Particularly for more complex functionalities we develop that chain multiple contract calls together.

:::

---

<br/>

## Glossary Of Terms
#### Terms throughout all Legendary Labs contracts and LabDocs
---