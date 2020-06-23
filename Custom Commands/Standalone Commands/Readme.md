## Standalone snippets and examples

This means that you can use the below snippets/code samples how they are, they do not relay on something else. For more complex CustomCommands (CCs) check the GitHub folders.

`Ungrouped` means you can put the commands wherever you like.


### Channel Help (_delete after 60 seconds_)

```ts
{{/* Command: autohelp - Case sensitive trigger: false - Group: Channel Help */}}
{{/* Ensure your DB contains the answers */}}
{{$msg := cembed "color" 9021952 "title" "Quick Channel Help Topics:" "author" (sdict "name" "Make sure you have DMs enabled or this wont work") "footer" (sdict "text" "React to this message to receive help on that topic.") "description" ":one: What are the Rules? \n:two: What is SAR? \n:three: Message / Channel / User IDs \n:four: How I use encryption? \n:five: How to use DiscordCrypt? \n:six: What are RRS-Feeds? \n:seven: I cannot get another role! \n:eight: I cannot see or write in help channels \n:nine: Why are accounts without avatar forbidden?"}}

{{$msg = sendMessageRetID nil $msg}}
{{if dbGet .User.ID "cld_autohelp"}}{{deleteMessage nil $msg 60}}{{end}}{{dbSetExpire .User.ID "cld_autohelp" "" 60}}
{{addMessageReactions nil $msg "1️⃣" "2️⃣" "3️⃣" "4️⃣" "5️⃣" "6️⃣" "7️⃣" "8️⃣" "9️⃣"}}
```


### Response automatically with picture/gif to exact message

```ts
{{/* Exact Match: dang - Case sensitive trigger: false - Group: Ungrouped */}}
{{ $embed := cembed "image" (sdict "url" "https://media.giphy.com/media/YJDmc88k7ttao/giphy.gif")  }}
{{ sendMessage nil $embed }}
```


### Basic Verification System

```ts
{{/* Basic welcome Reaction Verification */}}
{{ $msg := toInt (dbGet 0 "welcomeEmbed").Value }}
{{if not (hasRoleID 720787229773004850)}}
{{if eq .Reaction.Emoji.Name "✅"}}
{{$day := currentTime.Day}}{{$second := mod $day 10}}{{if in (cslice 11 12 13) $day}}{{$day = print $day "th"}}{{else}}{{if eq $second 1}}{{$day = print $day "st"}}{{else if eq $second 2}}{{$day = print $day "nd"}}{{else if eq $second 3}}{{$day = print $day "rd"}}{{else}}{{$day = print $day "th"}}{{end}}{{end}}
{{$date := print (currentTime.Format "Monday") " " $day " " (currentTime.Format "January 2006, 3:04PM")}}
{{addRoleID 720790092750258186}} {{/* The role you want to give once approved */}}
{{ $userID := .User.ID }}
{{ $avatar := (joinStr "" "https://cdn.discordapp.com/avatars/" (toString .User.ID) "/" .User.Avatar ".png") }}
{{ $embed := cembed
"title" (joinStr "" "User: " .User.Username " has verified them self.")
"thumbnail" (sdict "url" $avatar)
"description" (joinStr "" "Verified on: " $date)
"color" 60928}}
{{sendMessage 720788455617069076 $embed}} {{/* The message role you want to give once approved */}}
{{$emoji := .Reaction.Emoji.Name}}{{with .Reaction.Emoji.ID}}{{$emoji = (joinStr ":" $emoji .)}}{{end}}
{{deleteMessageReaction .Reaction.ChannelID .ReactionMessage.ID .Reaction.UserID $emoji}}
{{else}}
{{$emoji := .Reaction.Emoji.Name}}{{with .Reaction.Emoji.ID}}{{$emoji = (joinStr ":" $emoji .)}}{{end}}
{{deleteMessageReaction .Reaction.ChannelID .ReactionMessage.ID .Reaction.UserID $emoji}}
{{end}}
{{else}}
{{$emoji := .Reaction.Emoji.Name}}{{with .Reaction.Emoji.ID}}{{$emoji = (joinStr ":" $emoji .)}}{{end}}
{{deleteMessageReaction .Reaction.ChannelID .ReactionMessage.ID .Reaction.UserID $emoji}}
{{end}}
```


### Twitch Stream Announcement if you start streaming
```ts
{{ $avatar := (joinStr "" "https://cdn.discordapp.com/avatars/" (toString .User.ID) "/" .User.Avatar ".png") }}
{{ $preview := (joinStr "" "https://static-cdn.jtvnw.net/previews-ttv/live_user_" (slice .User.Avatar 22) "-1920x1080.jpg") }}

{{sendMessage nil (cembed
"title" .User.Username
"url" "https://www.twitch.tv/CKsTechNewstesttwitch"
"color" 6570404
"fields" (cslice
    (sdict "name" "Now Playing" "value" .User.Username)
    (sdict "name" "Stream Status" "value" .User.Avatar))
"thumbnail" (sdict "url" $avatar)
"image" (sdict "url" $preview)
"footer" (sdict "icon_url" $avatar "text" "Twitch")
"timestamp" currentTime
)}}
```


### Basic Suggestion system
```ts
{{/* regex: ^-(suggest|suggestion)\b - Case sensitive trigger: false - Group: Ungrouped*/}}
^-(suggest|suggestion)\b - Case sensitive trigger: false - Group: Ungrouped
{{$col := 16777215}}{{$pos := 0}}{{$r := .Member.Roles}}{{range .Guild.Roles}}{{if and (in $r .ID) (.Color) (gt $pos .Position)}}{{$pos = .Position}}{{$col = .Color}}{{end}}{{end}}

{{$args := parseArgs 1 ""
  (carg "string" "suggestion")}}

{{if (dbGet .User.ID "suggestCld")}}This command is on cooldown for {{humanizeDurationSeconds ((dbGet .User.ID "suggestCld").ExpiresAt.Sub currentTime)}} to avoid spam.{{else}}{{dbSetExpire .User.ID "suggestCld" "cooldown" 600}}

{{ $embed := cembed
"description" (joinStr " " .CmdArgs)
"color" $col
"author" (sdict "name" (joinStr "" .User.Username "#" .User.Discriminator) "url" "" "icon_url" (.User.AvatarURL "512"))
"timestamp"  currentTime
"footer" (sdict "text" (joinStr "" "Submit your suggestion with -suggest - " .User.ID))
}}
{{ $id := (sendMessageRetID 356486960417734666 $embed) }}
{{ addMessageReactions 356486960417734666 $id "upvote:524907425531428864" "downvote:524907425032175638" }}
{{ sendDM (joinStr "" "Suggestion submitted successfully. If you want to discuss this or other suggestions, use the <#525535451839463424> channel. If you want to delete your suggestion, do so with `-deletesuggestion " $id "` on the YAGPDB server.") }}
{{addReactions "upvote:524907425531428864" }} {{deleteTrigger 20}}{{end}}
```


### Reaction trigger to a specific message in a channel
```ts
{{if and (eq .Reaction.MessageID <MESSAGEID>) (eq .Reaction.Emoji.Name "✅")}}
{{addRoleID <ROLEID>}}
{{$x := sendMessageRetID <CHANNELID> (print .User.Mention " take a look at this channel!")}}
{{deleteMessage <CHANNELID> $x 1}}{{end}}
```


### Concert and find Hex colours + offset
```ts
{{/* Command: decimal - Case sensitive trigger: false - Group: Ungrouped */}}
{{ with (lower (reFind `^[0-9a-fA-F]{1,6}$` (joinStr " " .CmdArgs) ) ) }}
    {{ $h := . }} {{ $z := 0 }} {{ $e := 0 }}
    {{ $lenHex := len . | mult -1 | add 1 }}
    {{ range $k, $kk := seq $lenHex 1 }}
    {{/* Indexing array its ASCII codes get proper value */}}
        {{ $v := index $h $k }}
        {{ $a := "" }}
        {{ if and (ge $v 48) (le $v 57) }}
        {{ $a = printf "%c" $v }}
        {{ else if and (ge $v 97) (le $v 102) }} {{ $a = add $v -87 }} {{ end }}
        {{/* The equation for calc is formed here */}}
        {{ $kk = mult $kk -1 }}
        {{ $z = add $z (mult $a (pow 16 $kk) ) }}
    {{ end }}
    {{ joinStr "" "Hex: " $h " in Decimal: " $z }}
{{ else }}
    Error, argument needs to be RGB hex in length of 6 characters eg 89aa00.
{{ end }}
```


#### Promote someone based on the upvotes/likes and give him a specific role
```ts
{{$userID := ""}}
{{$counter := 0}}
{{$requiredVotes := (add (div (toInt ((dbGet 312306999704682497 "staffcount").Value)) 2) 1)}}

{{if and (eq .Reaction.Emoji.Name "👍") (reFind `^petition to promote` .ReactionMessage.Content)}}
    {{$userID = (index .ReactionMessage.Mentions 0).ID }}
    {{range .ReactionMessage.Reactions}}
        {{$counter = .Count}}
    {{end}}

{{if gt $counter $requiredVotes}}
    {{giveRoleID $userID "715636173757743154"}}
{{end}}

{{end}}
```


### Give new user a role, take role back from old user and update latest user
```ts
{{giveRoleID .User.ID 723151084310495312}}
{{$tmpUser := (userArg (toInt $lastUser.Value))}}
{{/* check if its a valid user or not */}}
{{if $tmpUser}}
{{takeRoleID ($tmpUser.ID) 606891664396648474}}
{{end}}

{{/* OPTIONAL: If you don't want a channel topic goal tracker delete everything but dbset */}}
{{/*Goal Tracker in Topic */}}
{{$current := toInt (reFind `\d+` .Channel.Topic)}}
{{$list := cslice 15 20 25 30 50 75 100}}{{$found := false}}
{{$round := toInt (slice .StrippedMsg 0 2)}}
{{range $list}}{{if and (lt $round .) (not $found)}}{{$round = .}}{{$found = true}}{{end}}{{end}}
{{$res := toInt (mult $round (pow 10 (sub (len .StrippedMsg) 2)))}}
{{if not (eq $res $current)}}{{editChannelTopic nil (print "Next Goal: " $res " ;\nSay the number that comes after.\nA person must let someone else go before being able to go again.\n-nextcount for next number\ndo NOT edit your msg.. or ELSE ^^")}}{{end}}

{{dbSet 118 "counter_user" (toString .User.ID)}}
{{else}}

{{/* Message did not match expected next value */}}
{{deleteTrigger 1}}
{{/* Removed, because too annoying */}}
{{/*sendDM "That is not the next number, learn how to count"*/}}
{{end}}
{{end}}
```


### Automatically remove a users old reaction
```ts
{{/*This custom command allows you to automatically remove a users old reaction if they have already reacted to a message, this is a premium-only feature, unless you make a cc for it, like I did IMPORTANT NOTE!!! IF YOU WANT THIS TO WORK YOU **MUST** MAKE SURE THE OPTION -RR IS SET TO TRUE ON YOUR ROLEMENU OTHERWISE THIS WON'T WORK! ADDITIONALLY, SET IT TO "remove existing role when assigning a new role in group" TO GREEN! Trigger: Reaction Select the option "Added + Removed Reactions" */}}
``{{if eq .Reaction.MessageID 712660609035927602}}``
{{/* CONFIGURATION VARIABLES, THESE ARE VARIABLES THAT YOU **MUST** DEFINE */}}
{{/*This is the MESSAGE ID of the message with the rolemenu*/}}
{{$ReactionMsg := 712660609035927602}}
{{/*This is the CHANNEL ID where the message with the rolemenu is in*/}}
{{$Channel := 704812591364571147}}
{{/*CONFIGURATION VARIABLES END*/}}
{{/*CODE STARTS (DON'T TOUCH IF YOU DON'T KNOW WHAT YOU ARE DOING)*/}}
{{if and (eq .ReactionMessage.ID $ReactionMsg) (not .User.Bot) (eq .Channel.ID $Channel)}} {{$Emoji := .Reaction.Emoji.Name}}{{with .Reaction.Emoji.ID}}{{$Emoji = (joinStr ":" $Emoji .)}}{{end}} {{$dbReactionMsg := dbGet $ReactionMsg "ReactionMsg"}} {{if .ReactionAdded}} {{if $dbReactionMsg}} {{$dbReactionMsg := sdict $dbReactionMsg.Value}}
```


### Bump notification every 120 minutes
```ts
{{/* Trigger type: Hourly interval - Interval 2 Hours - Channel: #bump */}}
{{ $bumpCooldownMinutes := 120 }}
{{ $now := currentTime }}
{{ $guildOwnerUser := userArg .Guild.OwnerID }}
{{ $reminderMessage := joinStr "" $guildOwnerUser.Mention " it is now time to bump **" .Guild.Name "**!" }}

{{ $globalCategoryID := toInt (dbGet 0 "Global").Value }}
{{ $lastBumpTime := (dbGet $globalCategoryID "Last Bump").Value }}

{{ if $lastBumpTime }}
    {{ $minutesSinceBump := toInt ($now.Sub $lastBumpTime).Minutes }}
    {{ if gt $minutesSinceBump $bumpCooldownMinutes }}
        {{ sendMessage nil $reminderMessage }}
    {{ end }}
{{ else }}
    {{ sendMessage nil $reminderMessage }}
{{ end }}
```


### World Clock
```ts
{{/*
    Usage: `-worldclock`.

    Recommended trigger: Command trigger with trigger `worldclock`.
*/}}

{{ $clocks := sdict
    "Vancouver" "America/Vancouver"
    "New York" "America/New_York"
    "London" "Europe/London"
    "Moscow" "Europe/Moscow"
    "Tokyo" "Asia/Tokyo"
}}
{{ $hour := .TimeHour }}

{{ $embed := sdict
    "title" "🕰️ World Clock"
    "fields" cslice
    "color" 0x0070BB
    "footer" (sdict "text" "Your time")
    "timestamp" currentTime
}}

{{ range $name, $ := $clocks }}
    {{ $time := currentTime.In (newDate 0 0 0 0 0 0 .).Location }}
    {{ $formatted := printf "%s, %s"
        $time.Weekday.String
        ($time.Format "3:04:05 PM")
    }}
    {{ $embed.fields.Append (sdict
        "name" $name
        "value" $formatted
    ) | $embed.Set "fields" }}
{{ end }}

{{ sendMessage nil (cembed $embed) }}
```


### Giphy Search
```ts
{{/*
    Usage: `giphy`.

    Recommended trigger: Command (Mention/cmd prefix).
*/}}
{{$base_url := "https://giphy.com/search/"}}
{{$query := urlescape .StrippedMsg}}
{{$id := sendMessageRetID nil (print $base_url $query)}}
{{sleep 3}}
{{$Message := getMessage nil $id}}{{$del := false}}
{{with $Message.Embeds}}
    {{with (index . 0).Thumbnail}}
        {{if .Width}}{{editMessage nil  $id (complexMessageEdit "content" "" "embed" (sdict "description" "**Giphy Search Result**" "image" (sdict "url" .ProxyURL)))}}
        {{else}}{{$del := true}}
        {{end}}
    {{else}}{{$del := true}}
    {{end}}
{{else}}{{$del := true}}
{{end}}
{{if $del}}{{deleteMessage nil $id}}{{end}}
```


### Big Emotes
```ts
{{/*
	This command allows you to enlarge emojis - version 2. Usage: `-bigemoji <emoji>`.

	Recommended trigger: Regex trigger with trigger `^-(be|big-?emo(te|ji))`
*/}}

{{ if .CmdArgs }}
	{{ $embed := sdict }} {{ $emoji := index .CmdArgs 0 }}
		{{ if reFind `\p{So}|.\x{20e3}` $emoji }}
			{{ $emoji_U := "" }}
			{{ $url := "https://raw.githubusercontent.com/iamcal/emoji-data/master/img-google-136/" }}
			{{- range toRune $emoji }}
				{{- $emoji_U = joinStr "-" $emoji_U (printf "%04x" .) }}
			{{- end -}}
			{{ $url = joinStr "" $url $emoji_U ".png" }}
			{{ $embed.Set "image"  (sdict "url" $url) }}
		{{ else }}
			{{ with reFindAllSubmatches `<(a)?:[\w~]+:(\d+)>` $emoji }}
				{{ $animated := index . 0 1 }}
				{{ $id := index . 0 2 }}
				{{ $ext := ".png" }}
				{{ if $animated }} {{ $ext = ".gif" }} {{ end }}
				{{ $embed.Set "image"  (sdict "url" (printf "https://cdn.discordapp.com/emojis/%s%s" $id $ext)) }}
			{{ end }}
		{{ end }}

		{{$ID := 0}}
		{{if $embed}}
			{{ $embed.Set "color" 0x39ff14 }}
			{{ $embed.Set "author" (sdict "name" .User.Username "icon_url" (.User.AvatarURL "256")) }}
			{{ $ID = sendMessageRetID nil (cembed $embed) }}
		{{ end }}

		{{ sleep 1 }} {{/* This just puts a standard output when no matching image is found. You can also use deleteMessage here */}}
		{{ with getMessage nil $ID }}
			{{ if not (index .Embeds 0).Image.Width }}
				{{ $embed.Set "description" $emoji}} {{ $embed.Del "image" }}
				{{ editMessage nil $ID (cembed $embed) }}
			{{ end }}
		{{ end }}
{{ end }}
{{ deleteTrigger 0 }}
```


### Basic Partnership Reputation as Fast as Possible (for those who bump)
```ts
{{/*
	Trigger Type: Regex

	Trigger: (https?:\/\/)?(www\.)?((discord\.(gg|io|me|li)|discord(app)?\.com\/invite|invite\.gg))(\/|\\)[A-z+0-9]+
*/}}
Response: {{execAdmin "grep" .User}}
{{/*
	Set the command to Only Run in your Partnerships channel and save it.

	On the Control Panel, go to Fun > Reputation. Enable the module and select Admins and YAGPDB on the option "Allowed roles to give/remove points"
*/}}
```


### Rename your channel automatically (Current time example)

**Info**

Discord only allows to change the channel name once every 10 minutes.

```ts
{{/*
	Minute Interval

	Interval: 10

	Channel: Choose your channel which you like to rename.
*/}}
{{$clocks := sdict "1" "ServerTime" "2" "ServerTime" "3" "ServerTime" "4" "ServerTime" "5" "ServerTime" "6" "ServerTime" "7" "ServerTime" "8" "ServerTime" "9" "ServerTime" "10" "ServerTime" "11" "ServerTime" "12" "ServerTime"}}
{{$time := (currentTime.In (newDate 0 0 0 0 0 0 "Europe/Berlin").Location)}}
{{$name := (joinStr "" " " ($clocks.Get (str ($time.Format "3"))) " " ($time.Format "3:04 PM") "" ($time.Format "") "")}}
{{editChannelName 719728567054893146 $name}}
```


### Simple membercount & online member counter

```ts
{{/* Replace xxxxx with channel IDs, use voice channels for best results. This basically replaces typical "Stats Bods". */}}
{{editChannelName "xxxxx" (joinStr "" "Members: " .Guild.MemberCount)}}
{{editChannelName "xxxxx" (joinStr "" "Online: " onlineCount)}}
{{/* Basic clock (UTC) in voice channel form  */}}
{{editChannelName "xxxxx" (joinStr "" (currentTime.Format "3:04PM") " - " (currentTime.Format "15:04") " UTC")}}
{{/* Basic clock (none UTC Variant), make sure you change the offset (-4) and  EDT. */}}
{{editChannelName "xxxxx" (joinStr "" ((currentTime.Add (toDuration (mult -4 .TimeHour))).Format "3:04PM") " - " ((currentTime.Add (toDuration (mult -4 .TimeHour))).Format "15:04") " EDT")}}
```


### Message link preview

 ```ts
{{/* This will show a preview of message links posted, use https://discordapp.com/channels\/(\d+)\/(\d+)\/(\d+) as the regex trigger */}}
{{ $matches := reFindAllSubmatches `https://discordapp.com/channels\/(\d+)\/(\d+)\/(\d+)` .Message.Content }}
{{$msg := getMessage (index (index $matches 0) 2) (index (index $matches 0) 3) }}
{{if not $msg}}

{{else}}
{{ $avatar := (joinStr "" "https://cdn.discordapp.com/avatars/" (toString $msg.Author.ID) "/" $msg.Author ".png") }}

{{$embedRaw := sdict
"description" (joinStr "" "**[Message Link](" (index (index $matches 0) 0) ")  to <#" $msg.ChannelID ">**\n" $msg.Content)
"color" 4645612
"author" (sdict "name"  $msg.Author.Username "icon_url" ($msg.Author.AvatarURL "64"))
"footer" (sdict "text" (joinStr "" "Req. by "  .Message.Author.Username ". Quote from "))
"timestamp" $msg.Timestamp  }}

{{if $msg.Attachments}}
{{$embedRaw.Set "image" (sdict "url" (index $msg.Attachments 0).URL) }}
{{end}}

{{ sendMessage nil (cembed $embedRaw) }}

{{/* Delete the trigger if it only contained a link and nothing more */}}
{{if eq (len (index (index $matches 0) 0)) (len .Message.Content) }} {{deleteTrigger 1}} {{end}}
{{end}}
```


### Quiz

```ts
{{/* Case sensitive trigger: false - Group: Ungrouped */}}
{{ $qEmbed := cembed

"title" "Quiz"
{{/* Link to quit question Google Sheet/Docs */}}
"description" "If you want to take the quiz, click [here](https://docs.google.com/forms/d/e/)!"
"color" 32768
"footer" (sdict "text" (joinStr " " "Requested by:" (.User.Username))) }}

{{ sendMessage nil $qEmbed }}
```


### Ban

```ts
{{/* StartsWith: \-ban - Case sensitive trigger: false - Group: Ungrouped */}}
{{if .Message.Mentions}}
:hammer: Banned `{{index .Message.Mentions 0}}` forever biatch!
{{end}}
```
