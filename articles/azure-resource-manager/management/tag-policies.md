---
title: 리소스 태그 지정 정책
description: 태그 준수를 보장하기 위해 할당할 수 있는 Azure 정책에 대해 설명합니다.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147019"
---
# <a name="assign-policies-for-tag-compliance"></a>태그 준수에 대한 정책 할당

Azure [정책을](../../governance/policy/overview.md) 사용하여 태그 지정 규칙 및 규칙을 적용합니다. 정책을 만들면 조직에 예상되는 태그가 없는 리소스가 구독에 배포되는 시나리오를 피할 수 있습니다. 태그를 수동으로 적용하거나 호환되지 않는 리소스를 검색하는 대신 배포 하는 동안 필요한 태그를 자동으로 적용 하는 정책을 만듭니다. 이제 새 [수정](../../governance/policy/concepts/effects.md#modify) 효과 및 [업데이트 관리 작업을](../../governance/policy/how-to/remediate-resources.md)사용하여 기존 리소스에 태그를 적용할 수도 있습니다. 다음 섹션에서 태그에 대한 예제 정책을 보여줍니다.

## <a name="policies"></a>정책

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>다음 단계

* 리소스에 태그를 지정하는 방법에 대해 알아보려면 [태그를 사용하여 Azure 리소스 구성](tag-resources.md)을 참조하세요.
* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
