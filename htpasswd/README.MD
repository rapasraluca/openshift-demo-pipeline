# Install htpasswd identity provider

create the htpasswd file

```bash
htpasswd -c -B -b users.htpasswd user password
```

create the secret with password list

```bash
oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd -n openshift-config
```

create the manifest:

```yaml
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: my_htpasswd_provider 
    mappingMethod: claim 
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpass-secret
```

and apply it 

```bash
oc apply -f htpass.yaml
```
