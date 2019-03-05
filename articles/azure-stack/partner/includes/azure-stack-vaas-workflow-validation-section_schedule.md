---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: e9d59fadb9cbfeb7463f799bdab2e6da49208fe9
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57343496"
---
유효성 검사 워크플로에서 **예약** 워크플로 만드는 동안 지정 된 워크플로 수준 일반적인 매개 변수를 사용 하는 테스트 (참조 [서비스로AzureStack유효성검사에대한워크플로일반매개변수](../azure-stack-vaas-parameters.md)). 테스트 매개 변수 값에 유효 하지 않게 됩니다, 경우 있습니다 해야 재충전할 하에 설명 된 대로 [워크플로 매개 변수를 수정](../azure-stack-vaas-monitor-test.md#change-workflow-parameters)합니다.

> [!NOTE]
> 기존 인스턴스에 대해 유효성 검사 테스트를 예약 이전 인스턴스 대신 새 인스턴스를 포털에서 만들어집니다. 이전 인스턴스에 대 한 로그는 보존할 되지만 포털에서 액세스할 수 없습니다.  
테스트를 성공적으로 완료 되 면 합니다 **일정** 작업 비활성화 합니다.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. 선택 **일정** 테스트 인스턴스 예약에 대 한 프롬프트를 열려면 상황에 맞는 메뉴에서입니다.

1. 테스트 매개 변수를 검토 하 고 선택한 **제출** 실행에 대 한 테스트를 예약 합니다.