Install rabbitmq with server
```shell
~ % brew install rabbitmq
```
Installed Location (for intel mac)
```
# located
/usr/local/Cellar/rabbitmq/3.12.2

# Links to binaries
/usr/local/sbin

# accessible from
/usr/local/opt/rabbitmq/sbin
```

Add directory in PATH
```
~ % export PATH=$PATH:/usr/local/opt/rabbitmq/sbin
```

Find out installed location
```
~ % brew info rabbitmq
```

Starting the Server
```
~ % brew services start rabbitmq
```

check server status
```
~ % rabbitmqctl status
```

```
http://localhost:15672

# username: guest
# password: guest
```

Stopping the Server
```
~ % brew services stop rabbitmq
```

Other commands

ref: https://www.rabbitmq.com/access-control.html

```
# To use Celery we need to create a RabbitMQ user, 
# a virtual host and allow that user access to that virtual host:


rabbitmqctl add_user myuser mypassword
abbitmqctl add_vhost myvhost
rabbitmqctl set_user_tags myuser mytag
rabbitmqctl set_permissions -p myvhost myuser ".*" ".*" ".*"


# Substitute in appropriate values for myuser, mypassword and myvhost above.
```
```
Delete user:
rabbitmqctl delete_user user

Change user password:
rabbitmqctl change_password user password

Create a new virtualhost:
rabbitmqctl add_vhost /my_vhost

Show available virtual hosts:

Remove virtual host:
rabbitmqctl delete_vhost /hostname

Assign rights to virtual host:
rabbitmqctl set_permissions -p /hostname user “. *” “. *” “. *”

List of rights:
rabbitmqctl list_permissions -p /hostname

Show user rights:
rabbitmqctl list_user_permissions user

Remove user rights:
rabbitmqctl clear_permissions -p /hostname user
```

```
Reset everything to default
Completely reset all bunny settings:

rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl start_app
```













