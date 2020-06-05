---
title: 관리 API와 서비스 API 간의 차이점
description: API는 Azure Batch 서비스의 다양한 계층에서 작동합니다.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867591"
---
# <a name="service-level-and-management-level-apis"></a>서비스 수준 및 관리 수준 API

Azure Batch에는 서비스 수준과 관리 수준을 위한 두 가지 API 세트가 있습니다. 이름 지정은 유사하지만 서로 다른 결과를 반환하는 경우가 있습니다. 활동 로그를 원하는 경우 관리 API를 사용해야 합니다. 서비스 수준 API는 Azure Resource Management 계층을 무시하고 기록되지 않습니다.


예를 들어 Batch 관리 및 Batch 서비스 둘 다에는 풀용 API가 있습니다. 
- 풀을 삭제하기 위한 API는 batch 계정(https://docs.microsoft.com/rest/api/batchservice/pool/delete )을 직접 대상으로 합니다. 

- 풀 https://docs.microsoft.com/rest/api/batchmanagement/pool/delete 를 삭제하기 위한 이 API는 management.azure.com 계층을 대상으로 합니다.

