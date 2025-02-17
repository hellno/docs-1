---
description: >-
  Learn how fetch the ERC20/721/1155/POAP balances of a solana address across
  Ethereum, Gold, Base, and Zora.
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

# ⚖️ Token Balances

## Table Of Contents

In this guide, you will learn how to use [Airstack](https://airstack.xyz) to:

- [Get All ERC20s Owned By Solana Address](token-balances.md#get-all-erc20s-owned-by-solana-address)
- [Get All NFTs Owned By Solana Address](token-balances.md#get-all-nfts-owned-by-solana-address)
- [Get All POAPs Owned By Solana Address](token-balances.md#get-all-poaps-attended-by-solana-address)

## Pre-requisites

- An [Airstack](https://airstack.xyz/) account
- Basic knowledge of GraphQL

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

## Get All ERC20s Owned By Solana Address

You can fetch all ERC20 tokens on Ethereum, Base, and Zora owned by any Solana address:

### Try Demo

{% embed url="https://app.airstack.xyz/query/29dvo4VnBa" %}
Show me the ERC20 balances of Solana address GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV across Ethereum, Base, and Zora
{% endembed %}

### Code

{% tabs %}
{% tab title="Query" %}

```graphql
query MyQuery {
  Ethereum: TokenBalances(
    input: {
      filter: {
        owner: { _eq: "GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV" }
        tokenType: { _eq: ERC20 }
      }
      blockchain: ethereum
      limit: 50
    }
  ) {
    TokenBalance {
      formattedAmount
      tokenAddress
      token {
        name
        symbol
      }
    }
  }
  Base: TokenBalances(
    input: {
      filter: {
        owner: { _eq: "GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV" }
        tokenType: { _eq: ERC20 }
      }
      blockchain: base
      limit: 50
    }
  ) {
    TokenBalance {
      formattedAmount
      tokenAddress
      token {
        name
        symbol
      }
    }
  }
  Zora: TokenBalances(
    input: {
      filter: {
        owner: { _eq: "GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV" }
        tokenType: { _eq: ERC20 }
      }
      blockchain: zora
      limit: 50
    }
  ) {
    TokenBalance {
      formattedAmount
      tokenAddress
      token {
        name
        symbol
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
    "Ethereum": {
      "TokenBalance": [
        {
          "formattedAmount": 0.4232628308354559,
          "tokenAddress": "0x7d1afa7b718fb893db30a3abc0cfc608aacfebb0",
          "token": {
            "name": "Matic Token",
            "symbol": "MATIC"
          }
        }
        // Other Ethereum ERC20 tokens
      ]
    },
    "Base": {
      "TokenBalance": [
        {
          "formattedAmount": 0.04527032518190595,
          "tokenAddress": "0x4200000000000000000000000000000000000006",
          "token": {
            "name": "Wrapped Ether",
            "symbol": "WETH"
          }
        }
        // Other Base ERC20 tokens
      ]
    },
    "Zora": {
      "TokenBalance": [
        {
          "formattedAmount": 0.0030225,
          "tokenAddress": "0x4200000000000000000000000000000000000006",
          "token": {
            "name": "Wrapped Ether",
            "symbol": "WETH"
          }
        }
      ]
    }
  }
}
```

{% endtab %}
{% endtabs %}

## Get All NFTs Owned By Solana Address

You can fetch all NFT collections on Ethereum, Gold, Base, and Zora owned by any Solana address by using the [`TokenBalances`](../../api-references/api-reference/tokenbalances-api.md) API:

### Try Demo

{% embed url="https://app.airstack.xyz/query/RKMTOBysDZ" %}
Show me all the NFTs owned by GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV across Ethereum, Gold, Base, and Zora
{% endembed %}

### Code

{% tabs %}
{% tab title="Query" %}

```graphql
query MyQuery {
  Ethereum: TokenBalances(
    input: {
      filter: {
        owner: { _eq: "GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV" }
        tokenType: { _in: [ERC721, ERC1155] }
      }
      blockchain: ethereum
      limit: 50
    }
  ) {
    TokenBalance {
      formattedAmount
      tokenAddress
      token {
        name
        symbol
      }
      tokenNfts {
        contentValue {
          image {
            medium
          }
        }
      }
    }
  }
  Polygon: TokenBalances(
    input: {
      filter: {
        owner: { _eq: "GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV" }
        tokenType: { _in: [ERC721, ERC1155] }
      }
      blockchain: polygon
      limit: 50
    }
  ) {
    TokenBalance {
      formattedAmount
      tokenAddress
      token {
        name
        symbol
      }
      tokenNfts {
        contentValue {
          image {
            medium
          }
        }
      }
    }
  }
  Base: TokenBalances(
    input: {
      filter: {
        owner: { _eq: "GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV" }
        tokenType: { _in: [ERC721, ERC1155] }
      }
      blockchain: base
      limit: 50
    }
  ) {
    TokenBalance {
      formattedAmount
      tokenAddress
      token {
        name
        symbol
      }
      tokenNfts {
        contentValue {
          image {
            medium
          }
        }
      }
    }
  }
  Zora: TokenBalances(
    input: {
      filter: {
        owner: { _eq: "GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV" }
        tokenType: { _in: [ERC721, ERC1155] }
      }
      blockchain: zora
      limit: 50
    }
  ) {
    TokenBalance {
      formattedAmount
      tokenAddress
      token {
        name
        symbol
      }
      tokenNfts {
        contentValue {
          image {
            medium
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
    "Ethereum": {
      "TokenBalance": [
        {
          "formattedAmount": 1,
          "tokenAddress": "0xa723a8a69d9b8cf0bc93b92f9cb41532c1a27f8f",
          "token": {
            "name": "farcasting no. 1",
            "symbol": "FARCASTING"
          },
          "tokenNfts": {
            "contentValue": {
              "image": {
                "medium": "https://assets.airstack.xyz/image/nft/WAqW4V7VQ9ZvDl+JZt9C8PsLG21w5O9LGYQ2aWiLoXTV+fg5rhY0nW5nvymkVgGvjYYZevGHKzZVnizh6wkByg==/medium.png"
              }
            }
          }
        }
        // Other Ethereum NFTs
      ]
    },
    "Polygon": {
      "TokenBalance": [
        {
          "formattedAmount": 1,
          "tokenAddress": "0xd57867f2fdb89eadc8e859a89e3d5039c913d1d9",
          "token": {
            "name": "Lens Collect | Profile #1 - Publication #714",
            "symbol": "LENS-COLLECT"
          },
          "tokenNfts": {
            "contentValue": {
              "image": null
            }
          }
        }
        // Other Polygon NFTs
      ]
    },
    "Base": {
      "TokenBalance": [
        {
          "formattedAmount": 1,
          "tokenAddress": "0x0e76013ff360eea66b19e5dce6e0c697036bc2c0",
          "token": {
            "name": "SocialGraphs",
            "symbol": "SG"
          },
          "tokenNfts": {
            "contentValue": {
              "image": {
                "medium": "https://assets.airstack.xyz/image/nft/8453/Bx023e1+9XnGFYLY5nEdSzmVHMVoGlt4mMbmM3F558B2aoRCT4cMX5Bmy6Y92lszy9MOpLE6QJJmsH6pXO3Fkg==/medium.aaf"
              }
            }
          }
        }
        // Other Base NFTs
      ]
    },
    "Zora": {
      "TokenBalance": [
        {
          "formattedAmount": 1,
          "tokenAddress": "0xb9e43d3fc1c1d030d2fdab5e3b3326bb6b7526d5",
          "token": {
            "name": "in-motion images",
            "symbol": ""
          },
          "tokenNfts": {
            "contentValue": {
              "image": {
                "medium": "https://assets.airstack.xyz/image/nft/7777777/jhHqmdPsuhVIQnq/ea2L6VhKmtzJnwC2hEqIo3MIiYevip3jE36pj6LInDr3psjkBacwsGHPKauxhMxtI3svkw==/medium.png"
              }
            }
          }
        }
        // Other Zora NFTs
      ]
    }
  }
}
```

{% endtab %}
{% endtabs %}

## Get All POAPs Attended By Solana Address

You can fetch all POAP events attended by any Solana address by using the [`Poaps`](../../api-references/api-reference/poaps-api.md) API:

### Try Demo

{% embed url="https://app.airstack.xyz/query/IP86HMTSlL" %}
Show me all the POAPs attended by GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV
{% endembed %}

### Code

{% tabs %}
{% tab title="Query" %}

```graphql
query MyQuery {
  Poaps(
    input: {
      filter: { owner: { _eq: "GJQUFnCu7ZJHxtxeaeskjnqyx8QFAN1PsiGuShDMPsqV" } }
      blockchain: ALL
    }
  ) {
    Poap {
      eventId
      tokenId
      poapEvent {
        eventName
        description
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
    "Poaps": {
      "Poap": [
        {
          "eventId": "167485",
          "tokenId": "7002301",
          "poapEvent": {
            "eventName": "You've Met Jye - February 2024",
            "description": "A digital collectible as proof of meeting with Jye in February 24'. Whether catch up's in London and Paris to NFT Paris and side-events around it to the classic virtual call, it was nice to meet and look forward to staying in touch.\n\nhttps://nf.td/sandiforward"
          }
        },
        {
          "eventId": "150716",
          "tokenId": "6800204",
          "poapEvent": {
            "eventName": "You've met marpar.eth - September 2023",
            "description": "You met Mariano (marpar.eth) IRL during September 2023.\r\n \r\n Website: https://chainjet.io \r\n Telegram: marian2js \r\n Twitter: marian2js \r\n POAP created using welook.io"
          }
        },
        {
          "eventId": "150398",
          "tokenId": "6798634",
          "poapEvent": {
            "eventName": "Firefly at ETHGlobal New York 2023",
            "description": "This POAP commemorates meeting with Firefly at ETHGlobal New York 2023."
          }
        }
        // Other POAPs
      ]
    }
  }
}
```

{% endtab %}
{% endtabs %}

## Developer Support

If you have any questions or need help regarding fetching token balances of a solana address, please join our Airstack's [Telegram](https://t.me/+1k3c2FR7z51mNDRh) group.

## More Resources

- [Token Balances Guides](../token-balances/)
- [Resolving Solana Addresses](resolve-identities.md)
- [Social Followers Of Solana Addresses](social-followers.md)
- [Social Followings Of Solana Addresses](social-followings.md)
- [Check XMTP For Solana Addresses](../xmtp/check-multiple-users.md)
- [TokenBalances API Reference](../../api-references/api-reference/tokenbalances-api.md)
- [Poaps API Reference](../../api-references/api-reference/poaps-api.md)
