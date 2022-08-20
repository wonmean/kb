# RabbitMQ

## List all queues

```bash
sudo rabbitmqctl list_queues
```

## Delete queue

```bash
sudo rabbitmqctl delete_queue <queue>
```

## List all consumers

```bash
sudo rabbitmqctl list_consumers
```

## Delete all queues

```bash
sudo rabbitmqctl stop_app
sudo rabbitmqctl reset
sudo rabbitmqctl start_app
```

This one seems the most complete.  
Use after `sudo -i`.

```bash
for q in `rabbitmqctl list_queues`; do
    curl -i -u <user>:<password> -XDELETE http://localhost:15672/api/queues/%2f/$q
done
```

## Service fails to start

This may be due to a corrupt or previously locked database.  
First to check is the start up logs.

```bash
sudo vi /var/log/rabbitmq/startup_err
```

If the log shows a locked database, an upgrade failed.  
[Reference](https://stackoverflow.com/questions/41294754/rabbitmq-server-start-failed-with-file-locked).

```text
(Found lock file at ~s.
Either previous upgrade is in progress or has failed.
Database backup path: ~s)
```

One fix is to wipe clean the database, which will delete all current messages.
Moving will effectively do the same as removing.

```bash
sudo mv /var/lib/rabbitmq/mnesia/ /var/lib/rabbitmq/mnesia.20220412
```

Reinstall `rabbitmq-server` afterwards.
