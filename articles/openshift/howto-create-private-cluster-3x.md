---
title: Azure Red Hat OpenShift 3.11을 사용하여 프라이빗 클러스터 만들기 | Microsoft Docs
description: Azure Red Hat OpenShift 3.11을 사용하여 프라이빗 클러스터 만들기
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, 프라이빗 클러스터, red hat
ms.openlocfilehash: f4ce6c79fa9fe6d05fdea4b877a8aa7faf404a9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83727451"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Azure Red Hat OpenShift 3.11을 사용하여 프라이빗 클러스터 만들기

프라이빗 클러스터는 다음과 같은 이점을 제공합니다.

* 프라이빗 클러스터는 공용 IP 주소에서 클러스터 컨트롤 플레인 구성 요소(예: API 서버)를 노출하지 않습니다.
* 프라이빗 클러스터의 가상 네트워크는 고객이 구성할 수 있으며, 이를 통해 ExpressRoute 환경을 비롯한 다른 가상 네트워크와 피어링할 수 있도록 네트워킹을 설정할 수 있습니다. 내부 서비스와 통합되도록 가상 네트워크에서 사용자 지정 DNS를 구성할 수도 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

다음 구성 코드 조각의 필드는 새로운 것이며 클러스터 구성에 포함되어야 합니다. `managementSubnetCidr`은 클러스터 가상 네트워크 내에 있어야 하며 Azure에서 클러스터를 관리하는 데 사용됩니다.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

아래에 제공된 샘플 스크립트를 사용하여 프라이빗 클러스터를 배포할 수 있습니다. 클러스터가 배포되면 `cluster get` 명령을 실행하고 `properties.FQDN` 속성을 보고 OpenShift API 서버의 개인 IP 주소를 확인합니다.

클러스터 가상 네트워크는 사용 권한을 가지고 생성되어 수정할 수 있습니다. 그런 다음, 필요에 따라 네트워킹을 설정하여 가상 네트워크(ExpressRoute, VPN, 가상 네트워크 피어링)에 액세스할 수 있습니다.

클러스터 가상 네트워크에서 DNS 이름 서버를 변경하는 경우 VM을 이미지로 다시 설치할 수 있도록 `true`로 설정된 `properties.RefreshCluster` 속성을 사용하여 클러스터에 대한 업데이트를 실행해야 합니다. 이 업데이트를 통해 새 이름 서버를 선택할 수 있습니다.

## <a name="sample-configuration-scripts"></a>샘플 구성 스크립트

이 섹션의 샘플 스크립트를 사용하여 프라이빗 클러스터를 설정하고 배포합니다.

### <a name="environment"></a>Environment

사용자 고유의 값을 사용하여 아래 환경 변수를 입력합니다.

> [!NOTE]
> 현재 프라이빗 클러스터에서 지원되는 유일한 위치이므로 위치를 `eastus2`로 설정해야 합니다.

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

### <a name="private-clusterjson"></a>private-cluster.json

위에서 정의한 환경 변수를 사용하여 프라이빗 클러스터를 사용하도록 설정된 샘플 클러스터 구성이 있습니다.

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

## <a name="deploy-a-private-cluster"></a>프라이빗 클러스터 배포

위의 샘플 스크립트를 사용하여 프라이빗 클러스터를 구성한 후 다음 명령을 실행하여 프라이빗 클러스터를 배포합니다.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>다음 단계

OpenShift 콘솔에 액세스하는 방법에 대한 자세한 내용은 [웹 콘솔 연습](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)을 참조하세요.
