---
title: 관리 Api와 서비스 Api의 차이점-Azure Batch | Microsoft Docs
description: Api는 Azure Batch 서비스의 다양 한 계층에서 작동 합니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672755"
---
# <a name="service-level-and-management-level-apis"></a>서비스 수준 및 관리 수준 Api

Azure Batch에는 두 가지 Api 집합이 있습니다. 하나는 서비스 수준에 해당 하 고 다른 하나는 관리 수준에 대 한 것입니다. 이름 지정은 유사 하지만 서로 다른 결과를 반환 합니다. 활동 로그를 원하는 경우 관리 Api를 사용 해야 합니다. 서비스 수준 Api는 Azure 리소스 관리 계층을 무시 하 고 기록 되지 않습니다.


Batch 관리 및 Batch 서비스에는 둘 다 풀 용 Api가 있습니다 (예:). 
- 풀을 삭제 하는이 API는 batch 계정에서 직접 대상입니다. https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- 풀 https://docs.microsoft.com/rest/api/batchmanagement/pool/delete을 삭제 하는이 API는 management.azure.com 계층을 대상으로 합니다.

