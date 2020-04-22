---
title: Azure 포털을 사용하여 직접 피어링에서 Azure 피어링 서비스 활성화
titleSuffix: Azure
description: Azure 포털을 사용하여 직접 피어링에서 Azure 피어링 서비스 활성화
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687038"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Azure 포털을 사용하여 직접 피어링에서 Azure 피어링 서비스 활성화

이 문서에서는 Azure 포털을 사용하여 직접 피어링에서 Azure [피어링 서비스를](overview-peering-service.md) 사용하도록 설정하는 방법에 대해 설명합니다.

원하는 경우 [PowerShell](howto-peering-service-powershell.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 조건을](prerequisites.md) 검토합니다.
* 피어링 서비스를 사용하도록 설정하려는 구독에서 직접 피어링을 선택합니다. 레거시 직접 피어링을 변환하거나 새 직접 피어링을 만들 수 있는 경우 다음을 수행합니다.
    * 레거시 직접 피어링을 변환하려면 [포털을 사용하여 레거시 직접 피어링](howto-legacy-direct-portal.md)변환의 지침을 Azure 리소스로 변환합니다.
    * 새 직접 피어링을 만들려면 [포털을 사용하여 직접 피어링 만들기 또는 수정의](howto-direct-portal.md)지침을 따릅니다.

## <a name="enable-peering-service-on-a-direct-peering"></a>직접 피어링에 Peering Service 사용 설정

### <a name="view-direct-peering"></a><a name= get></a>직접 피어링 보기
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>피어링 서비스를 위한 직접 피어링 사용

이전 단계에서 직접 피어링을 연 후 피어링 서비스에 대해 사용하도록 설정합니다.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>직접 피어링 연결 수정

연결 설정을 수정하려면 [포털을 사용하여 직접 피어링 만들기 또는 수정에서](howto-direct-portal.md)"직접 피어링 수정" 절을 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 Exchange 피어링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>추가 리소스

자주 묻는 질문은 [피어링 서비스 FAQ를](service-faqs.md)참조하십시오.