## Standalone snippets and examples

This means that you can use the below snippets/code samples how they are, they do not relay on something else. For more complex CustomCommands (CCs) check the GitHub folders.


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
