## Join message in server channel
```ts
{{ $avatar := (joinStr "" "https://cdn.discordapp.com/avatars/" (toString .User.ID) "/" .User.Avatar ".png?size=1024") }}
{{$embed := cembed
"title" (joinStr " " ":0blobjoining:" "Welcome to CKs Tech News Discord Server :two_hearts: ")
"description"  (joinStr " " "hello" (.User.Mention) "to our server :blush: feel free to give yourself some roles in the" "<#569476987857403904>" "channel and have a nice stay! :)")
"color" 65280
"thumbnail" (sdict "url" $avatar)
}}
{{ sendMessage nil $embed }}
```

## User Leave Message
```ts
{{$var := randInt 1 100}}

{{if lt $var 97}}
{{$embed := cembed
"description"  (joinStr " " ":0blobleaving:"  (.User.Mention) "Left... :cry: ")
"color" 16711680 }}
{{ sendMessage nil $embed }}

{{else}}
{{$embed := cembed
"description"  (joinStr " " ":0blobleaving:" ":crab: :crab: " (.User.Mention) " is gone! :crab: :crab:  ")
"color" 16711680 }}
{{ sendMessage nil $embed }}

{{end}}
```
