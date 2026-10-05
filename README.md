# nutz-artifacts

The payout Bundles of the Nut Vault, one directory per Epoch, published by `nutz-keeper` right
after each Root is posted (or, for an Epoch with no Root, as soon as it is computed). Data only:
nothing here is code, and nothing here is authoritative. The chain is; these files let anyone
check it.

## Layout

```
epochs/<e>/input.json         what the computation read: chain, Distributor, window, end block, funding, carry
epochs/<e>/allocations.json   every Holder's allocation, ascending by address
epochs/<e>/tree.json          the OpenZeppelin standard-v1 Merkle tree of the claims (absent when the Epoch has no Root)
epochs/<e>/root.txt           the Root this Bundle computes for the Epoch; empty when it has none
```

One commit per Epoch, by `nutz-keeper <keeper@nutz.wtf>`, titled `epoch <e>: root <root>` or
`epoch <e>: no Root`. The keeper never rewrites history; a recomputed Bundle is a new commit.
The Root the Distributor holds is the one that pays out: when it differs from `root.txt`, the
verifier below says so.
`epochs/<e>/` at any commit that carries it is that Epoch's Bundle, so a raw URL at a commit
sha (`https://raw.githubusercontent.com/nutzwtf/nutz-artifacts/<sha>/epochs/<e>/`) is stable.

## Verifying an Epoch

[`nutz-verify`](https://github.com/nutzwtf/nutz-verify) recomputes an Epoch from the chain and
compares it with the Root the Distributor holds; given a checkout of this repository it also
diffs its result against the published Bundle:

```
nutz-verify epoch <e> --artifacts epochs/<e> --rpc <url>
```

`MATCH` with no diff means the Bundle here is what the chain pays out. Anything else is worth a
report: the Dispute window exists for exactly that.
