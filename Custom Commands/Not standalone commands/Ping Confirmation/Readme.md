```ts
{{/* Trigger Type: Regex */}}
{{/* Trigger: <@&Role-1-ID> */}}
{{/* Make sure you replace xxxxxxxxxxxxx with the role. */}}
{{$Role2 := xxxxxxxxxxxxx}}
{{addMessageReactions nil (sendMessageRetID nil (cembed "description" (print .User.Mention " are you sure you want to ping <@&" $Role2 "> ?") "footer" (sdict "text" "Ping Confirmation"))) "✅" "❎"}}
```


```ts
{{/* Trigger Type: Reaction */}}
{{/* Trigger: Reaction Added + Removed or Reaction Added */}}
{{$Role2 := xxxxxxxxxxxx}}
{{if and (eq .ReactionMessage.Author.ID 204255221017214977) .ReactionMessage.Embeds .ReactionAdded (not .User.Bot) (or (eq .Reaction.Emoji.Name "✅") (eq .Reaction.Emoji.Name "❎"))}}
    {{with (index .ReactionMessage.Embeds 0).Footer.Text}}
        {{if eq . "Ping Confirmation"}}
            {{if eq $.Reaction.Emoji.Name "✅"}}
                {{sendMessageNoEscape nil (print "<@&" $Role2 ">")}}
            {{end}}
            {{deleteTrigger 0}}
        {{end}}
    {{end}}
{{end}}
```
