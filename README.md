# YAGPDB scripts

My own collection of YAGPDB scripts, which I use on my Discord Server. Most of the scripts are disabled or not used anymore, I decided to open source them so others can benefit from it.

### Requirements
* [Discord](https://discord.com/)
* [YAGPDB](https://yagpdb.xyz/), make sure you [read the documents](https://docs.yagpdb.xyz/commands/all-commands)
* (_optional_) You find a lot of examples and code here from [Pedro](https://github.com/Pedro-Pessoa/). THX at this point goes to Pedro I learned a lot from you! 👍

## Table of Contents
* [YAGPDB scripts](#yagpdb-scripts)
    * [Requirements](#requirements)
  * [Table of Contents](#table-of-contents)
    * [What you need to change based on your own profile/needs](#what-you-need-to-change-based-on-your-own-profileneeds)
    * [FAQ](#faq)
      * [How do I ban people without avatar who are trying to join my server?](#how-do-i-ban-people-without-avatar-who-are-trying-to-join-my-server)
      * [I use a lot of CCs but I get a strange message that my limit is exceeded, what can I do?](#i-use-a-lot-of-ccs-but-i-get-a-strange-message-that-my-limit-is-exceeded-what-can-i-do)
      * [Embedlimits](#embedlimits)


### What you need to change based on your own profile/needs
* Username
* Database names
* Role-/User-IDs
* Channel IDs
* Links to specific profiles/websites
* You can choose to copy the comments `{{/*This is a comment*/}}` or not, I see no problem to keep them in the CC (unless you hit the character limit).



### FAQ

#### How do I ban people without avatar who are trying to join my server?

You can use `If not .User.Avatar` in your **User Join** message.


#### I use a lot of CCs but I get a strange message that my limit is exceeded, what can I do?

Some commands need "Pro" Membership. This means you can only create 1 DB per one User on your server unless you unlocked pro. Most DB errors are because you try to execute too many DB commands.


#### Embedlimits

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
