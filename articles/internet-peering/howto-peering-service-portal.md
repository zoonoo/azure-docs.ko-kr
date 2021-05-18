---
title: Azure Portal을 통해 직접 피어링에 Azure Peering Service를 사용하도록 설정
titleSuffix: Azure
description: Azure Portal을 통해 직접 피어링에 Azure Peering Service를 사용하도록 설정
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700045"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Azure Portal을 통해 직접 피어링에 Azure Peering Service를 사용하도록 설정

이 문서에서는 Azure Portal을 통해 직접 피어링에 Azure [Peering Service](overview-peering-service.md)를 사용하도록 설정하는 방법을 설명합니다.

원하는 경우 [PowerShell](howto-peering-service-powershell.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [사전 요구 사항](prerequisites.md)을 검토합니다.
* 구독에서 Peering Service를 사용하도록 설정할 직접 피어링을 선택합니다. 직접 피어링이 없으면 레거시 직접 피어링을 변환하거나 새 직접 피어링을 만듭니다.
    * 레거시 직접 피어링을 변환하려면 [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)의 지침을 수행합니다.
    * 새 직접 피어링을 만들려면 [포털을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-portal.md)의 지침을 수행합니다.

## <a name="enable-peering-service-on-a-direct-peering"></a>직접 피어링에 Peering Service 사용 설정

### <a name="view-direct-peering"></a><a name= get></a>직접 피어링 보기
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>직접 피어링에 Peering Service를 사용하도록 설정

이전 단계에서 직접 피어링을 연 후 직접 피어링에 Peering Service를 사용하도록 설정합니다.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>직접 피어링 연결 수정

연결 설정을 수정하려면 [포털을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-portal.md)에서 “직접 피어링 수정” 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 교환 피어링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용하여 레거시 교환 피어링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>추가 리소스

질문과 대답은 [Peering Service FAQ](service-faqs.md)를 참조하세요.