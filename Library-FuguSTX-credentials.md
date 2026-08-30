# FuguSTX — Three-application credential split

Rehearses: the shared instructions, FuguTTX D9

**A policy document is not a permission.**

Evidence: 2026-08-25. The agent key creates every dev, train, and image resource
type. The platform denied the same key `billing consumption list`, although its
policy stated the operator scope. Only a platform response proves authorization.
Scope: one Organization, one policy set, one day.
Maps to: the shared instructions, FuguTTX D9.

**That denial did not reproduce.**

Evidence: 2026-08-28. A consumption read with the agent key passed, and the
pipeline key read the same data. This corrects the 2026-08-25 claim above: the
drift is closed without a policy change.
Scope: one read per key, one day.
Maps to: the shared instructions, FuguTTX D9.

**An IAM rule holds permission sets of one scope type only.**

Evidence: 2026-08-26. The three applications and their policies applied. A
policy that mixes project sets with `BillingReadOnly` or the IAM managers needs
two rules. The second rule takes organization scope, so a shared Organization
cannot hold a per-project IAM administrator.
Scope: one Organization, one policy set.
Maps to: the shared instructions, FuguTTX D9.

**The train-key mint needs an IAM write on the pipeline policy.**

Evidence: 2026-08-28. A probe proved that `IAMApplicationManager` covers api-key
create and delete on an application. The set also mints keys on the operator
application, and the pilot accepts that trade at organization scope.
Scope: one policy set. The shared instructions do not state this gap.
Maps to: the shared instructions, FuguTTX D9.

**A minted key defaults to the wrong project.**

Evidence: 2026-08-28. `scw iam api-key create` without `default-project-id`
binds the key to the organization default project, and the Object Storage calls
of the key then target that project. Every mint must pass the option.
Scope: one CLI version, one Organization.
Maps to: the shared instructions, FuguTTX D9.
