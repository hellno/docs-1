---
description: >-
  Learn how to use Airstack to build token gating with ERC6551 token-bound
  accounts.
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: true
---

# 🚪 Token Gating

With ERC6551 accounts, users can now own assets that are owned by several layers of NFTs.

With such a feature, traditional token-gating with regular ERC20s, ERC721s, or ERC1155s will not be able to detect assets if they are owned by an ERC6551 token-bound account, even if technically they are owned and controlled by a certain user.

In order to provide proper token-gating for assets owned by ERC6551 token-bound accounts, it requires several steps to ensure that verification is done not just on the EOA level, but also on the proceeding ERC6551 accounts within the NFT ownership tree.

Thus, the algorithm for ERC6551 token-gating will be as follows:

1. [Verify ownership of Token(s) or NFT(s) on an EOA](token-gating.md#step-1-verify-ownership-of-token-s-or-nft-s-on-an-eoa)
2. [Get all token-bound ERC6551 accounts](token-gating.md#step-2-get-all-token-bound-erc6551-accounts)
3. [Verify ownership of Token(s) or NFT(s) on ERC6551 accounts](token-gating.md#step-3-verify-ownership-of-token-s-or-nft-s-on-erc6551-accounts)
4. [Iterate on the next ERC6551 accounts level](token-gating.md#step-4-iterate-on-the-next-erc6551-accounts-level)

## Pre-requisites

* An [Airstack](https://airstack.xyz/) account
* Basic knowledge of GraphQL
* Basic knowledge of [ERC6551](https://eips.ethereum.org/EIPS/eip-6551)

## Get Started

#### JavaScript/TypeScript/Python

If you are using JavaScript/TypeScript or Python, Install the Airstack SDK:

{% tabs %}
{% tab title="npm" %}
**React**

```sh
npm install @airstack/airstack-react
```

**Node**

```sh
npm install @airstack/node
```
{% endtab %}

{% tab title="yarn" %}
**React**

```sh
yarn add @airstack/airstack-react
```

**Node**

```sh
yarn add @airstack/node
```
{% endtab %}

{% tab title="pnpm" %}
**React**

```sh
pnpm install @airstack/airstack-react
```

**Node**

```sh
pnpm install @airstack/node
```
{% endtab %}

{% tab title="pip" %}
```sh
pip install airstack
```
{% endtab %}
{% endtabs %}

Then, add the following snippets to your code:

{% tabs %}
{% tab title="React" %}
```jsx
import { init, useQuery } from "@airstack/airstack-react";

init("YOUR_AIRSTACK_API_KEY");

const query = `YOUR_QUERY`; // Replace with GraphQL Query

const Component = () => {
  const { data, loading, error } = useQuery(query);

  if (data) {
    return <p>Data: {JSON.stringify(data)}</p>;
  }

  if (loading) {
    return <p>Loading...</p>;
  }

  if (error) {
    return <p>Error: {error.message}</p>;
  }
};
```
{% endtab %}

{% tab title="Node" %}
```javascript
import { init, fetchQuery } from "@airstack/node";

init("YOUR_AIRSTACK_API_KEY");

const query = `YOUR_QUERY`; // Replace with GraphQL Query

const { data, error } = await fetchQuery(query);

console.log("data:", data);
console.log("error:", error);
```
{% endtab %}

{% tab title="Python" %}
```python
import asyncio
from airstack.execute_query import AirstackClient

api_client = AirstackClient(api_key="YOUR_AIRSTACK_API_KEY")

query = """YOUR_QUERY""" # Replace with GraphQL Query

async def main():
    execute_query_client = api_client.create_execute_query_object(
        query=query)

    query_response = await execute_query_client.execute_query()
    print(query_response.data)

asyncio.run(main())
```
{% endtab %}
{% endtabs %}

#### Other Programming Languages

To access the Airstack APIs in other languages, you can use [https://api.airstack.xyz/gql](https://api.airstack.xyz/gql) as your GraphQL endpoint.

## **🤖 AI Natural Language**[**​**](https://xmtp.org/docs/tutorials/query-xmtp#-ai-natural-language)

[Airstack](https://airstack.xyz/) provides an AI solution for you to build GraphQL queries to fulfill your use case easily. You can find the AI prompt of each query in the demo's caption or title for yourself to try.

<figure><img src="../../.gitbook/assets/NounsClip_060323FIN3.gif" alt=""><figcaption><p>Airstack AI (Demo)</p></figcaption></figure>

## Step 1: Verify Ownership of Token(s) or NFT(s) on an EOA

Given a 0x address, Lens profile, Farcaster, or ENS, simply check if the owner holds the specific NFT that is gated:

### Try Demo

{% embed url="https://app.airstack.xyz/query/06EErmlEIE" %}
Verify Ownership of Token(s) or NFT(s) on an EOA
{% endembed %}

### Code

{% tabs %}
{% tab title="Query" %}
```graphql
query MyQuery {
  TokenBalances(
    input: {
      blockchain: base
      filter: {
        tokenAddress: { _eq: "0x99d3fd2f1cF2E99C43F95083B98033d191F4eAbb" }
        tokenId: { _eq: "10" }
        owner: { _in: "0x6da658f5840fecc688a4bd007ef6b314d9138135" }
      }
    }
  ) {
    TokenBalance {
      amount
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "data": {
    "TokenBalances": {
      "TokenBalance": [
        {
          "amount": "1"
        }
      ]
    }
  }
}
```
{% endtab %}
{% endtabs %}

If the `amount` field exists, then it indicates that the user has ownership of the token or the NFT inside the EOA. Thus, gating access can be provided to the user.

On the other hand, if the `amount` field does not exist, the EOA does not hold the asset and should proceed to the next step to check the asset in the TBAs.

## Step 2: Get All Token Bound ERC6551 Accounts

Given a 0x address, Lens profile, Farcaster, or ENS, you can fetch all the [1st-level token-bound accounts](#user-content-fn-1)[^1]:

### Try Demo

{% embed url="https://app.airstack.xyz/query/YjTI8VMvY8" %}
Show all ERC6551 accounts owned by the NFTs owned by 0x6da658f5840fecc688a4bd007ef6b314d9138135
{% endembed %}

### Code

{% tabs %}
{% tab title="Query" %}
```graphql
query MyQuery {
  TokenBalances(
    input: {
      filter: { owner: { _in: ["0x6da658f5840fecc688a4bd007ef6b314d9138135"] } }
      blockchain: base
      limit: 200
    }
  ) {
    TokenBalance {
      tokenNfts {
        erc6551Accounts {
          address {
            addresses
          }
        }
      }
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "data": {
    "TokenBalances": {
      "TokenBalance": [
        {
          "tokenNfts": {
            "erc6551Accounts": [
              {
                "address": {
                  "addresses": ["0x9b220ed6f11f0223d1e01140fbb0e1b22c713a1a"]
                }
              }
            ]
          }
        },
        {
          "tokenNfts": {
            "erc6551Accounts": [] // NFT does not own any TBA
          }
        },
        {
          "tokenNfts": {
            "erc6551Accounts": [
              {
                "address": {
                  "addresses": ["0x9aacd59af1615b44cf066a9245caea7bdd44c98b"]
                }
              }
            ]
          }
        },
        {
          "tokenNfts": {
            "erc6551Accounts": [
              {
                "address": {
                  "addresses": ["0x06265b9caeae3fe4c6b557daaff80a20546d16cf"]
                }
              }
            ]
          }
        }
      ]
    }
  }
}
```
{% endtab %}
{% endtabs %}

From this example, you can see the there are 3 TBA on the 1st level owned by the given address `0x6da658f5840fecc688a4bd007ef6b314d9138135`.

By formatting the the output with the following format function:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const formatFunction = (data) =>
  data?.TokenBalances?.TokenBalance?.map(({ tokenNfts }) =>
    tokenNfts?.erc6551Accounts?.map(({ address }) =>
      address?.addresses?.map((addr) => addr)
    )
  )
    .filter(Boolean)
    .flat(2)
    .filter((address, index, array) => array.indexOf(address) === index) ?? [];
```
{% endtab %}

{% tab title="Python" %}
```python
def format_function(data):
    result = []

    if data and 'TokenBalances' in data and 'TokenBalance' in data['TokenBalances'] and data['TokenBalances']['TokenBalance']:
        for item in data['TokenBalances']['TokenBalance']:
            if 'tokenNfts' in item and 'erc6551Accounts' in item['tokenNfts']:
                for account in item['tokenNfts']['erc6551Accounts']:
                    if 'address' in account and 'addresses' in account['address']:
                        result.append(account['address']['addresses'])

    result = [item for sublist in result for item in sublist]
    result = list(set(result))

    return result
```
{% endtab %}
{% endtabs %}

The formatted data will be a flat array of all the ERC6551 account address:

```json
[
  "0x9b220ed6f11f0223d1e01140fbb0e1b22c713a1a",
  "0x9aacd59af1615b44cf066a9245caea7bdd44c98b",
  "0x06265b9caeae3fe4c6b557daaff80a20546d16cf"
]
```

## Step 3: Verify Ownership of Token(s) or NFT(s) on ERC6551 Accounts

Once you have all the ERC6551 accounts on the 1st level of the tree compiled into a single array, you can provide it as an array input into the following query to verify the ownership of token(s) or NFT(s):

### Try Demo

{% embed url="https://app.airstack.xyz/query/s4QaHbs3hZ" %}
Verify ownership of NFT on ERC6551 Accounts
{% endembed %}

### Code

{% tabs %}
{% tab title="Query" %}
```graphql
query MyQuery {
  TokenBalances(
    input: {
      blockchain: base
      filter: {
        tokenAddress: { _eq: "0x99d3fd2f1cF2E99C43F95083B98033d191F4eAbb" }
        tokenId: { _eq: "14" }
        owner: {
          _in: [
            "0xd8dc5794dd43aa9d7495f05bf110614ed32e950f"
            "0x333d0ddaf1ecf507f54641055f732914c7f00f18"
            "0x8bce0326262c140f82b754ee15dbd7b0a52654a6"
            "0x97bd7bb13368348f10c872b5cc7d06e6993aa6eb"
          ]
        }
      }
    }
  ) {
    TokenBalance {
      amount
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "data": {
    "TokenBalances": {
      "TokenBalance": [
        {
          "amount": "1"
        }
      ]
    }
  }
}
```
{% endtab %}
{% endtabs %}

Similarly to above, if the `amount` field does not exist, none of the TBAs in the array hold the asset and should proceed to the next step to check the asset in the TBAs.

## Step 4: Iterate On The Next ERC6551 Accounts Level

After going through and checking the 1st layer, you can go through the 2nd, 3rd, ..., nth layer of the ERC6551 tree to verify the ownership of token or NFT on each level.

The complete code will look like as follows:

{% tabs %}
{% tab title="React" %}
```jsx
import { useState, useEffect } from "react";
import { init, useLazyQuery } from "@airstack/airstack-react";

init("YOUR_AIRSTACK_API_KEY");

const VERIFY_OWNERSHIP = `
  query MyQuery(
    $owner: [Identity!]
    $tokenAddress: Address!
    $tokenId: String
  ) {
    TokenBalances(
      input: {
        blockchain: base
        filter: {
          tokenAddress: { _eq: $tokenAddress }
          tokenId: { _eq: $tokenId }
          owner: { _in: $owner }
        }
      }
    ) {
      TokenBalance {
        amount
      }
    }
  }
`;

const GET_ALL_ERC6551 = `
  query MyQuery($owner: [Identity!]) {
    TokenBalances(
      input: {
        filter: { owner: { _in: $owner } }
        blockchain: base
        limit: 200
      }
    ) {
      TokenBalance {
        tokenNfts {
          erc6551Accounts {
            address {
              addresses
            }
          }
        }
      }
    }
  }
`;

const formatFunction = (data) =>
  data?.TokenBalances?.TokenBalance?.map(({ tokenNfts }) =>
    tokenNfts?.erc6551Accounts?.map(({ address }) =>
      address?.addresses?.map((addr) => addr)
    )
  )
    .filter(Boolean)
    .flat(2)
    .filter((address, index, array) => array.indexOf(address) === index) ?? [];

const Component = () => {
  const [owner, setOwner] = useState(["0x6da658f5840fecc688a4bd007ef6b314d9138135"]);
  const [tokenAddress] = useState("0x99d3fd2f1cF2E99C43F95083B98033d191F4eAbb");
  const [tokenId] = useState("13");
  // provide access if `isAccessGrantedERC6551` is true, otherwise don't
  const [isAccessGrantedERC6551, setIsAccessGrantedERC6551] = useState(false);
  const [verifyOwnership] = useLazyQuery(
    VERIFY_OWNERSHIP,
    {},
    {
      onCompleted: async (data) => {
        console.log(data);
        if (data?.TokenBalances?.TokenBalance?.[0]?.amount) {
          setIsAccessGrantedERC6551(true);
        } else {
          getAllERC6551({ owner });
        }
      },
    }
  );
  const [getAllERC6551] = useLazyQuery(
    GET_ALL_ERC6551,
    {},
    {
      dataFormatter: formatFunction,
      onCompleted: (data) => {
        console.log(data);
        if (data?.length > 0) {
          verifyOwnership({
            owner: data,
            tokenAddress,
            tokenId,
          });
        }
      },
    }
  );

  useEffect(() => {
    verifyOwnership({
      owner,
      tokenAddress,
      tokenId,
    });
  }, []);

  return (
    //
  )
}
```
{% endtab %}

{% tab title="Node" %}
```javascript
import { init, fetchQuery } from "@airstack/node";

init("YOUR_AIRSTACK_API_KEY");

const VERIFY_OWNERSHIP = `
  query MyQuery(
    $owner: [Identity!]
    $tokenAddress: Address!
    $tokenId: String
  ) {
    TokenBalances(
      input: {
        blockchain: polygon
        filter: {
          tokenAddress: { _eq: $tokenAddress }
          tokenId: { _eq: $tokenId }
          owner: { _in: $owner }
        }
      }
    ) {
      TokenBalance {
        amount
      }
    }
  }
`;

const GET_ALL_ERC6551 = `
  query MyQuery($owner: [Identity!]) {
    TokenBalances(
      input: {
        filter: { owner: { _in: $owner } }
        blockchain: polygon
        limit: 200
      }
    ) {
      TokenBalance {
        tokenNfts {
          erc6551Accounts {
            address {
              addresses
            }
          }
        }
      }
    }
  }
`;

const formatFunction = (data) =>
  data?.TokenBalances?.TokenBalance?.map(({ tokenNfts }) =>
    tokenNfts?.erc6551Accounts?.map(({ address }) =>
      address?.addresses?.map((addr) => addr)
    )
  )
    .filter(Boolean)
    .flat(2)
    .filter((address, index, array) => array.indexOf(address) === index) ?? [];

// Console `Access Granted` if the asset is owned within one of the TBA
const isAccessGrantedERC6551 = async (walletAddress, tokenAddress, tokenId) => {
  let addresses = [walletAddress];
  while (addresses?.length > 0) {
    const { data } = await fetchQuery(
      VERIFY_OWNERSHIP,
      {
        owner: addresses,
        tokenAddress,
        tokenId,
      },
      { cache: false }
    );
    if (data?.TokenBalances?.TokenBalance?.[0]?.amount) {
      console.log("Access Granted");
      break;
    } else {
      const { data: erc6551Data } = await fetchQuery(
        GET_ALL_ERC6551,
        {
          owner: addresses,
        },
        { cache: false }
      );
      addresses = formatFunction(erc6551Data);
    }
  }
};

isAccessGrantedERC6551(
  "0x6da658f5840fecc688a4bd007ef6b314d9138135",
  "0x99d3fd2f1cF2E99C43F95083B98033d191F4eAbb",
  "14"
);
```
{% endtab %}

{% tab title="Python" %}
```python
import asyncio
from airstack.execute_query import AirstackClient

api_client = AirstackClient(api_key='YOUR_AIRSTACK_API_KEY')

verify_ownership = """
  query MyQuery(
    $owner: [Identity!]
    $tokenAddress: Address!
    $tokenId: String
  ) {
    TokenBalances(
      input: {
        blockchain: polygon
        filter: {
          tokenAddress: { _eq: $tokenAddress }
          tokenId: { _eq: $tokenId }
          owner: { _in: $owner }
        }
      }
    ) {
      TokenBalance {
        amount
      }
    }
  }
"""

get_all_erc6551 = """
  query MyQuery($owner: [Identity!]) {
    TokenBalances(
      input: {
        filter: { owner: { _in: $owner } }
        blockchain: polygon
        limit: 200
      }
    ) {
      TokenBalance {
        tokenNfts {
          erc6551Accounts {
            address {
              addresses
            }
          }
        }
      }
    }
  }
"""


def format_function(data):
    result = []

    if data and 'TokenBalances' in data and 'TokenBalance' in data['TokenBalances'] and data['TokenBalances']['TokenBalance']:
        for item in data['TokenBalances']['TokenBalance']:
            if 'tokenNfts' in item and 'erc6551Accounts' in item['tokenNfts']:
                for account in item['tokenNfts']['erc6551Accounts']:
                    if 'address' in account and 'addresses' in account['address']:
                        result.append(account['address']['addresses'])

    result = [item for sublist in result for item in sublist]
    result = list(set(result))

    return result

# print `Access Granted` if the asset is owned within one of the TBA
async def isAccessGrantedERC6551(walletAddress, tokenAddress, tokenId):
    addresses = [walletAddress]
    while len(addresses) > 0:
        execute_query_client = api_client.create_execute_query_object(
            query=verify_ownership, variables={"owner": addresses, "tokenAddress": tokenAddress, "tokenId": tokenId})

        query_response = await execute_query_client.execute_query()
        data = query_response.data

        if data and "TokenBalances" in data and "TokenBalance" in data["TokenBalances"] and data["TokenBalances"]["TokenBalance"] and len(data["TokenBalances"]["TokenBalance"]) > 0 and "amount" in data["TokenBalances"]["TokenBalance"][0]:
            print("Access Granted")
            break
        else:
            execute_query_client = api_client.create_execute_query_object(
                query=get_all_erc6551, variables={"owner": addresses})

            query_response = await execute_query_client.execute_query()

            addresses = format_function(query_response.data)


asyncio.run(
    isAccessGrantedERC6551(
        "0x6da658f5840fecc688a4bd007ef6b314d9138135",
        "0x99d3fd2f1cF2E99C43F95083B98033d191F4eAbb",
        "13"
    )
)
```
{% endtab %}
{% endtabs %}

## Developer Support

If you have any questions or need help regarding building token gating for ERC6551 accounts, please join our Airstack's [Telegram](https://t.me/+1k3c2FR7z51mNDRh) group.

## More Resources

* [TokenBalances API](../../api-references/api-reference/tokenbalances-api.md)
* [Accounts API](../../api-references/api-reference/accounts-api.md)
* [Other Tokenbound ERC6551 Tutorials](./)
  * [NFTs](nfts.md)
  * [NFT Owners](nft-owners.md)
  * [Sort Results](sort-results.md)

[^1]: Token bound accounts that is owned directly by the NFT that is owned directly by the given 0x address, Lens profile, Farcaster, or ENS.
