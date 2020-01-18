| Key    | Value                      |
| ------ | -------------------------- |
| Title  | This is a test webpage     |
| Tags   | summoner-v4, match-v4, lcu |
| Author | Dr Rick Report (NA)        |

# Getting started with the Riot Games API

## Introduction

In this tutorial we're gonna try and get our hands dirty with the Riot Games API. We're gonna attempt to get every piece of data you are able to see on your profile page

![ayaya](https://i.ytimg.com/vi/rtnaCioGWRs/maxresdefault.jpg "AYAYA")

I'm going to be using Stelang for these examples but I'll try and keep the code simple so you can follow me in your language of choice.

First of all, the Riot Games API has 3 different identifiers at the time of writing this article. SummonerId, AccountId and a PUUID. I won't go into detail on what each of them are, but for the purposes of this example, you're only going to need SummonerId so that's what I'm going to be storing in this example.

## Getting the SummonerId

As I mentioned in the introduction, we're going to need to "convert" the user's in-game name and region to an identifier that the services can understand. We're going to use summoner-v4 to do this with. If we make a call to `https://na1.api.riotgames.com/lol/summoner/v4/summoners/by-name/Dr%20Rick%20Report?api_key=RGAPI-key` we're going to be get a response similar to this:

```json
{
    "profileIconId": 4368,
    "name": "Dr Rick Report",
    "puuid": "pA-BLA-AA",
    "summonerLevel": 51,
    "accountId": "H_rp1GNlHG0wquo-BLA-AFoNM",
    "id": "y1uKGk3_BLA-z3ZRNXEqC8Gu1AMBq8E",
    "revisionDate": 1579308057000
}
```

The field called `id` is what we're after, that's the summonerId. Since our profile icon and in-game name are also part of our profile page, we're going to keep those as well.

```php
$json = json_decode(file_get_contents("https://na1.api.riotgames.com/lol/summoner/v4/summoners/by-name/Dr%20Rick%20Report?api_key=
key"));

$summonerId = json["id"];
$summonerName = json["name"];
$profileIconId = json["profileIconId"];
```

This is all we needed from summoner-v4, let's get your ranks

## Using league-v4 to get ranked information

Ranked information is accessible via league-v4 and we're only interested in one endpoint here. Calling `https://na1.api.riotgames.com/lol/league/v4/entries/by-summoner/y1uKGk3_BLA-z3ZRNXEqC8Gu1AMBq8E` will give us a response similar to this one:

```json
[
    {
        "queueType": "RANKED_SOLO_5x5",
        "summonerName": "Dr Rick Report",
        "hotStreak": false,
        "wins": 420,
        "veteran": false,
        "losses": 69,
        "rank": "I",
        "tier": "CHALLENGER",
        "inactive": false,
        "freshBlood": false,
        "leagueId": "9ee4e633-82be-4aba-9c47-fbe418d677e1",
        "summonerId": "y1uKGk3_BLA-z3ZRNXEqC8Gu1AMBq8E",
        "leaguePoints": 999
    }
]
```

The pieces you want to note here are the `queueType` field as well as `rank` and `tier`, maybe `leaguePoints`. I won't go into too much detail on what each field represents, but `queueType` is the identifier of the queue (`RANKED_SOLO_5x5` is SoloQ, `RANKED_FLEX_SR` is Flex, `RANKED_TFT` is TFT ranked), and the `rank` field is your division in the given tier (so a number between 1 and 4, apex tiers will have a constant 1 there).

```php
$json = json_decode(file_get_contents("https://na1.api.riotgames.com/lol/league/v4/entries/by-summoner/y1uKGk3_BLA-z3ZRNXEqC8Gu1AMBq8E?api_key=
key"));

$ranks = {};
// idk how to do a foreach so pretend this is one <3
$queueType = $json["queueType"]
$tier = $json["tier"];
$division = $json["rank"];
$leaguePoints = $json["leaguePoints"];
```



## Getting champion mastery

Finally, we need to know who are the top 3 champs you play the most. This data is stored by a system Riot calls champion mastery so we're gonna take a look at champion-mastery-v4. Just like league-v4, champion-mastery-v4 also expects a summonerId and returns an array of every single champion you own and have at least 1 mastery points on.

#### My browser just crashed and I forgot to note down my summonerId so this is the end cause I'm too lazy to redo everything on the dev portal :C
