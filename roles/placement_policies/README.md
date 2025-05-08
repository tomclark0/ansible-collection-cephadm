# Placement Policies

Placement policies manage which pools are used to store which data when using RGW.
This role creates and updates arbitrary placement policies for usage with RGW.
## Prerequisites

### Host prerequisites

* The role assumes target hosts connection over SSH with user that has passwordless sudo configured.
* Either direct Internet access or private registry with desired Ceph image accessible to all hosts is required.

### Inventory

This role assumes the existence of the following groups:

* `mons`

All Ceph hosts must be in the `ceph` group.

This role is executed on `cephadm_bootstrap_host`. This defaults to the
first member of the mon group.

### Pools

* This role assumes all pools given are already defined.

## Role variables

* `cephadm_rgw_placement_policy_zone`:
Defaults to `default`. Changes only required for multisite configuration.

* `cephadm_rgw_placement_policies`:
Placement policies manage which pools are used for what data.
Each placement policy expects:
`key:` This is the name of the placement policy.
`index_pool:` The pool used for RGW indexes
`data_extra_pool:` The replicated pool used for multipart uploads.
`storage_classes:`
  `name:` The name of the storage storage class.
  `data_pool:` The pool containing binary blob data.
  `compression_type:` OPTIONAL - Defaults to undefined. Supported options include: `lz4 snappy zlib zstd`

  Example:
  ```
  cephadm_rgw_placement_policies:
  - key: "default-placement"
    index_pool: "default.rgw.buckets.index"
    data_extra_pool: "default.rgw.buckets.non-ec"
    storage_classes:
      - name: "STANDARD"
        data_pool: "default.rgw.buckets.data"
      - name: "STANDARD_ARCHIVE"
        data_pool: "default.rgw.glacier.data"
        compression_type: "lz4"
      - name: "HIGH_SPEED"
        data_pool: "default.rgw.buckets.3x-NVME"
  - key: "temporary"
    index_pool: "default.rgw.temporary.index"
    data_extra_pool: "default.rgw.temporary.non-ec"
    inline_data: true
    storage_classes:
      - name: "STANDARD"
        data_pool: "default.rgw.temporary.data"
  ```
