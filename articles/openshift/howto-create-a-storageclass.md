---
title: Azure Red Hat OpenShift 4에서 Azure Files StorageClass 만들기
description: Azure Red Hat OpenShift에서 Azure Files StorageClass를 만드는 방법에 대해 알아봅니다.
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: aro, openshift, az aro, red hat, cli, azure 파일
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a3e2a994311334d410aeffd55e7f6febaa85d6e4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842671"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4에서 Azure Files StorageClass 만들기

이 문서에서는 Azure Files를 사용 하 여 ReadWriteMany (RWX) 저장소를 동적으로 프로 비전 하는 Azure Red Hat OpenShift 4 용 StorageClass를 만듭니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 필수 구성 요소를 설정 하 고 필요한 도구를 설치 합니다.
> * Azure 파일 provisioner을 사용 하 여 Azure Red Hat OpenShift 4 StorageClass 만들기

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure CLI 버전 2.6.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

Azure Red Hat OpenShift 4 클러스터를 구독에 배포 합니다. [Azure Red Hat OpenShift 4 클러스터 만들기](tutorial-create-cluster.md) 를 참조 하세요.


### <a name="set-up-azure-storage-account"></a>Azure storage 계정 설정

이 단계에서는 Azure Red Hat OpenShift (ARO) 클러스터의 리소스 그룹 외부에 리소스 그룹을 만듭니다. 이 리소스 그룹은 Azure Red Hat OpenShift의 동적 provisioner에서 만든 Azure Files 공유를 포함 합니다.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>권한 설정
### <a name="set-resource-group-permissions"></a>리소스 그룹 권한 설정

ARO 서비스 주체에는 새 Azure storage 계정 리소스 그룹에 대 한 ' listKeys ' 권한이 있어야 합니다. 이를 위해 ' 참가자 ' 역할을 할당 합니다. 

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER –-query servicePrincipalProfile.clientId -o tsv)

az role assignment create –-role Contributor -–assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>ARO 클러스터 권한 설정

OpenShift 영구 볼륨 바인더 서비스 계정에는 비밀을 읽을 수 있는 기능이 필요 합니다. 이를 위해 OpenShift 클러스터 역할을 만들고 할당 합니다.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Azure Files provisioner를 사용 하 여 StorageClass 만들기

이 단계에서는 Azure Files provisioner를 사용 하 여 StorageClass를 만듭니다. StorageClass 매니페스트에서는 ARO 클러스터가 현재 리소스 그룹 외부의 저장소 계정을 볼 수 있도록 저장소 계정에 대 한 세부 정보가 필요 합니다.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  skuName: Standard_LRS 
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>기본 StorageClass 변경 (옵션)

ARO의 기본 StorageClass는 managed premium 이라고 하며 azure-disk provisioner를 사용 합니다. StorageClass 매니페스트에 대해 patch 명령을 실행 하 여이를 변경 합니다.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Azure 파일 StorageClass 확인 (선택 사항)

새 응용 프로그램을 만들고 저장소를 할당 합니다.

```bash
oc new-project azfiletest
oc new-app –template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
test.txt 파일은 Azure Portal의 Storage 탐색기를 통해서도 표시 됩니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Azure 파일 및 Azure Red Hat OpenShift 4를 사용 하 여 동적 영구 저장소를 만들었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 스토리지 계정 만들기
> * Azure Files provisioner를 사용 하 여 Azure Red Hat OpenShift 4 클러스터에 StorageClass 구성

다음 문서로 이동 하 여 Azure Red Hat OpenShift 4 지원 되는 리소스에 대해 알아보세요.

* [Azure Red Hat OpenShift 지원 정책](support-policies-v4.md)
