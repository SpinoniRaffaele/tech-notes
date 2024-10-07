The graph works only on actual testnets (you cannot connect it with your ganache or hardhat localhost like you could do with Moralis).

the first step to work with the graph is therefore to deploy your smart contracts to testnets.

# What is it?
It is a decentralized middleware that stores blockchain event's data, it is a network of multiple nodes that fetches blockchain data and stores it. The graph exposes an API to retrieve the stored data.

**Why?**
Blockchain is not designed to retrieve data, it is not indexed and searching data on it is simply not practical. The graph is the indexing layer filling this gap in retrieving information from chains.

When you want to work with the graph, you need to build a subgraph where you define your specific data models, network, contract addresses and other configs. After that you will deploy your subgraph you can test it in the GUI in the website, then you can interact programmatically with it.


## Example
Go to subgraph studio (in the official website) -> create a subgraph and select the chain
install gloabally the npm package of the graph-cli
then you can `graph init` and follow the prompt (this creates a minimal project with package.json and schema.graphql: the graphql syntax for defining the schema of the DB, subgraph.yml config for the subgraph)

example of schema.graphql
```graphql
type ActiveItem @entity {
    id: ID!
    buyer: Bytes! # address
    seller: Bytes! # address
    nftAddress: Bytes! # address
    tokenId: BigInt!
    price: BigInt
}
```
The CLI command `graph codegen` will then generate the typescript types for this schema

In the src directory you can see the mappings.ts file which contains a list of functions that are triggered whenever an event of a specific type is raised in the chain.
In the subgraph.yml file the mapping between the event and the function name is declared

By default the subgraph.yml will search for events since the genesis block of the blockchain which is not really practical, so you can define a `startblock:  1234` under `dataSources -> source`

Example of mappings.ts
```typescript
import { BigInt, Address } from "@graphprotocol/graph-ts"  
//the graph provides utility types
import {
    NftMarketplace,
    ItemBought as ItemBoughtEvent,
    ItemCanceled as ItemCanceledEvent,
    ItemListed as ItemListedEvent,
} from "../generated/NftMarketplace/NftMarketplace"
import { ItemListed, ActiveItem, ItemBought, ItemCanceled } from "../generated/schema"

export function handleItemListed(event: ItemListedEvent): void {
	//ItemListed.load will load an element from the ItemListed table
    let itemListed = ItemListed.load(
        getIdFromEventParams(event.params.tokenId, event.params.nftAddress)
    )
    let activeItem = ActiveItem.load(
        getIdFromEventParams(event.params.tokenId, event.params.nftAddress)
    )
    if (!itemListed) {
        itemListed = new ItemListed(
            getIdFromEventParams(event.params.tokenId, event.params.nftAddress)
        )
    }
    if (!activeItem) {
        activeItem = new ActiveItem(
            getIdFromEventParams(event.params.tokenId, event.params.nftAddress)
        )
    }
    itemListed.seller = event.params.seller
    activeItem.seller = event.params.seller
    itemListed.nftAddress = event.params.nftAddress
    activeItem.nftAddress = event.params.nftAddress
    itemListed.tokenId = event.params.tokenId
    activeItem.tokenId = event.params.tokenId
    itemListed.price = event.params.price
    activeItem.price = event.params.price
    activeItem.buyer = Address.fromString("0x0000000000000000000000000000000000000000")

	// itemListed.save will create a new entry in the table
    itemListed.save()
    activeItem.save()
}

function getIdFromEventParams(tokenId: BigInt, nftAddress: Address): string {
    return tokenId.toHexString() + nftAddress.toHexString()
}
```

After the development you can deploy your subgraph by copying the login token from the website and running:
```
graph auth --studio <token>
graph codegen && graph build
graph deploy --studio <projName>
```
Then in the website you have a playground to execute some graphQL queries over the decentralized DB.

## Interact with the graph from a frontend
Install the *@apollo/client* and *graphql*

```javascript
import {useQuery, gql, ApolloClient, InMemoryCache} from "@apollo/client"

const QUERY = gql`QueryCodeHere`

function initializeClient() {
	const client = new ApolloClient({
	cache: new InMemoryCache(),
	uri: "https://api.studio....." 
	// you can see your subgraph URL in the graph website (centrlaized gateway)
	})
}

function graphInteraction() {
	const {loading, error, data} = useQuery(QUERY)
}
```

