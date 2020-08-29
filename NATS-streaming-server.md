### Stan

- `stan` means nats client in community name convention

### Creating a stan (publisher)

```
import nats from 'node-nats-streaming'

const stan = nats.connect('ClusterID', 'ClientID', {
  url: 'http://XXXXX'
})
```

### Queue Group

We can have `Queue Groups` associated to a channel to limit that `only one of listener services` get the desired event.

```
NATS Streaming Server's default channel count is 1000
```

### Creating subscription with Queue Group (listener)

```
const subscription = stan.subscribe('ChannelName', 'QueueGroupName')
subscription.on('message', (msg: Message) => {
  const messageData = msg.getData()
})
```

### Subscription Options

Examples:

```
// create Option
const options = stan.subscriptionOptions()
  .setDeliverAllAvailable()
  .setManualAckMode()
  .setMaxInFlight()
  .setDurableName()

// and pass it as 3rd argument
const subscription = stan.subscrib('ChannelName', 'QueueGroupName', options)

```

#### Example setting ManualAckMode

Default Ack Time is 30 seconds.

```
const options = stan.subscriptionOptions()
  .setManualAckMode(true)

const subscription = stan.subscribe('ChannelName', 'QueueGroupName')
subscription.on('message', (msg: Message) => {
  msg.ack() // acknowledge the message!
})
```

### Message Class

- message.`getSubject()` returns channel name.
- message.`getSequence()` returns the sequence number of the message. (starting from 1)
- message.`getData()` returns the data associated with the message payload. If the stanEncoding is not set to 'binary', a string is returned.

### Monitoring NATS streaming server

`localhost:MONITORINGP-PORT/`

To check specific channel,

`ocalhost:MONITORINGP-PORT/streaming/channels?subs=1`

### Deployment example

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nats-depl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nats
  template:
    metadata:
      labels:
        app: nats
    spec:
      containers:
        - name: nats
          image: nats-streaming:0.17.0
          args: [
              '-p',
              '4222',
              '-m',
              '8222',
              '-hbi', # heartbeat, NATS server is going to send to all of its different connected clients every so many seconds
              '5s',
              '-hbt', # how long each client has to respond
              '5s',
              '-hbf', # number of times that each client can fail before NATS streaming server is going to assume that connection is dead and gone
              '2',
              '-SD',
              '-cid',
              'ticketing',
            ]
---
apiVersion: v1
kind: Service
metadata:
  name: nats-srv
spec:
  selector:
    app: nats
  ports:
    - name: client
      protocol: TCP
      port: 4222
      targetPort: 4222
    - name: monitoring
      protocol: TCP
      port: 8222
      targetPort: 8222
```
