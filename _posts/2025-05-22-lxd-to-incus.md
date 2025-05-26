---
layout : post
title : "LXD to INCUS"
categories : [lxd, incus]
published : false
---

### ERROR
```shell
$ sudo cat /var/log/incus/incus.log

time="2025-05-22T17:14:35+07:00" level=error msg="Failed to start the daemon" err="Failed to initialize global database: failed to ensure schema: failed to execute queries from /var/lib/incus/database/patch.global.sql: no such trigger: on_instance_snaphot_delete"
```

>>> Given it’s failing on deleting a trigger, you could go and edit /var/lib/incus/database/patch.global.sql and change all “DROP TABLE” to “DROP TABLE IF EXISTS” and all “DROP TRIGGER” to “DROP TRIGGER IF EXISTS” which should get the database patch to clear.



```
INSERT INTO certificates_projects (certificate_id, project_id) SELECT identity_id, project_id FROM identities_projects;
DROP TRIGGER on_auth_group_delete;
DROP TRIGGER on_cluster_group_delete;
DROP TRIGGER on_identity_delete;
DROP TRIGGER on_identity_provider_group_delete;
DROP TRIGGER on_image_alias_delete;
DROP TRIGGER on_image_delete;
DROP TRIGGER on_instance_backup_delete;
DROP TRIGGER on_instance_delete;
DROP TRIGGER on_instance_snaphot_delete;
DROP TRIGGER on_network_acl_delete;
DROP TRIGGER on_network_delete;
DROP TRIGGER on_network_zone_delete;
DROP TRIGGER on_node_delete;
DROP TRIGGER on_operation_delete;
DROP TRIGGER on_profile_delete;
DROP TRIGGER on_project_delete;
DROP TRIGGER on_storage_bucket_delete;
DROP TRIGGER on_storage_pool_delete;
DROP TRIGGER on_storage_volume_backup_delete;
DROP TRIGGER on_storage_volume_delete;
DROP TRIGGER on_storage_volume_snapshot_delete;
DROP TRIGGER on_warning_delete;

```


DROP TRIGGER on_instance_delete;


DROP TRIGGER IF EXISTS on_instance_delete;



```shell
time="2025-05-22T17:39:39+07:00" level=warning msg="Instance type not operational" driver=qemu err="QEMU command not available for CPU architecture" type=virtual-machine
time="2025-05-22T17:39:41+07:00" level=error msg="Failed to start the daemon" err="Failed to initialize global database: failed to ensure schema: failed to execute queries from /var/lib/incus/database/patch.global.sql: table certificates already exists"
```

### Fixing "no such table: identities" Error in Ubuntu Incus
The error "no such table: identities" when working with Incus 
(LXC's next-generation system container manager) typically 
occurs when the database schema is outdated or corrupted.

#### Alternative approach - manual schema update:
If you know what you're doing, you can manually create the missing table:

```shell
sudo incus admin sql global "CREATE TABLE identities 
(id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, 
type INTEGER NOT NULL, 
name TEXT NOT NULL, 
identifier TEXT NOT NULL)"
```

### no such column: metadata"
```sql
CREATE TABLE IF NOT EXISTS identities (
    id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    type INTEGER NOT NULL,
    name TEXT NOT NULL,
    identifier TEXT NOT NULL,
    metadata TEXT
);

--- If Table Exists But Missing Column
ALTER TABLE identities ADD COLUMN metadata TEXT;
```

### no such table: identities_projects"
```sql
CREATE TABLE IF NOT EXISTS identities_projects (
    identity_id INTEGER NOT NULL,
    project_id INTEGER NOT NULL,
    role_id INTEGER NOT NULL,
    FOREIGN KEY (identity_id) REFERENCES identities (id) ON DELETE CASCADE,
    FOREIGN KEY (project_id) REFERENCES projects (id) ON DELETE CASCADE,
    FOREIGN KEY (role_id) REFERENCES identities_roles (id) ON DELETE CASCADE,
    PRIMARY KEY (identity_id, project_id)
);

CREATE TABLE IF NOT EXISTS identities_roles (
    id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    name TEXT NOT NULL,
    description TEXT NOT NULL,
    UNIQUE (name));

```

### no such trigger: on_auth_group_delete"

```sql
DROP TRIGGER IF EXISTS on_auth_group_delete;
DROP TRIGGER IF EXISTS on_cluster_group_delete;
DROP TRIGGER IF EXISTS on_identity_delete;
```

### no such table: auth_groups_permissions"

```sql
DROP TABLE IF EXISTS identities_projects;
DROP TABLE IF EXISTS auth_groups_permissions;
DROP TABLE IF EXISTS auth_groups_identity_provider_groups;
DROP TABLE IF EXISTS identities_auth_groups;
DROP TABLE IF EXISTS identity_provider_groups;
DROP TABLE IF EXISTS identities;
DROP TABLE IF EXISTS auth_groups;
```

### Failed adding description column to certificate: duplicate column name: description"



### [error in trigger on_instance_snapshot_delete: no such table: main.auth_groups_permissions"](https://github.com/zabbly/incus/issues/78)

```
DROP TRIGGER IF EXISTS on_instance_snapshot_delete;

```



### Reference
1. [Lxd-to-incus migration failed](https://discuss.linuxcontainers.org/t/lxd-to-incus-migration-failed-in-manjaro-failed-getting-target-server-info/22627)




```sql
UPDATE storage_pools_config SET value='/var/lib/incus/disks/default.img' WHERE value='/var/snap/lxd/common/lxd/disks/default.img';
UPDATE profiles SET description='Default Incus profile' WHERE description='Default LXD profile';
UPDATE projects SET description='Default Incus project' WHERE description='Default LXD project';
CREATE TABLE  IF NOT EXISTS certificates (
    id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    fingerprint TEXT NOT NULL,
    type INTEGER NOT NULL,
    name TEXT NOT NULL,
    certificate TEXT NOT NULL,
    restricted INTEGER NOT NULL DEFAULT 0,
    UNIQUE (fingerprint)
);
CREATE TABLE IF NOT EXISTS  "certificates_projects" (
    certificate_id INTEGER NOT NULL,
    project_id INTEGER NOT NULL,
    FOREIGN KEY (certificate_id) REFERENCES certificates (id) ON DELETE CASCADE,
    FOREIGN KEY (project_id) REFERENCES "projects" (id) ON DELETE CASCADE,
    UNIQUE (certificate_id, project_id)
);


CREATE TABLE IF NOT EXISTS identities (
        id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
        type INTEGER NOT NULL,
        name TEXT NOT NULL,
        identifier TEXT NOT NULL);

ALTER TABLE identities ADD COLUMN metadata TEXT;


CREATE TABLE IF NOT EXISTS identities_projects (
    identity_id INTEGER NOT NULL,
    project_id INTEGER NOT NULL,
    role_id INTEGER NOT NULL,
    FOREIGN KEY (identity_id) REFERENCES identities (id) ON DELETE CASCADE,
    FOREIGN KEY (project_id) REFERENCES projects (id) ON DELETE CASCADE,
    FOREIGN KEY (role_id) REFERENCES identities_roles (id) ON DELETE CASCADE,
    PRIMARY KEY (identity_id, project_id)
);

CREATE TABLE IF NOT EXISTS identities_roles (
    id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    name TEXT NOT NULL,
    description TEXT NOT NULL,
    UNIQUE (name));

DELETE FROM schema WHERE version < 73;
UPDATE schema SET version=69 WHERE version=73;


INSERT INTO certificates (id, fingerprint, type, name, certificate, restricted) SELECT id, identifier, 2, name, json_extract(metadata, "$.cert"), 0 FROM identities WHERE type=3;
INSERT INTO certificates (id, fingerprint, type, name, certificate, restricted) SELECT id, identifier, 3, name, json_extract(metadata, "$.cert"), 1 FROM identities WHERE type=4;
INSERT INTO certificates (id, fingerprint, type, name, certificate, restricted) SELECT id, identifier, 3, name, json_extract(metadata, "$.cert"), 0 FROM identities WHERE type=6;
INSERT INTO certificates_projects (certificate_id, project_id) SELECT identity_id, project_id FROM identities_projects;
DROP TRIGGER IF EXISTS on_auth_group_delete;
DROP TRIGGER IF EXISTS on_cluster_group_delete;
DROP TRIGGER IF EXISTS on_identity_delete;
DROP TRIGGER IF EXISTS on_identity_provider_group_delete;
DROP TRIGGER IF EXISTS on_image_alias_delete;
DROP TRIGGER IF EXISTS on_image_delete;
DROP TRIGGER IF EXISTS on_instance_backup_delete;
DROP TRIGGER IF EXISTS on_instance_delete;
DROP TRIGGER IF EXISTS on_instance_snaphot_delete;
DROP TRIGGER IF EXISTS on_network_acl_delete;
DROP TRIGGER IF EXISTS on_network_delete;
DROP TRIGGER IF EXISTS on_network_zone_delete;
DROP TRIGGER IF EXISTS on_node_delete;
DROP TRIGGER IF EXISTS on_operation_delete;
DROP TRIGGER IF EXISTS on_profile_delete;
DROP TRIGGER IF EXISTS on_project_delete;
DROP TRIGGER IF EXISTS on_storage_bucket_delete;
DROP TRIGGER IF EXISTS on_storage_pool_delete;
DROP TRIGGER IF EXISTS on_storage_volume_backup_delete;
DROP TRIGGER IF EXISTS on_storage_volume_delete;
DROP TRIGGER IF EXISTS on_storage_volume_snapshot_delete;
DROP TRIGGER IF EXISTS on_warning_delete;
DROP TABLE IF EXISTS identities_projects;
DROP TABLE IF EXISTS auth_groups_permissions;
DROP TABLE IF EXISTS auth_groups_identity_provider_groups;
DROP TABLE IF EXISTS identities_auth_groups;
DROP TABLE IF EXISTS identity_provider_groups;
DROP TABLE IF EXISTS identities;
DROP TABLE IF EXISTS auth_groups;
```