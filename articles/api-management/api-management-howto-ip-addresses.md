---
title: Azure API 관리 서비스의 IP 주소 | 마이크로 소프트 문서
description: Azure API 관리 서비스의 IP 주소를 검색하는 방법과 변경 시기를 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047740"
---
# <a name="ip-addresses-of-azure-api-management"></a>Azure API 관리의 IP 주소

이 문서에서는 Azure API 관리 서비스의 IP 주소를 검색하는 방법을 설명합니다. IP 주소는 서비스가 가상 네트워크에 있는 경우 공개 또는 비공개일 수 있습니다.

IP 주소를 사용하여 방화벽 규칙을 만들거나, 백 엔드 서비스로 들어오는 트래픽을 필터링하거나, 아웃바운드 트래픽을 제한할 수 있습니다.

## <a name="ip-addresses-of-api-management-service"></a>API 관리 서비스의 IP 주소

개발자, 기본, 표준 또는 프리미엄 계층의 모든 API 관리 서비스 인스턴스에는 해당 서비스 인스턴스에만 해당 공용 IP 주소가 있습니다(다른 리소스와 공유되지 않음). 

Azure 포털에서 리소스의 개요 대시보드에서 IP 주소를 검색할 수 있습니다.

![API 관리 IP 주소](media/api-management-howto-ip-addresses/public-ip.png)

다음 API 호출을 통해 프로그래밍 방식으로 가져올 수도 있습니다.

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

공용 IP 주소는 응답의 일부가 됩니다.

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

[다중 지역 배포에서](api-management-howto-deploy-multi-region.md)각 지역 배포에는 하나의 공용 IP 주소가 있습니다.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>VNet에서 API 관리 서비스의 IP 주소

API 관리 서비스가 가상 네트워크 내에 있는 경우 공용 및 비공개의 두 가지 유형의 IP 주소가 있습니다.

공용 IP 주소는 Azure 리소스 `3443` 관리자를 통해 구성을 관리하기 위해 포트의 내부 통신에 사용됩니다. 외부 VNet 구성에서는 런타임 API 트래픽에도 사용됩니다. API 관리에서 공용(인터넷 을 향한) 백 엔드로 요청이 전송되면 공용 IP 주소가 요청의 출처로 표시됩니다.

[내부 VNet 모드에서만](api-management-using-with-internal-vnet.md)사용할 **only** 수 있는 개인 가상 IP(VIP) 주소는 네트워크 내에서 직접 API 액세스를 위한 게이트웨이, 개발자 포털 및 관리 평면과 같은 API 관리 끝점에 연결하는 데 사용됩니다. 네트워크 내에서 DNS 레코드를 설정하는 데 사용할 수 있습니다.

Azure 포털 및 API 호출의 응답에서 두 유형의 주소를 볼 수 있습니다.

![VNet IP 주소의 API 관리](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API 관리는 VNet 외부의 연결에 공용 IP 주소를 사용하고 VNet 내의 연결에는 개인 IP 주소를 사용합니다.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>소비 계층 API 관리 서비스의 IP 주소

API 관리 서비스가 소비 계층 서비스인 경우 전용 IP 주소가 없습니다. 소비 계층 서비스는 결정적인 IP 주소 없이 공유 인프라에서 실행됩니다. 

트래픽 제한을 위해 Azure 데이터 센터의 IP 주소 범위를 사용할 수 있습니다. 정확한 [단계는 Azure Functions 설명서 문서를](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) 참조하십시오.

## <a name="changes-to-the-ip-addresses"></a>IP 주소 변경 사항

API 관리의 개발자, 기본, 표준 및 프리미엄 계층에서 공용 IP 주소(VIP)는 서비스 수명 동안 정적이며 다음과 같은 예외는 다음과 같습니다.

* 서비스가 삭제된 다음 다시 생성되었습니다.
* 서비스 구독이 [일시 중단](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)되거나 [경고](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)된 다음(예: 미지불) 복원되었습니다.
* Azure 가상 네트워크가 서비스에 추가되거나 서비스에서 제거됩니다.
* API 관리 서비스는 외부 및 내부 VNet 배포 모드 간에 전환됩니다.

[다중 지역 배포에서](api-management-howto-deploy-multi-region.md)지역 IP 주소는 지역이 비어 있고 복원된 경우 변경됩니다.
