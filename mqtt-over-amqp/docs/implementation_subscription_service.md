# Subscription Service

It could be based on the same service as in EnMasse project using brokers and durable subscription on topics.

On connection, the client with “clean session = false” want to know if a session is already present (MQTT spec). The SS can check if durable subscriptions already exist for that client (based on subscription name with client id). If a session is available the SS should recover the routes from subscriptions to the direct unique client publish address $mqtt.to.[client-id].publish so that “offline” messages are sent to the client.

When a message is published with a “retain” flag (related property in the related AMQP message), the SS could configure a forwarding of the message from the “topic” to a queue like $retain.[topic] which will store the retained message.

On subscription, the client provides to the SS the following information :

* its direct unique address for receiving published messages, $mqtt.to.[client-id].publish
* the topic it wants to subscribe to

Then the SS :

* it creates a subscription queue (i.e. named $mqtt.to.[client-id].publish)
* an available retained message is copied to this queue (from the corresponding $retain.[topic] queue) and it is received as first message by the client.
* it binds the subscription queue to the requested topic so that the client starts to receive all messages published to that topic.
