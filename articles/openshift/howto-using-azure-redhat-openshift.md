---
title: Azure Red Hat OpenShift 4.3 클러스터 만들기 | Microsoft Docs
description: Azure Red Hat OpenShift 3.11를 사용 하 여 클러스터 만들기
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, red hat, cli
ms.openlocfilehash: 3c336a1fbfb9f991ff824e8deafe84f3d899771d
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082832"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Azure Red Hat OpenShift 4.3 클러스터 만들기, 액세스 및 관리

> [!IMPORTANT]
> Azure Red Hat OpenShift 4.3은 현재 미국 동부에서 비공개 미리 보기 에서만 사용할 수 있습니다. 비공개 미리 보기 수락은 초대 전용입니다. 이 기능을 사용 하도록 설정 하기 전에 구독을 등록 해야 합니다. [Azure Red Hat OpenShift 비공개 미리 보기 등록](https://aka.ms/aro-preview-register)

> [!NOTE]
> 미리 보기 기능은 셀프 서비스 이며 제공 되는 그대로 제공 되며 서비스 수준 계약 (SLA) 및 제한 된 보증에서 제외 됩니다. 따라서이 기능은 프로덕션 용도로 사용 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Red Hat OpenShift 4.3 클러스터를 만들려면 다음이 필요 합니다.

- Azure CLI 버전 2.0.72 이상
  
- ' Az aro ' 확장명입니다.

- 네트워크 보안 그룹이 연결 되지 않은 두 개의 빈 서브넷을 포함 하는 가상 네트워크입니다.  클러스터가 이러한 서브넷에 배포 됩니다.

- AAD 응용 프로그램 및 서비스 주체를 자동으로 만드는 `az aro create`에 대 한 클러스터 AAD 응용 프로그램 (클라이언트 ID 및 암호) 및 서비스 주체 또는 충분 한 AAD 권한입니다.

- RP 서비스 사용자 및 클러스터 서비스 주체는 클러스터 가상 네트워크에 대 한 참가자 역할을 보유 해야 합니다.  가상 네트워크에서 "사용자 액세스 관리자" 역할을 사용 하는 경우 `az aro create`는 역할 할당을 자동으로 설정 합니다.

### <a name="install-the-az-aro-extension"></a>' Az aro ' 확장을 설치 합니다.
`az aro` 확장을 사용 하면 Azure CLI를 사용 하 여 명령줄에서 직접 Azure Red Hat OpenShift 클러스터를 만들고, 액세스 하 고, 삭제할 수 있습니다.

> [!Note] 
> `az aro` 확장은 미리 보기로 (이므로 됩니다. 이후 릴리스에서 변경 되거나 제거 될 수 있습니다.
> `az aro` 확장 미리 보기를 옵트인 (opt in) 하려면 `Microsoft.RedHatOpenShift` 리소스 공급자를 등록 해야 합니다.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Azure에 로그인합니다.

   ```console
   az login
   ```

2. 다음 명령을 실행 하 여 `az aro` 확장을 설치 합니다.

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. ARO 확장이 등록 되어 있는지 확인 합니다.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>두 개의 빈 서브넷을 포함 하는 가상 네트워크 만들기

두 개의 빈 서브넷이 포함 된 가상 네트워크를 만들려면 다음 단계를 수행 합니다.

1. 다음 변수를 설정합니다.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. 클러스터에 대 한 리소스 그룹을 만듭니다.

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

4. 가상 네트워크에 두 개의 빈 서브넷을 추가 합니다.

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

5. 가상 네트워크 및 서브넷의 개인 링크 서비스에 대 한 네트워크 정책을 사용 하지 않도록 설정 합니다. 이는 ARO 서비스가 클러스터에 액세스 하 고 관리 하는 데 필요한 요구 사항입니다.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>클러스터 만들기

다음 명령을 실행 하 여 클러스터를 만듭니다.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> 클러스터를 만드는 데 일반적으로 약 35 분이 소요 됩니다.

## <a name="access-the-cluster-console"></a>클러스터 콘솔 액세스

Azure Red Hat OpenShift 4.3 클러스터 리소스에서 클러스터 콘솔 URL (`https://console-openshift-console.apps.<random>.<location>.aroapp.io/`양식)을 찾을 수 있습니다. 다음 명령을 실행 하 여 리소스를 봅니다.

```console
az aro list -o table
```

`kubeadmin` 사용자를 사용 하 여 클러스터에 로그인 할 수 있습니다.  다음 명령을 실행 하 여 `kubeadmin` 사용자에 대 한 암호를 찾습니다.

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>클러스터 삭제

다음 명령을 실행 하 여 클러스터를 삭제 합니다.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```