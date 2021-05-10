---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531642"
---
* 클라이언트가 API 버전 2021-01-01-preview 이상을 사용하는 경우 **표준 SKU [공용 IPv4 주소](../articles/virtual-network/public-ip-addresses.md#standard)** 입니다. 공용 IP 주소 리소스는 외부 또는 내부 액세스를 위해 가상 네트워크를 설정할 때 필요합니다. 내부 가상 네트워크를 사용하는 경우 공용 IP 주소는 관리 작업에만 사용됩니다. [API Management의 IP 주소](../articles/api-management/api-management-howto-ip-addresses.md)에 대해 자세히 알아보세요.

  * IP 주소는 API Management 인스턴스 및 가상 네트워크와 동일한 지역 및 구독에 있어야 합니다.

  * IP 주소의 값은 해당 지역에 API Management 인스턴스의 가상 공용 IPv4 주소로 할당됩니다. 

  * 외부 가상 네트워크에서 내부 가상 네트워크로 또는 그 반대로 변경하거나, 네트워크에서 서브넷을 변경하거나, API Management 인스턴스의 가용성 영역을 업데이트하는 경우 다른 공용 IP 주소를 구성해야 합니다. 

  > [!IMPORTANT]
  > 현재 Azure Portal은 API Management 인스턴스를 만들거나 업데이트할 때 API 버전 2021-01-01 미리 보기를 사용합니다. Azure Resource Manager 템플릿 또는 API Management REST API를 사용하여 이 API 버전을 지정할 수 있습니다. Azure CLI와 Azure PowerShell은 현재 API 버전 2020-12-01을 지원합니다.
