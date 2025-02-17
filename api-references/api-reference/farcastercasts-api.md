---
description: >-
  Learn all the detailed references of FarcasterCasts API that provide Farcaster
  casts information, including the input filters and output fields.
---

# FarcasterCasts API

The FarcasterCasts API enables you to fetch a list of all casts based on various filters, including casters, cast hash, cast's parent hash, cast's URL, casting time, and whether they contain frames, embeds, or any mentions in them.

## Inputs

### filters

| Name                | Type            | Description                                                                                                          |
| ------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------- |
| `castedBy`          | `Identity`      | Filter by caster. You can input any type of identity supported by [Airstack Identity API](airstack-identity-api.md). |
| `castedAtTimestamp` | `Time`          | Filter by the time when Frames is casted.                                                                            |
| `frameUrl`          | `String`        | Filter By Frame URL.                                                                                                 |
| `hasEmbeds`         | `Boolean`       | Filter by whether a cast has any embeds or not.                                                                      |
| `hasFrames`         | `Boolean`       | Filter by whether a cast has any Frames or not.                                                                      |
| `hasMentions`       | `Boolean`       | Filter by whether a cast has any mentions or not.                                                                    |
| `hash`              | `String`        | Filter by Farcaster cast hash.                                                                                       |
| `parentHash`        | `String`        | Filter by Farcaster cast's parent hash.                                                                              |
| `url`               | `Simple_String` | Filter by Warpcast's Cast URL.                                                                                       |

### blockchain

{% hint style="info" %}
For **FarcasterCasts** API, it will return all Farcaster casts.

You just need to specify the input to `ALL` for the query to work.
{% endhint %}

| Enum  | Description |
| ----- | ----------- |
| `ALL` | -           |

## Outputs

| Name                | Type                                             | Description                                                                     |
| ------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------- |
| `castedAtTimestamp` | `Time`                                           | Time when the cast                                                              |
| `castedBy`          | [`Social`](socials-api.md)                       | **Nested Query** – Caster details.                                              |
| `channel`           | [`FarcasterChannel`](farcasterchannels-api.md)   | **Nested Query** – Farcaster Channel where the cast is casted.                  |
| `embeds`            | `[Map]`                                          | Embeds contained in the casts.                                                  |
| `fid`               | `String`                                         | Caster's FID.                                                                   |
| `frame`             | [`FarcasterFrame`](../objects/farcasterframe.md) | **Nested Query** – Farcaster Frame attached to the cast.                        |
| `hash`              | `String`                                         | Cast Hash.                                                                      |
| `id`                | `ID`                                             | Airstack unique identifier for the data point.                                  |
| `mentions`          | `[Mentions]`                                     | **Nested Query** – Farcaster profiles that like that are mentioned in the cast. |
| `numberOfLikes`     | `Int`                                            | Number of likes on the cast.                                                    |
| `numberOfRecasts`   | `Int`                                            | Number of recasts on the cast.                                                  |
| `numberOfReplies`   | `Int`                                            | Number of replies on the cast.                                                  |
| `parentCast`        | [`FarcasterCast`](farcastercasts-api.md)         | **Nested Query** – Cast's parent details.                                       |
| `parentFid`         | `String`                                         | Cast's parent FID.                                                              |
| `parentHash`        | `String`                                         | Cast's parent hash.                                                             |
| `parentUrl`         | `String`                                         | Cast's parent URL.                                                              |
| `rawText`           | `String`                                         | Raw text contained in the cast.                                                 |
| `recastedBy`        | [`[Social]`](socials-api.md)                     | **Nested Queries** – List of all Farcaster profiles recasted the cast.          |
| `rootParentHash`    | `String`                                         | The root hash of the cast.                                                      |
| `rootParentUrl`     | `String`                                         | The root URL of the cast.                                                       |
| `text`              | `String`                                         | The text content of the cast.                                                   |
| `url`               | `String`                                         | Warpcast's Cast URL.                                                            |
