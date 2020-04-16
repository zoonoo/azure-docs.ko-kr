---
title: Azure Red Hat 오픈시프트 4.3 클러스터 만들기 | 마이크로 소프트 문서
description: Azure Red Hat 오픈시프트 4.3을 사용하여 클러스터 만들기
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, 오픈 시프트, 아즈 아로, 빨간 모자, cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398885"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Azure Red Hat OpenShift 4.3 클러스터 생성, 액세스 및 관리

> [!IMPORTANT]
> Azure Red Hat OpenShift 4.3은 현재 미국 동부 및 미국 동부 2의 비공개 미리 보기에서만 사용할 수 있습니다. 비공개 미리 보기 수락은 초대에 의해서만 가능합니다. 이 기능을 활성화하기 전에 구독을 등록하십시오: [Azure Red Hat OpenShift 개인 미리 보기 등록](https://aka.ms/aro-preview-register)

> [!NOTE]
> 미리 보기 기능은 셀프 서비스이며 현재 제공된 대로 제공되며 서비스 수준 계약(SLA) 및 제한 보증에서 제외됩니다. 따라서 이 기능은 프로덕션용이 아닙니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Red Hat OpenShift 4.3 클러스터를 만들려면 다음이 필요합니다.

- Azure CLI 버전 2.0.72 이상
  
- '아즈 아로' 확장

- 네트워크 보안 그룹이 연결되지 않은 두 개의 빈 서브넷이 포함된 가상 네트워크입니다.  클러스터가 이러한 서브넷에 배포됩니다.

- 클러스터 AAD 응용 프로그램(클라이언트 ID 및 비밀) 및 서비스 주체 또는 AAD 응용 프로그램 및 서비스 주체를 자동으로 만들 수 있는 `az aro create` 충분한 AAD 권한입니다.

- RP 서비스 주체와 클러스터 서비스 주체는 각각 클러스터 가상 네트워크에서 기여자 역할이 있어야 합니다.  가상 네트워크에 `az aro create` "사용자 액세스 관리자" 역할이 있는 경우 자동으로 역할 할당을 설정합니다.

### <a name="install-the-az-aro-extension"></a>'az aro' 확장 설치
확장을 `az aro` 사용하면 Azure CLI를 사용하여 명령줄에서 직접 Azure Red Hat OpenShift 클러스터를 생성, 액세스 및 삭제할 수 있습니다.

> [!Note] 
> 확장이 `az aro` 미리 보기에서 현재 상태입니다. 향후 릴리스에서 변경되거나 제거될 수 있습니다.
> `az aro` 확장 미리 보기에 옵트인하려면 리소스 `Microsoft.RedHatOpenShift` 공급자를 등록해야 합니다.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Azure에 로그인합니다.

   ```console
   az login
   ```

2. 다음 명령을 실행하여 `az aro` 확장을 설치합니다.

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. ARO 확장이 등록되었는지 확인합니다.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>빨간 모자 당기기 비밀 받기(선택 사항)

Red Hat 끌어오기 비밀을 사용하면 클러스터가 Red Hat 컨테이너 레지스트리 및 추가 콘텐츠에 액세스할 수 있습니다. 끌어오기 비밀을 사용하는 것은 선택 사항이지만 권장됩니다.

풀 시크릿을 얻으려면 다음을 수행하십시오.

1. [https://editor.swagger.io](https://cloud.redhat.com/openshift/install/azure/aro-provisioned ) 로 이동합니다.
1. Red Hat 계정에 로그인하거나 비즈니스 이메일을 사용하여 새 Red Hat 계정을 만드세요. 이용 약관에 동의해야 합니다.
1. **클릭 비밀 다운로드를 선택합니다.**

어딘가에 안전하게 *pull-secret.txt* 파일을 저장합니다. 클러스터를 만들 때마다 파일을 사용합니다.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>두 개의 빈 서브넷이 포함된 가상 네트워크 만들기

다음 단계에 따라 두 개의 빈 서브넷이 포함된 가상 네트워크를 만듭니다.

1. 다음 변수를 설정합니다.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. 클러스터에 대한 리소스 그룹을 만듭니다.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. 가상 네트워크 만들기

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. 가상 네트워크에 두 개의 빈 서브넷을 추가합니다.

   ```console
   for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. 가상 네트워크 및 서브넷에서 개인 링크 서비스에 대한 네트워크 정책을 사용하지 않도록 설정합니다. 이는 ARO 서비스가 클러스터에 액세스하고 관리하기 위한 요구 사항입니다.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>클러스터 만들기

다음 명령을 실행하여 클러스터를 만듭니다.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> 일반적으로 클러스터를 만드는 데 약 35분이 걸립니다.

## <a name="access-the-cluster-console"></a>클러스터 콘솔 에 액세스

Azure Red Hat OpenShift 4.3 클러스터 리소스에서 클러스터 콘솔 URL(양식)을 `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`찾을 수 있습니다. 다음 명령을 실행하여 리소스를 봅니다.

```console
az aro list -o table
```

사용자를 사용하여 클러스터에 로그인할 수 있습니다. `kubeadmin`  다음 명령을 실행하여 사용자의 암호를 `kubeadmin` 찾습니다.

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>클러스터 삭제

다음 명령을 실행하여 클러스터를 삭제합니다.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
