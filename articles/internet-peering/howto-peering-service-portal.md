---
title: 포털을 사용 하 여 직접 피어 링에서 피어 링 서비스 사용
titleSuffix: Azure
description: 포털을 사용 하 여 직접 피어 링에서 피어 링 서비스 사용
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774979"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>포털을 사용 하 여 직접 피어 링에서 피어 링 서비스 사용

이 문서에서는 포털을 사용 하 여 직접 피어 링에서 [피어 링 서비스](overview-peering-service.md) 를 사용 하도록 설정 하는 방법을 설명 합니다.

원하는 경우 [PowerShell](howto-peering-service-powershell.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작 하기 전에 [필수 구성 요소](prerequisites.md) 를 검토 하세요.
* 구독에서 피어 링 서비스를 사용 하도록 설정 하려는 직접 피어 링을 선택 합니다. 없는 경우 레거시 직접 피어 링을 변환 하거나 새 직접 피어 링을 만듭니다.
    * 레거시 직접 피어 링을 변환 하려면 [포털을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)의 지침을 따르세요.
    * 새 직접 피어 링을 만들려면 [포털을 사용 하 여 직접 피어 링 만들기 또는 수정](howto-direct-portal.md)의 지침을 따르세요.

## <a name="enable-peering-service-on-a-direct-peering"></a>직접 피어 링에서 피어 링 서비스 사용

### <a name= get></a>직접 피어 링 보기
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>피어 링 서비스에 대 한 직접 피어 링 사용

이전 단계에서 직접 피어 링을 연 후 피어 링 서비스에 대해 사용 하도록 설정 합니다.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>직접 피어 링 연결 수정

연결 설정을 수정 해야 하는 경우 [포털을 사용 하 여 직접 피어 링 만들기 또는 수정](howto-direct-portal.md) 에서 **직접 피어 링 수정** 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [포털을 사용 하 여 Exchange 피어 링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>추가 리소스

질문과 대답은 [피어 링 서비스 FAQ](service-faqs.md)를 참조 하세요.