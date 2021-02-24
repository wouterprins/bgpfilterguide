---
layout: page
title: Rejecting RPKI Invalid BGP Routes
permalink: /guides/reject_invalids/
---

* TOC
{:toc}

# Reject RPKI Invalid BGP Routes

## Purpose

A complete filter for any BGP configuration should reject RPKI invalid BGP routes.

Whether a route is RPKI *invalid* or not is determined following the
[RFC 6811](https://tools.ietf.org/html/rfc6811) procedure.

It is considered *harmfull* to manipulate BGP Path Attributes (such as LOCAL_PREF or BGP Communities)
based on the RPKI Origin Validation state.

# Configuration Examples

## OpenBGPD
```
deny quick from ebgp ovs invalid
deny quick to ebgp ovs invalid
```

## BIRD
```
function reject_invalids()
{
        if (roa_check(roas, net, bgp_path.last) == ROA_INVALID) then {
                print "Reject: RPKI Invalid: ", net, " ", bgp_path;
                reject;
        }
}
```

## Junos
```
policy-statement reject_invalids {
  term invalid {
    from {
      protocol bgp;
        validation-database invalid;
    }
    then {
      validation-state invalid;
      reject;
    }
  }
}
```

## Cisco classic IOS and IOS XE

```
route-map ebgp-in deny 1
 match rpki invalid
!
```