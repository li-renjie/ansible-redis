# ansible-redis

Install redis (single node, replication or cluster mode) on RHEL Linux.

This role installs redis by compiling from source.

## Requirements

None.

## Role Variables

Specify redis version:

    version: 5.0.4

Specify redis listen port and interface:

    redis_port: 6379
    redis_bind: 0.0.0.0

Memory limitation:

    redis_maxmemory: 1073741824      # 0 for unlimited
    redis_maxmemory_policy: noeviction

Deploy configure:

    prefix_dir: "/usr/local"
    install_dir: "{{ prefix_dir }}/redis"

    conf_dir: "/data/redis-{{ redis_port }}/conf"
    data_dir: "/data/redis-{{ redis_port }}/data"
    logs_dir: "/data/redis-{{ redis_port }}/logs"
    run_dir: "/data/redis-{{ redis_port }}/run"

    redis_conf_file: "{{ conf_dir }}/redis-{{ redis_port }}.conf"

More variables are listed in `defaults/main.yml`.

## Dependencies

None.

## Example Playbook

### Single node

Inventory example:

    [redis]
    test-1

Playbook example:

    - hosts: redis
      gather_facts: true
      roles:
        - redis
      vars:
        version: 5.0.4
        redis_port: 6800
        redis_password: "pwd123"

### Master-Slave replication

Inventory example:

    [redis]
    test-1 redis_port=6479
    test-2 redis_port=6480 redis_replicaof=192.168.0.1:6479

Playbook example:

    - hosts: redis
      gather_facts: true
      roles:
        - redis
      vars:
        version: 5.0.4
        redis_password: "pwd123"

### Redis cluster

Inventory example:

    [redis_master]
    test-1 redis_port=6800
    test-2 redis_port=6801
    test-3 redis_port=6802

    [redis_replica]
    test-4 redis_port=6900
    test-5 redis_port=6901
    test-6 redis_port=6902

Playbook example:

    - hosts: redis_master
      gather_facts: true
      roles:
        - redis
      vars:
        redis_password: "hello"
        redis_cluster_enabled: 'yes'
        master_group: redis_master

    - hosts: redis_replica
      gather_facts: true
      roles:
        - redis
      vars:
        redis_password: "hello"
        redis_cluster_enabled: 'yes'
        replica_group: redis_replica
        master: 192.168.10.171:6800

In cluster mode, inventyry master group name (master_group) or replica group name (replica_group) should be specified.

Currently, the replica node can not choose its master node.

## License

BSD
