# Event Sinks

This reproduction is for registering an event sink and testing that it has been
configured correctly.

## Requirements

* Nomad
* Go (to run the included `webhook_server.go`)

#### Start Nomad
```
$ nomad agent -dev
```

#### Register an event sink
```
$ cat all-jobs-sink.json
{
  "ID": "all-jobs",
  "Address": "http://127.0.0.1:8080",
  "Topics": {
    "Job": [
      "*"
    ]
  },
  "Type": "webhook"
}
$ nomad event sink register all-jobs-sink.json
```

#### Start the webhook server
```
# in a separate terminal
$ go run webhook_server.go 8080 # this port comes from the Address in all-jobs-sink.json
```

#### Run an example job
```
$ nomad job run example.nomad
```

#### See the event in the webhook server output
```
$ go run webhook_server.go 8080
{"Index":15,"Events":[{"Topic":"Job","Type":"JobRegistered","Key":"example"...
```
