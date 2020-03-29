---
title: 오류 후 작업 재시도
description: 오류를 확인하고 다시 시도합니다.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919994"
---
# <a name="detecting-and-handling-batch-service-errors"></a>배치 서비스 오류 감지 및 처리

REST 서비스 API로 작업할 때 오류를 확인하는 것이 중요합니다. 일괄 처리 작업을 실행할 때 오류가 발생하는 경우는 드물지 않습니다.

## <a name="common-errors"></a>일반 오류 

- 네트워킹 실패 - 일괄 처리에 도달하지 못했거나 Batch 응답이 클라이언트에 제 시간에 도달하지 않은 요청입니다.
- 내부 서버 오류 - 표준 5xx 상태 코드 HTTP 응답입니다.
- 제한은 다시 시도 후 헤더를 사용 하 여 429 또는 503 상태 코드 HTTP 응답 과 같은 오류를 일으킬 수 있습니다.
- 포함 하는 4xx 오류 와 같은 오류 이미존재 및 잘못 된 작업. 즉, 리소스가 상태 전환에 대한 올바른 상태가 아닙니다.

다양한 유형의 오류 코드 및 특정 오류 코드에 대한 자세한 내용은 [일괄 처리 상태 및 오류 코드를](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)참조하십시오.

## <a name="when-to-retry"></a>다시 시도할 때

일괄 처리 API는 오류가 있는 경우 알려줍니다. 모두 다시 시도할 수 있으며 모두 해당 목적을 위한 전역 재시도 처리기를 포함합니다. 이 기본 제공 메커니즘을 사용하는 것이 가장 좋습니다.

오류가 발생한 후 다시 시도하기 전에 약간의(재시도 사이에 몇 초)를 기다려야 합니다. 너무 자주 또는 너무 빨리 다시 시도하면 재시도 처리기가 제한됩니다.

### <a name="for-more-information"></a>참조 항목  

[일괄 처리 API 및 도구는](batch-apis-tools.md) API 참조 정보에 연결됩니다. 예를 들어 .NET API에는 필요한 재시도 정책을 지정해야 하는 [다시 시도PolicyProvider 클래스가]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) 있습니다. 

각 API 및 기본 재시도 정책에 대한 자세한 내용은 [일괄 처리 상태 및 오류 코드를](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)참조하십시오.
