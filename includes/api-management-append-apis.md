---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601912"
---
## <a name="append-other-apis"></a>다른 API 추가

다음을 포함하여 다양한 서비스에 의해 노출되는 API를 구성할 수 있습니다.
* OpenAPI 사양
* SOAP API
* Azure App Service의 API Apps 기능
* Azure 함수 앱
* Azure Logic Apps
* Azure Service Fabric

다음 단계를 사용하여 기존 API에 다른 API를 추가합니다. 

>[!NOTE] 
> 다른 API를 가져오면 작업이 현재 API에 추가됩니다.

1. Azure Portal에서 Azure API Management 인스턴스로 이동합니다.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Azure API Mgmt 인스턴스로 이동":::

1. 왼쪽 메뉴에서 **API** 를 선택합니다.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="API 선택":::

1. 다른 API를 추가하려는 API 옆에 있는 **...** 를 클릭합니다.
1. 드롭다운 메뉴에서 **가져오기** 를 선택합니다.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="가져오기 선택":::

1. API를 가져올 서비스를 선택합니다.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="서비스 선택":::