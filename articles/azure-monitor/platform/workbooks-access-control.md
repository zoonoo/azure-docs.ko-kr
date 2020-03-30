---
title: Azure 모니터 통합 문서 액세스 제어
description: 역할 기반 액세스 제어를 통해 미리 빌드되고 사용자 지정 매개 화된 통합 문서로 복잡한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658424"
---
# <a name="access-control"></a>Access Control

통합 문서의 액세스 제어는 다음 두 가지를 나타냅니다.

* 통합 문서의 데이터를 읽는 데 필요한 액세스입니다. 이 액세스는 통합 문서에 사용된 리소스의 표준 [Azure 역할에](https://docs.microsoft.com/azure/role-based-access-control/overview) 의해 제어됩니다. 통합 문서는 이러한 리소스에 대한 액세스를 지정하거나 구성하지 않습니다. 사용자는 일반적으로 해당 리소스에 대한 [모니터링 판독기](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) 역할을 사용하여 해당 리소스에 대해 이 액세스 권한을 얻습니다.

* 통합 문서를 저장하는 데 필요한 액세스

    - 개인 `("My")` 통합 문서를 저장하면 추가 권한이 필요하지 않습니다. 모든 사용자는 개인 통합 문서를 저장할 수 있으며 사용자만 해당 통합 문서를 볼 수 있습니다.
    - 공유 통합 문서를 저장하려면 통합 문서를 저장하려면 리소스 그룹에 쓰기 권한이 필요합니다. 이러한 권한은 일반적으로 [모니터링 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) 역할에 의해 지정되지만 *통합 문서 기여자* 역할을 통해 설정할 수도 있습니다.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>통합 문서 관련 권한이 있는 표준 역할

[모니터링 리더에는](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) 리소스에서 데이터를 읽기 위해 도구(통합 문서 포함)를 모니터링하는 데 사용되는 표준 /읽기 권한이 포함됩니다.

[모니터링 기여자는](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) 항목을 저장하기 위해 다양한 모니터링 도구에서 사용하는 일반 `/write` 권한(공유 통합 문서 저장 권한 포함)을 `workbooks/write` 포함합니다.
"통합 문서 기여자"는 공유 통합 문서를 저장하는 개체에 "통합 문서/쓰기" 권한을 추가합니다.
사용자가 볼 수 있는 개인 통합 문서를 저장하기 위해 특별한 권한이 필요하지 않습니다.

사용자 지정 역할 기반 액세스 제어의 경우:

공유 `microsoft.insights/workbooks/write` 통합 문서를 저장하기 위해 추가합니다. 자세한 내용은 통합 [문서 기고자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) 역할을 참조하십시오.

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
