### Response automatically to Reputation plus or minus

This requires you to enable YAGPDB's Reputation system which is located under `Fun - Reputation`. Once you've done that setup another CC (e.g. `Reputation Automation`) Group.


### Respond to rep+

```ts
{{/* StartsWith: +rep - Case sensitive trigger: false - Group: Ungrouped */}}
{{if gt (len .Args) 1}}
{{with (userArg (index .Args 1))}}
{{/* What you like to say after rep+ */}}
{{joinStr "" $.User.Mention " gave " .Mention  " a rep boost!"}}
{{if toInt (index $.Args 1)}}{{exec "giverep" .}}{{end}}
{{end}}
{{else}}
You didn't specify who
{{end}}
```


### Respond to rep-

```ts
{{/* StartsWith: +rep - Case sensitive trigger: false - Group: Ungrouped */}}
{{if gt (len .Args) 1}}
{{with (userArg (index .Args 1))}}
{{/* What you like to say after rep+ */}}
{{joinStr "" $.User.Mention " took " .Mention  " a rep, sorry son."}}
{{if toInt (index $.Args 1)}}{{exec "takerep" .}}{{end}}
{{end}}
{{else}}
You didn't specify who
{{end}}
```
