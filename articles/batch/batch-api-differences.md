---
title: 관리 API와 서비스 API 간의 차이점 - Azure 배치 | 마이크로 소프트 문서
description: API는 Azure Batch 서비스의 여러 계층에서 작동합니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672755"
---
# <a name="service-level-and-management-level-apis"></a>서비스 수준 및 관리 수준 API

Azure Batch에는 서비스 수준에 대한 API 집합과 관리 수준에 대한 API 집합이 두 개 있습니다. 이름은 종종 비슷하지만 다른 결과를 반환합니다. 활동 로그를 원하는 경우 관리 API를 사용해야 합니다. 서비스 수준 API는 Azure 리소스 관리 계층을 우회하며 기록되지 않습니다.


예를 들어 일괄 처리 관리 및 일괄 처리 서비스에는 풀용 API가 있습니다. 
- 풀을 삭제하는 이 API는 일괄 처리 계정에 직접 대상으로 합니다.https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- 풀을 https://docs.microsoft.com/rest/api/batchmanagement/pool/delete 삭제하는 이 API는 management.azure.com 계층을 대상으로 합니다.

