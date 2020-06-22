# YAGPDB scripts

![Project Picture Custom Commands](https://github.com/CHEF-KOCH/YAGPDB-scripts/blob/master/Screenshots/CustomCommands.png?raw=true)

My own collection of YAGPDB scripts, which I use (_or used_) on my Discord Server. Most of the scripts are currently disabled or I simply do not used them anymore so I decided to open source them which hopefully helps others.


### Requirements
* [Discord](https://discord.com/)
* [YAGPDB](https://yagpdb.xyz/), make sure you [read the documents](https://docs.yagpdb.xyz/commands/all-commands)
* (_optional_) You find a lot of examples and code here from [Pedro](https://github.com/Pedro-Pessoa/). THX at this point goes to Pedro I learned a lot from you!


## Table of Contents
* [YAGPDB scripts](#yagpdb-scripts)
    * [Requirements](#requirements)
  * [Table of Contents](#table-of-contents)
    * [What you need to change based on your own profile/needs](#what-you-need-to-change-based-on-your-own-profileneeds)
  * [FAQ](#faq)
      * [How do I ban people without avatar who are trying to join my server?](#how-do-i-ban-people-without-avatar-who-are-trying-to-join-my-server)
      * [I use a lot of CCs but I get a strange message that my limit is exceeded, what can I do?](#i-use-a-lot-of-ccs-but-i-get-a-strange-message-that-my-limit-is-exceeded-what-can-i-do)
      * [Are the scripts in GoLang?](#are-the-scripts-in-golang)
    * [How secure is it to use the Bot I heard rumours it's insecure?](#how-secure-is-it-to-use-the-bot-i-heard-rumours-its-insecure)
      * [(Discord) YAGPDB Embedlimits](#discord-yagpdb-embedlimits)
      * [Difference between Role IDs?](#difference-between-role-ids)
      * [What does YAGPDB Pro (Premium) offers?](#what-does-yagpdb-pro-premium-offers)
  * [Interesting Discord APIs](#interesting-discord-apis)


### What you need to change based on your own profile/needs
* Username
* Database names
* Role-/User-/Channel-IDs
* Links to specific profiles/websites
* You can choose to copy the comments `{{/*This is a comment*/}}` or not, I see no problem to keep them in the CC (unless you hit the character limit).


## FAQ

#### How do I ban people without avatar who are trying to join my server?

You can use `If not .User.Avatar` in your **User Join** message.


#### I use a lot of CCs but I get a strange message that my limit is exceeded, what can I do?

Some commands need "Pro" Membership. This means you can only create 1 DB per one User on your server unless you unlocked pro. Most DB errors are because you try to execute too many DB commands.


#### Are the scripts in GoLang?

It's basically running in a wrapper and not real coding. More like common scripting.


### How secure is it to use the Bot I heard rumours it's insecure?

The [rumours](https://pastebin.com/dj4kN448) are incorrect, Bots are designed to log and the source code as well as a Discord server is there, which means you can self-host + verify what exactly is going on. The owner does not store any databases in an external cloud or sells them to third-parties.


#### (Discord) YAGPDB Embedlimits

All limits are explained over [here](https://discord.com/developers/docs/resources/channel#embed-limits).

| Embed property character limits |  |
| --- | --- |
| title       | 256 |
| author name | 256 |
| description | 2048 |
| field title | 256 |
| field value | 1024 |
| footer      | 2048 |

You may have up to 25 fields in an embed. A regular bot message can have 1 embed, webhooks up to 10. The sum of all characters in an embed structure must not exceed **6000 characters**.


#### Difference between Role IDs?

| Role Name  | ID          |
|------------|-------------|
| Channel (e.g. `#general`) | <#Channel_ID> e.g. `<#722441737720561674>` |
| User ID `@CK-cool` | `<@683274307954147376>` |
| Role_ID `@Hackers` | `<@&529673007736553472>` |
| Custom Emoji | `<:yagpdb:575061416193556510>` |
| [Custom Emoji Animated](https://docs.yagpdb.xyz/reference/animated_emotes) | `<a:coolpepe:575061416193556510>`


| Roles      | `-listroles`  |
|------------|-------------|
| <img src="https://github.com/CHEF-KOCH/YAGPDB-scripts/blob/master/Screenshots/Role%20IDs%20explained.png?raw=true" width="250"> | <img src="https://github.com/CHEF-KOCH/YAGPDB-scripts/blob/master/Screenshots/listroles.png?raw=true" width="250"> |


#### What does YAGPDB Pro (Premium) offers?

General
- Infinite server stats retention
- Twitter feeds
- Priority support


Extended limits
- Message cache duration increased from 1 hour to 12 hours, which means you will be able to see the removed messages in the logs if the message was sent in the previous 12 hours
- Max custom commands increased from 100 to 250
- Max Reddit feeds increased from 100 to 1000
- Max YouTube feeds increased from 50 to 250 (Possibly higher if needed)
- Max Soundboard sounds increased from 50 to 250 (Possibly higher if needed)
- Increased database storage and interaction limits.
- Various custom command function limits increase

Another alternative compared to [Premium](https://yagpdb.xyz/premium) would be to [self-host the bot](https://github.com/jonas747/yagpdb).



## Interesting Discord APIs
* [Gateway API](https://discord.com/developers/docs/topics/gateway#commands-and-events-gateway-events)
