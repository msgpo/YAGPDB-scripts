### Verify Page content - Basic Emoji Verification system

```golang
{{$emojisArray := sdict
    "✅" (print $msg "was approved. " .ReactionMessage.Author.Mention)
    "❌" (print $msg "was denied. " .ReactionMessage.Author.Mention)
    "🖼" (print $msg "requires to have an image. " .ReactionMessage.Author.Mention)
    "⛔" (print $msg "doesn't fit the server's concepts or guidelines. " .ReactionMessage.Author.Mention)
    "🚮" (print $msg "seems quite unbalanced for the server's role-playing purposes. " .ReactionMessage.Author.Mention)
    "❓" (print $msg "contains invalid information. " .ReactionMessage.Author.Mention)
}}
{{with $emojisArray.Get .Reaction.Emoji.Name}} {{.}} {{end}}
```


### Verification DM message (empty for default)

```golang
{{sendMessage nil (cembed
"title" "Are you a bot?"
"description" (printf "Please solve the CAPTCHA at this link to make sure you're human, before you can enter %s: %s" .Server.Name .Link)
)}}
```


### Notification/Warning message

```goland
Please verify your account!

If you do not verify your account within 10 minutes you get kicked.
```
