---
title: Azure API Management에서 외부 캐시 사용 | Microsoft Docs
description: Azure API Management에서 외부 캐시를 구성하고 사용하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: 2e8863eed774884a99de8643c9e497378368d166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072498"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Azure API Management에서 외부 Azure Cache for Redis 사용

Azure API Management는 기본 제공 캐시를 활용하는 것 외에도 외부 Azure Cache for Redis에서 응답을 캐시할 수 있습니다.

외부 캐시를 사용하면 기본 제공 캐시의 몇 가지 제한 사항을 극복할 수 있습니다. 특히 유용한 경우는 다음과 같습니다.

* API Management 업데이트 중에 캐시를 주기적으로 지우지 않도록 방지
* 캐시 구성에 대한 더 자세한 제어
* API Management 계층보다 더 많은 데이터 캐시 허용
* API Management의 소비 계층에서 캐싱 사용

캐싱에 대한 자세한 내용은 [API Management 캐싱 정책](api-management-caching-policies.md)과 [Azure API Management의 사용자 지정 캐싱](api-management-sample-cache-by-key.md)을 참조하세요.

![APIM에 사용자 고유의 캐시 가져오기](media/api-management-howto-cache-external/overview.png)

학습할 내용:

> [!div class="checklist"]
> * API Management에서 외부 캐시 추가

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

+ [Azure API 관리 인스턴스 만들기](get-started-create-service-instance.md)
+ [Azure API Management에서 캐싱](api-management-howto-cache.md)에 대한 이해

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Redis에 대한 Azure 캐시 만들기

이 섹션에서는 Azure에서 Azure Cache for Redis를 만드는 방법을 설명합니다. Azure 내부 또는 외부에 Azure Cache for Redis가 이미 있으면 다음 섹션으로 <a href="#add-external-cache">건너뛸</a> 수 있습니다.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>외부 캐시 추가

다음 단계에 따라 Azure API Management에서 외부 Azure Cache for Redis를 추가합니다.

![APIM에 사용자 고유의 캐시 가져오기](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> **사용 설정은** API 관리의 다중 지역 구성의 경우 구성된 캐시와 통신할 API 관리 지역 배포를 지정합니다. **기본값**으로 지정된 캐시는 지역 값이 있는 캐시로 재정의됩니다.
>
> 예를 들어 API Management가 미국 동부, 동남 아시아 및 서유럽 지역에서 호스팅되고 2개의 캐시(**기본값** 및 **동남 아시아**에 대해 각각 하나씩)가 구성되어 있는 경우, **동남 아시아**에 대한 API Management는 자체 캐시를 사용하지만 다른 두 지역은 **기본값** 캐시 항목을 사용합니다.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>같은 구독에서 Azure Cache for Redis 추가

1. Azure Portal에서 API Management 인스턴스로 이동합니다.
2. 왼쪽 메뉴에서 **외부 캐시** 탭을 선택합니다.
3. **+추가** 단추를 클릭합니다.
4. **캐시 인스턴스** 드롭다운 필드에서 캐시를 선택합니다.
5. 드롭다운 **에서 사용** 필드에서 **기본값을** 선택하거나 원하는 영역을 지정합니다.
6. **저장**을 클릭합니다.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>현재 Azure 구독 또는 Azure(일반적) 외부에서 호스트되는 Azure Cache for Redis 추가

1. Azure Portal에서 API Management 인스턴스로 이동합니다.
2. 왼쪽 메뉴에서 **외부 캐시** 탭을 선택합니다.
3. **+추가** 단추를 클릭합니다.
4. **캐시 인스턴스** 드롭다운 필드에서 **사용자 지정**을 선택합니다.
5. 드롭다운 **에서 사용** 필드에서 **기본값을** 선택하거나 원하는 영역을 지정합니다.
6. **연결 문자열** 필드에서 Azure Cache for Redis 연결 문자열을 제공합니다.
7. **저장**을 클릭합니다.

## <a name="use-the-external-cache"></a>외부 캐시 사용

Azure API Management에서 외부 캐시가 구성되면 캐싱 정책을 통해 사용할 수 있습니다. 자세한 단계는 [Azure API Management에서 캐싱을 추가하여 성능 향상](api-management-howto-cache.md)을 참조하세요.

## <a name="next-steps"></a><a name="next-steps"> </a>다음 단계

* 캐싱 정책에 대한 자세한 내용은 [API Management 정책 참조][API Management policy reference]의 [캐싱 정책][Caching policies]을 참조하세요.
* 정책 식을 사용하여 키별 캐싱 항목에 대한 자세한 내용은 [Azure API Management에서 사용자 지정 캐싱](api-management-sample-cache-by-key.md)을 참조하세요.

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
