
Vault

1.  secret broker
2.  dev first model

common use case

1.  central secret management

dynamic secret / secure secrets /

K/V Secrets engine

1.  stored as key-value pairs
2.  two version: v1(kv), v2 (kv-v2)
3.  v2 is used by default
4.  v2 has versioning

Namespace

1.  virtual vault, vaults within a vault
2.  each have their own authn, authz, secrets
3.  vault as service
    1.  tenant isolation
    2.  self management

lab 1

Start vault in dev mode

```jsx
vault server -dev -dev-root-token-id="roo
```

Export vault addr

```jsx

export VAULT_ADDR='<http://127.0.0.1:8200>

```

Login to vault

```jsx
vault login
vault status
```

Check vault status

in dev mode ( secrets stored in memory ) in prod ( secrets stored in secret backend ) uses raft consensus mode

```jsx
% vault status
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    1
Threshold       1
Version         1.11.1
Build Date      2022-07-19T20:16:47Z
Storage Type    inmem
Cluster Name    vault-cluster-8282ee75
Cluster ID      c36b3d60-931b-fe67-76ca-f41ba031d8dd
HA Enabled      false
```

root token stoted in

```jsx
➜  ~ cat ~/.vault-token
root%
```

list policy

view policy

Token lookup

```jsx
➜  ~ vault token lookup
Key                 Value
---                 -----
accessor            vNjKij2VegGZkZ9KgrmvZDsD
creation_time       1662948527
creation_ttl        0s
display_name        token
entity_id           n/a
expire_time         <nil>
explicit_max_ttl    0s
id                  root
issue_time          2022-09-12T12:08:47.96663+10:00
meta                <nil>
num_uses            0
orphan              true
path                auth/token/create
policies            [root]
renewable           false
ttl                 0s
type                service
```

check path

```jsx
vault path-help secret
```

list auth methods

```jsx
➜  ~ vault auth list
Path      Type     Accessor               Description
----      ----     --------               -----------
token/    token    auth_token_68c6d6ec    token based credentials
```

vault secrets list

```jsx
➜  ~ vault secrets list
Path          Type         Accessor              Description
----          ----         --------              -----------
cubbyhole/    cubbyhole    cubbyhole_9f79315e    per-token private secret storage
identity/     identity     identity_d758727d     identity store
secret/       kv           kv_821ac46c           key/value secret storage
sys/          system       system_3906b649       system endpoints used for control, policy and debugging
```

vault ssh

create secret

```jsx
➜  ~ vault kv put -mount=secret foo bar=baz
= Secret Path =
secret/data/foo

======= Metadata =======
Key                Value
---                -----
created_time       2022-09-12T02:30:39.830546Z
custom_metadata    <nil>
deletion_time      n/a
destroyed          false
version            1
```

create secret from file ( recommended )

```jsx
vault kv put -mount=secret user name=@data
```

delete secret

```jsx
➜  ~ vault kv delete secret/user/foo
Success! Data deleted (if it existed) at: secret/data/user/foo
```