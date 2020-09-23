# Slightly Modified Velero Chart

For use with GitOps and ArgoCD.

Helm isn't great with CRDs in general, so Velero uses hooks to manage them to support both Helm v2 and Helm v3. Unfortunately, hooks are not considered permanent resources, so ArgoCD wants to recreate them all the time.

To avoid this from happening, we have forked the chart, and we remove all Helm hooks from CRDs and CRs.

Relevant Issues:

- https://github.com/vmware-tanzu/helm-charts/issues/158
- https://github.com/vmware-tanzu/helm-charts/issues/44

# How to Generate this Chart

If we need to bump the version of this chart, we need to clear out the helm hooks from `main`.

1. Check the status of those two issues linked above. If the maintainers have implemented a proper fix for this, we may be able to use the chart as-is and not fork it.
2. Rebase `main` off of `upstream/main`: `git fetch upstream && rebase upstream/main`. If this results in conflicts, resolve them before continuing. When in doubt, remember: the end goal is for the helm hooks to be gone.
3. Remove all instances of `helm.sh/hook`:

```
sed -i'' '/helm.sh\/hook/d' charts/velero/crds/*
sed -i'' '/helm.sh\/hook/d' charts/velero/templates/*
```

5. Double check that the only files that changed are the CRDs in the `crds/` directory, and two CRs: backupstoragelocation and volumesnapshotlocation.
6. Commit and PR against ezcater/vmware-helm-charts.
