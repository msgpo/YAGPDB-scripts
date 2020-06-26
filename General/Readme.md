## Join welcome message in server channel
```ts
{{ $avatar := (joinStr "" "https://cdn.discordapp.com/avatars/" (toString .User.ID) "/" .User.Avatar ".png?size=1024") }}
{{$embed := cembed
"title" (joinStr " " ":0blobjoining:" "Welcome to CKs Tech News Discord Server :two_hearts: ")
"description"  (joinStr " " "hello" (.User.Mention) "welcome to our server :blush: feel free to give yourself some roles in the" "<#569476987857403904>" "channel and have a nice stay! :)")
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


## Join Kick (kick a specific user directly after first message)

```ts
{{if eq .User.ID 683630053686378498}}
{{$urgaylol :=execAdmin "Testkick user "}}
{{end}}
{{if eq .User.ID 694623651403792494}}
{{$urgaylol :=execAdmin "kick <@!694623651403792494> "}}
{{end}}
{{if eq .User.ID 606157889912176640}}
{{$urgaylol :=execAdmin "kick <@!606157889912176640> "}}
{{end}}
{{if eq .User.ID 594493592983044106}}
{{addRoleID 693391415543332874}}
{{removeRoleID 692041392985342113}}
{end}}
