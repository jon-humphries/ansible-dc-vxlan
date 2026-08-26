# Fork: Data-model driven SGT (`ENABLE_SGT`)

Fork of `netascode/ansible-dc-vxlan` @ tag `0.8.1`, branch `feature/sgt-datamodel`.

## Why
Upstream hardcodes `ENABLE_SGT` off in every fabric security template, so each NAC
run reverts SGT/TrustSec that was enabled out-of-band (e.g. a separate Ansible
playbook). This fork makes SGT a per-fabric data-model attribute so NAC can enable
it on some fabrics and leave it off on others, deterministically.

## Data model keys (both default to `false` -> unchanged behaviour)

Member VXLAN EVPN fabric (type `VXLAN_EVPN`):
```yaml
vxlan:
  global:
    ibgp:
      security:
        enable_sgt: true            # emits ENABLE_SGT: true
        sgt_mac_segmentation: false # only emitted on NDFC >= 12.5.0 when enable_sgt
```

MSD / external (mcfg) fabric:
```yaml
vxlan:
  multisite:
    security:
      enable_sgt: true              # emits ENABLE_SGT: "on"
```

## Behaviour notes
- Defaults live in `roles/validate/files/defaults.yml`; omitting the key = `false`.
- NDFC does not support SGT with an IPv6 underlay, so on member fabrics the key is
  only emitted for IPv4 underlay fabrics (upstream guard preserved).
- `securityGroupTagMacSegmentation` / `_PREV` are only emitted on NDFC >= 12.5.0.

## Files changed
- roles/dtc/common/templates/ndfc_fabric/dc_vxlan_fabric/security/dc_vxlan_fabric_security.j2
- roles/dtc/common/templates/ndfc_fabric/msd_fabric/security/msd_fabric_security.j2
- roles/dtc/common/templates/ndfc_fabric/mcfg_fabric/security/mcfg_fabric_security.j2
- roles/validate/files/defaults.yml
