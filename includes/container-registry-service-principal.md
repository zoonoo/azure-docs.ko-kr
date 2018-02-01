## <a name="create-a-service-principal"></a>서비스 주체 만들기

컨테이너 레지스트리에 액세스할 수 있는 서비스 주체를 만들려면 다음 스크립트를 사용합니다. `ACR_NAME` 변수를 컨테이너 레지스트리 이름으로 업데이트하고 선택적으로 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 명령의 `--role` 값을 사용하여 다른 권한을 부여합니다. 이 스크립트를 사용하려면 [Azure CLI](/cli/azure/install-azure-cli)가 설치되어 있어야 합니다.

스크립트를 실행한 후 서비스 주체의 **ID**와 **암호**를 기록해 둡니다. 자격 증명이 있으면 컨테이너 레지스트리를 서비스 주체로 인증하도록 응용 프로그램과 서비스를 구성할 수 있습니다.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>기존 서비스 주체 사용

기존 서비스 주체에 레지스트리 액세스 권한을 부여하려면 서비스 주체에 새 역할을 할당해야 합니다. 새 서비스 주체를 만들 때처럼 풀, 푸시 및 풀, 소유자 액세스를 부여할 수 있습니다.

다음 스크립트는 `SERVICE_PRINCIPAL_ID` 변수에 지정한 서비스 주체에게 [az role assignment create][az-role-assignment-create] 명령을 사용하여 *풀* 권한을 부여합니다. 다른 수준의 액세스 권한을 부여하려면 `--role` 값을 조정합니다.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create