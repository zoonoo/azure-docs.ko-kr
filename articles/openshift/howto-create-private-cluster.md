---
title: Azure Red Hat OpenShift 3.11를 사용 하 여 개인 클러스터 만들기 Microsoft Docs
description: Azure Red Hat OpenShift 3.11를 사용 하 여 개인 클러스터 만들기
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, 개인 클러스터, red hat
ms.openlocfilehash: f4ce6c79fa9fe6d05fdea4b877a8aa7faf404a9b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204171"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Azure Red Hat OpenShift 3.11를 사용 하 여 개인 클러스터 만들기

개인 클러스터는 다음과 같은 이점을 제공 합니다.

* 개인 클러스터는 공용 IP 주소에서 클러스터 제어 평면 구성 요소 (예: API 서버)를 노출 하지 않습니다.
* 개인 클러스터의 가상 네트워크는 고객이 구성할 수 있으며,이를 통해 네트워킹을 설정 하 여 Express 경로 환경을 비롯 한 다른 가상 네트워크와 피어 링을 허용할 수 있습니다. 내부 서비스와 통합 하도록 가상 네트워크에서 사용자 지정 DNS를 구성할 수도 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

다음 구성 코드 조각의 필드는 새로운 것 이며 클러스터 구성에 포함 되어야 합니다. `managementSubnetCidr`클러스터 가상 네트워크 내에 있어야 하며 Azure에서 클러스터를 관리 하는 데 사용 됩니다.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

개인 클러스터는 아래에 제공 된 샘플 스크립트를 사용 하 여 배포할 수 있습니다. 클러스터가 배포 되 면 `cluster get` 명령을 실행 하 고 `properties.FQDN` 속성을 확인 하 여 openshift API 서버의 개인 IP 주소를 확인 합니다.

클러스터 가상 네트워크는 사용 권한으로 생성 되어 수정할 수 있습니다. 그런 다음 필요에 따라 네트워킹을 설정 하 여 가상 네트워크 (Express 경로, VPN, 가상 네트워크 피어 링)에 액세스할 수 있습니다.

클러스터 가상 네트워크에서 DNS 이름 서버를 변경 하는 경우 Vm을 이미지로 다시 설치 수 있도록 `properties.RefreshCluster` 속성이로 `true` 설정 된 클러스터에서 업데이트를 실행 해야 합니다. 이 업데이트를 통해 새 이름 서버을 선택할 수 있습니다.

## <a name="sample-configuration-scripts"></a>샘플 구성 스크립트

이 섹션의 샘플 스크립트를 사용 하 여 개인 클러스터를 설정 하 고 배포 합니다.

### <a name="environment"></a>환경

사용자 고유의 값을 사용 하 여 아래 환경 변수를 입력 합니다.

> [!NOTE]
> 이 위치는 현재 개인 클러스터 `eastus2` 에 대해 지원 되는 유일한 위치 이므로로 설정 되어야 합니다.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster. json

위에서 정의한 환경 변수를 사용 하 여 다음은 개인 클러스터가 사용 하도록 설정 된 샘플 클러스터 구성입니다.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>개인 클러스터 배포

위의 샘플 스크립트를 사용 하 여 개인 클러스터를 구성한 후 다음 명령을 실행 하 여 개인 클러스터를 배포 합니다.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>다음 단계

OpenShift 콘솔에 액세스 하는 방법에 대 한 자세한 내용은 [웹 콘솔 연습](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)을 참조 하세요.
