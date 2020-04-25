---
title: 리소스 태그 지정에 대 한 정책
description: 태그 준수를 보장 하기 위해 할당할 수 있는 Azure 정책에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147863"
---
# <a name="assign-policies-for-tag-compliance"></a>태그 준수에 대 한 정책 할당

[Azure Policy](../../governance/policy/overview.md) 를 사용 하 여 태그 지정 규칙 및 규칙을 적용 합니다. 정책을 만들면 조직에 필요한 태그가 없는 구독에 리소스를 배포 하는 시나리오를 방지할 수 있습니다. 수동으로 태그를 적용 하거나 규정을 준수 하지 않는 리소스를 검색 하는 대신 배포 중 필요한 태그를 자동으로 적용 하는 정책을 만듭니다. 이제 새 수정 효과 및 [수정](../../governance/policy/concepts/effects.md#modify) [작업](../../governance/policy/how-to/remediate-resources.md)을 사용 하 여 기존 리소스에 태그를 적용할 수도 있습니다. 다음 섹션에서 태그에 대한 예제 정책을 보여줍니다.

## <a name="policies"></a>정책

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>다음 단계

* 리소스에 태그를 지정하는 방법에 대해 알아보려면 [태그를 사용하여 Azure 리소스 구성](tag-resources.md)을 참조하세요.
* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
