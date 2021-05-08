---
title: Velero를 사용하여 Azure Red Hat OpenShift 4 클러스터 애플리케이션 백업 만들기
description: Velero를 사용하여 Azure Red Hat OpenShift 클러스터 애플리케이션의 백업을 만드는 방법을 알아봅니다.
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 1be3c1182ef5491f70d4010d4c7e73ef43b87853
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107883907"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Azure Red Hat OpenShift 4 클러스터 애플리케이션 백업 만들기

이 문서에서는 Azure Red Hat OpenShift 4 클러스터 애플리케이션 백업을 만들기 위한 환경을 준비합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 필수 구성 요소 설정 및 필요한 도구 설치
> * Azure Red Hat OpenShift 4 애플리케이션 백업 만들기

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure CLI 버전 2.6.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="install-velero"></a>Velero 설치

시스템에 Velero를 [설치](https://velero.io/docs/main/basic-install/)하려면 운영 체제에 권장되는 프로세스를 따릅니다.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Azure 스토리지 계정 및 Blob 컨테이너 설정

이 단계에서는 ARO 클러스터의 리소스 그룹 외부에 리소스 그룹을 만듭니다.  이 리소스 그룹을 통해 백업을 유지하고 애플리케이션을 새 클러스터로 복원할 수 있습니다.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Velero에 대한 권한 설정

### <a name="create-service-principal"></a>서비스 주체 만들기

Velero에서 백원 및 복원을 수행하려면 권한이 필요합니다. 서비스 주체를 만들면 이전 단계에서 정의한 리소스 그룹에 액세스할 수 있는 권한을 Velero에 부여하는 것입니다. 이 단계에서는 클러스터의 리소스 그룹을 가져옵니다.

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 클러스터에 Velero 설치

이 단계에서는 Velero를 사용하여 백업 및 복원을 수행하는 데 필요한 [사용자 지정 리소스 정의](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) 및 자체 프로젝트에 Velero를 설치합니다. Azure Red Hat OpenShift v4 클러스터에 성공적으로 로그인했는지 확인합니다.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Velero를 사용하여 백업 만들기

Velero를 사용하여 애플리케이션 백업을 만들려면 이 애플리케이션이 속해 있는 네임스페이스를 포함해야 합니다.  `nginx-example` 네임스페이스가 있고 해당 네임스페이스의 모든 리소스를 백업에 포함하려면 터미널에서 다음 명령을 실행합니다.

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
다음을 실행하여 백업의 상태를 확인할 수 있습니다.

```bash
oc get backups -n velero <name of backup> -o yaml
```

백업이 성공하면 `phase:Completed`가 출력되고 개체는 스토리지 계정의 컨테이너에 저장됩니다.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>스냅샷을 포함하도록 Velero를 사용하여 백업 만들기

애플리케이션의 영구 볼륨을 포함하도록 Velero를 사용하여 애플리케이션 백업을 만들려면 애플리케이션이 속해 있는 네임스페이스를 포함할 뿐만 아니라 백업을 만들 때 `snapshot-volumes=true` 플래그를 포함해야 합니다.

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

다음을 실행하여 백업의 상태를 확인할 수 있습니다.

```bash
oc get backups -n velero <name of backup> -o yaml
```

백업이 성공하면 `phase:Completed`가 출력되고 개체는 스토리지 계정의 컨테이너에 저장됩니다.

Velero를 사용하여 백업을 만들고 복원하는 방법에 대한 자세한 내용은 [Backup OpenShift resources the native way](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)(기본 방법으로 OpenShift 리소스 백업)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Red Hat OpenShift 4 클러스터 애플리케이션을 백업했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Velero를 사용하여 OpenShift v4 클러스터 애플리케이션 백업 만들기
> * Velero를 사용하여 스냅샷이 포함된 OpenShift v4 클러스터 애플리케이션 백업 만들기


Azure Red Hat OpenShift 4 클러스터 애플리케이션 복원을 만드는 방법을 알아보려면 다음 문서를 진행하세요.

* [Azure Red Hat OpenShift 4 클러스터 애플리케이션 복원 만들기](howto-create-a-restore.md)
* [스냅샷을 포함하여 Azure Red Hat OpenShift 4 클러스터 애플리케이션 복원 만들기](howto-create-a-restore.md)
