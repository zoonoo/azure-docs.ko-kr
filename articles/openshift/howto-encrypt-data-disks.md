---
title: Azure Red Hat OpenShift (ARO)에서 고객 관리 키 (CMK)를 사용 하 여 영구 볼륨 클레임 암호화
description: Azure Red Hat OpenShift에 대 한 사용자 고유의 키 (BYOK)/고객이 관리 하는 키 (CMK) 배포 지침을 가져옵니다.
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: encryption, byok, aro, cmk, openshift, red hat
ms.openlocfilehash: ca69594952c9fa547390e9a73b48ec8165145378
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505225"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Azure Red Hat OpenShift (ARO) (미리 보기)에서 고객 관리 키 (CMK)를 사용 하 여 영구적 볼륨 클레임 암호화

Azure Storage는 SSE (서버 쪽 암호화)를 사용 하 여 데이터가 클라우드에 유지 될 때 데이터를 자동으로 [암호화](../storage/common/storage-service-encryption.md) 합니다. 기본적으로 데이터는 Microsoft 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대 한 추가 제어를 위해 사용자 고유의 고객이 관리 하는 키를 제공 하 여 Azure Red Hat OpenShift 클러스터에서 데이터를 암호화할 수 있습니다.

> [!NOTE]
> 이 단계에서는 고객이 관리 하는 키를 사용 하 여 ARO 영구 볼륨을 암호화 하는 데 대 한 지원도 있습니다. 현재 마스터 또는 작업자 노드 운영 체제 디스크에는이 기능을 사용할 수 없습니다.

> [!IMPORTANT]
> ARO 미리 보기 기능은 셀프 서비스 옵트인 방식으로 사용할 수 있습니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. ARO 미리 보기는 최상의 노력을 기반으로 고객 지원에 의해 부분적으로 검사 됩니다. 따라서 이러한 기능은 프로덕션 용도로 사용할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 다음을 가정합니다.

* OpenShift 버전 4.4 이상에 기존 ARO 클러스터가 있습니다.

* **Oc** openshift 명령줄 도구인 base64 (core 유틸리티의 일부)와 **az** Azure CLI가 설치 되어 있습니다.

* Kubeadmin (global cluster admin user)로 **oc** 를 사용 하 여 ARO 클러스터에 로그인 합니다.

* ARO 클러스터와 동일한 구독에서 "참여자" 액세스 권한을 부여할 수 있는 계정으로 **az** 를 사용 하 여 Azure CLI에 로그인 합니다.

## <a name="limitations"></a>제한 사항

* 고객 관리 키 암호화에 대 한 가용성은 지역에 맞게 제공 됩니다. 특정 Azure 지역에 대 한 상태를 확인 하려면 [azure 지역][supported-regions]을 확인 합니다.
* Ultra Disks를 사용 하려는 경우 시작 하기 전에 먼저 구독에서 사용 하도록 설정 해야 합니다.

## <a name="declare-cluster--encryption-variables"></a>클러스터 & 암호화 변수 선언
고객이 관리 하는 암호화 키를 사용 하도록 설정 하려는 ARO 클러스터 인 경우 아래 변수를 구성 해야 합니다.
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from *az aro list -o table*
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from *az aro list -o table*
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>구독 ID 가져오기
Azure 구독 ID는 CMK 구성에서 여러 번 사용 됩니다. 가져와서 변수로 저장 합니다.
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 만들기
Azure Key Vault 인스턴스를 사용 하 여 키를 저장 해야 합니다. 보호 제거 및 일시 삭제를 사용 하 여 새 Key Vault을 만듭니다. 그런 다음, 자격 증명 모음 내에 새 키를 만들어 사용자 지정 키를 저장 합니다.

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Azure disk encryption set 만들기

Azure disk encryption 집합은 ARO의 디스크에 대 한 참조 지점으로 사용 됩니다. 이전 단계에서 만든 Azure Key Vault에 연결 되 고 해당 위치에서 고객이 관리 하는 키를 가져옵니다.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Key Vault에 대 한 디스크 암호화 설정 액세스 권한 부여
이전 단계에서 만든 디스크 암호화 집합을 사용 하 여 Azure Key Vault에 대 한 디스크 암호화 설정 액세스 권한을 부여 합니다.

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>역할 할당에 필요한 다른 Id 가져오기
ARO 클러스터에서 디스크 암호화 집합을 사용 하 여 ARO 클러스터의 영구적 볼륨 클레임 (Pvc)을 암호화 하도록 허용 해야 합니다. 이렇게 하려면 새 관리 서비스 ID (MSI)를 만듭니다. 또한 ARO MSI 및 디스크 암호화 집합에 대해 다른 사용 권한을 설정 합니다.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>CMK 암호화에 필요한 다른 역할 할당 구현
이전 단계에서 얻은 변수를 사용 하 여 필요한 역할 할당을 적용 합니다.

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>암호화 된 프리미엄 & 울트라 디스크에 대 한 k8s 저장소 클래스 만들기 (선택 사항)
Premium_LRS 및 UltraSSD_LRS 디스크에 CMK에 사용할 저장소 클래스를 생성 합니다.
```
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

그런 다음, ARO 클러스터에서이 배포를 실행 하 여 저장소 클래스 구성을 적용 합니다.
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 테스트
클러스터에서 PVC 암호화를 위해 고객이 관리 하는 키를 사용 하 고 있는지 확인 하려면 적절 한 저장소 클래스를 사용 하 여 영구적 볼륨 클레임을 만듭니다. 아래 코드 조각에서는 pod를 만들고 표준 디스크를 사용 하 여 영구 볼륨 클레임을 탑재 합니다.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>테스트 pod 구성 파일을 적용 합니다.
아래 명령을 실행 하 여 테스트 Pod 구성을 적용 하 고 새 영구 볼륨 클레임의 UID를 반환 합니다. UID는 CMK를 사용 하 여 디스크가 암호화 되었는지 확인 하는 데 사용 됩니다.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> 경우에 따라 Azure Active Directory 내에서 역할 할당을 적용할 때 약간의 지연이 발생 합니다. 이러한 명령이 실행 되는 속도에 따라 "전체 Azure 디스크 이름 확인" 명령이 성공 하지 못할 수 있습니다. 이 문제가 발생 하는 경우 **oc 설명 pvc mypod-cmk-encryption-pvc** 를 참조 하 여 디스크가 성공적으로 프로 비전 되었는지 확인 합니다. 역할 할당 전파가 완료 되지 않은 경우 Pod &를 *삭제* 하 고이를 *적용* 해야 합니다.
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>"EncryptionAtRestWithCustomerKey"를 사용 하 여 PVC 디스크가 구성 되어 있는지 확인 합니다. 
Pod는 CMK 저장소 클래스를 참조 하는 영구 볼륨 클레임을 만들어야 합니다. 다음 명령을 실행 하면 PVC가 예상 대로 배포 되었는지 확인 됩니다.
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

