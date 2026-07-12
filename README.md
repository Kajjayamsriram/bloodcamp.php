#bloodcamp App

## simple app flow
```
index.html
signup.php
index.php > search.php
           > donate-blood.php
           > find-donar.php
```

## comamnds to create secret in aws 
### Install plugin AWS SECRETS MANAGER plugin
```
aws secretsmanager create-secret --name 'sonar-token' --secret-string 'sonar-token-copy' --tags 'Key=jenkins:credentials:type,Value=string' --description 'stoken'

##For deleting secret
aws secretsmanager delete-secret --secret-id sonar-token --force-delete-without-recovery

Note:
#username/passowrd
aws secretsmanager create-secret --name 'dockerhublogin' --secret-string 'docker-token-copy' --tags 'Key=jenkins:credentials:type,Value=dockerpass' --description 'dockerhub'

#keypairs
aws secretsmanager create-secret --name 'luffy-key' --secret-string "$(cat luffy-private.pem)" --tags 'Key=jenkins:credentials:type,Value=sshUserPrivateKey' 'Key=jenkins:credentials:username,Value=luffy' --description 'luffy-key'
```
