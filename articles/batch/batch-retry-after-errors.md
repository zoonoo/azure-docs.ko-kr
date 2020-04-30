---
title: 오류가 발생 한 후 작업 다시 시도
description: 오류를 확인 하 고 다시 시도 하세요.
ms.topic: article
ms.date: 02/15/2020
ms.custom: seodec18
ms.openlocfilehash: 8addc4418f268a2c27b730543bdb309ef45fd5f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116539"
---
# <a name="detecting-and-handling-batch-service-errors"></a>배치 서비스 오류 감지 및 처리

REST 서비스 API를 사용 하는 경우 오류를 확인 해야 합니다. 일괄 작업을 실행할 때 오류가 발생 하는 것은 일반적이 지 않습니다.

## <a name="common-errors"></a>일반 오류 

- 네트워킹 오류-일괄 처리에 도달 하지 않았거나 일괄 응답이 클라이언트에 도달 하지 못한 요청입니다.
- 내부 서버 오류-표준 5xx 상태 코드 HTTP 응답입니다.
- 제한을 사용 하면 429 또는 503 상태 코드 HTTP 응답과 같은 오류가 다시 시도 헤더와 함께 발생할 수 있습니다.
- 그룹이 이미 있습니다 및 InvalidOperation와 같은 오류를 포함 하는 4xx 오류 이는 리소스가 상태 전환에 대 한 올바른 상태가 아님을 의미 합니다.

다양 한 오류 코드 유형과 특정 오류 코드에 대 한 자세한 내용은 [일괄 처리 상태 및 오류 코드](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)를 참조 하세요.

## <a name="when-to-retry"></a>다시 시도 하는 경우

일괄 처리 Api는 오류가 발생 하는 경우 사용자에 게 알립니다. 모두 다시 시도할 수 있으며, 모두 해당 목적을 위한 전역 다시 시도 처리기를 포함 합니다. 이 기본 제공 메커니즘을 사용 하는 것이 가장 좋습니다.

오류가 발생 한 후 다시 시도 하기 전에 약간의 시간 (재시도 사이 몇 초)을 기다려야 합니다. 너무 자주 또는 너무 빨리 다시 시도 하는 경우 다시 시도 처리기가 제한 됩니다.

### <a name="for-more-information"></a>참조 항목  

API 참조 정보에 대 [한 Batch api 및 도구](batch-apis-tools.md) 링크 예를 들어 .NET API에는 필요한 재시도 정책을 지정 해야 하는 [RetryPolicyProvider 클래스가]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) 있습니다. 

각 API 및 해당 기본 재시도 정책에 대 한 자세한 내용은 [일괄 처리 상태 및 오류 코드](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)를 참조 하세요.
