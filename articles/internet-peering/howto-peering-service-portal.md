---
title: Azure Portal를 사용 하 여 직접 피어 링에서 Azure 피어 링 서비스를 사용 하도록 설정
titleSuffix: Azure
description: Azure Portal를 사용 하 여 직접 피어 링에서 Azure 피어 링 서비스를 사용 하도록 설정
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700045"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 직접 피어 링에서 Azure 피어 링 서비스를 사용 하도록 설정

이 문서에서는 Azure Portal를 사용 하 여 직접 피어 링에서 Azure [피어 링 서비스](overview-peering-service.md) 를 사용 하도록 설정 하는 방법을 설명 합니다.

원하는 경우 [PowerShell](howto-peering-service-powershell.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작 하기 전에 [필수 구성 요소](prerequisites.md) 를 검토 합니다.
* 피어 링 서비스를 사용 하도록 설정 하려는 구독에서 직접 피어 링을 선택 합니다. 없는 경우 레거시 직접 피어 링을 변환 하거나 새 직접 피어 링을 만듭니다.
    * 레거시 직접 피어 링을 변환 하려면 [포털을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)의 지침을 따르세요.
    * 새 직접 피어 링을 만들려면 [포털을 사용 하 여 직접 피어 링 만들기 또는 수정](howto-direct-portal.md)의 지침을 따르세요.

## <a name="enable-peering-service-on-a-direct-peering"></a>직접 피어링에 Peering Service 사용 설정

### <a name="view-direct-peering"></a><a name= get></a>직접 피어 링 보기
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>피어 링 서비스에 대 한 직접 피어 링 사용

이전 단계에서 직접 피어 링을 연 후 피어 링 서비스에 대해 사용 하도록 설정 합니다.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>직접 피어 링 연결 수정

연결 설정을 수정 하려면 [포털을 사용 하 여 직접 피어 링 만들기 또는 수정](howto-direct-portal.md)의 "직접 피어 링 수정" 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 교환 피어링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용하여 레거시 교환 피어링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>추가 리소스

질문과 대답은 [피어 링 서비스 FAQ](service-faqs.md)를 참조 하세요.