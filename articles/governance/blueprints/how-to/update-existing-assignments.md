---
title: 기존 Azure Blueprint 할당을 업데이트하는 방법
description: Azure Blueprint에서 기존 할당을 업데이트하는 메커니즘을 알아봅니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956203"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>기존 청사진 할당을 업데이트하는 방법

청사진이 할당된 경우 할당을 업데이트할 수 있습니다. 다음을 포함하여 기존 할당을 업데이트하는 여러 가지 이유가 있습니다.

- [리소스 잠금](../concepts/resource-locking.md) 추가 또는 제거
- [동적 매개 변수](../concepts/parameters.md#dynamic-parameters) 값 변경
- 할당을 최근에 **게시된** 버전의 청사진으로 업그레이드

## <a name="updating-assignments"></a>할당 업데이트

1. **모든 서비스**를 클릭한 후 왼쪽 창에서 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Blueprints 서비스를 시작합니다. **정책** 페이지에서 **청사진**을 클릭합니다.

1. 왼쪽 페이지에서 **할당된 청사진**을 선택합니다.

1. 청사진 목록에서 청사진 할당을 마우스 왼쪽 단추로 클릭한 다음, **할당 업데이트** 단추를 클릭하거나 청사진 할당을 마우스 오른쪽 단추로 클릭하고 **할당 업데이트**를 선택합니다.

   ![할당 업데이트](../media/update-existing-assignments/update-assignment.png)

1. 원래 할당의 모든 값으로 미리 채워진 **청사진 할당** 페이지가 로드됩니다. **청사진 정의 버전**, **잠금 할당** 상태, 청사진 정의에 있는 동적 매개 변수를 변경합니다. 변경을 마쳤으면 **할당**을 클릭합니다.

1. 업데이트된 할당 세부 정보 페이지에서 새 상태를 확인합니다. 이 예제에서는 할당에 **잠금**을 추가했습니다.

   ![업데이트된 할당 - 잠김](../media/update-existing-assignments/updated-assignment.png)

1. 드롭다운을 사용하여 다른 **할당 작업**에 대한 세부 정보를 살펴봅니다. 선택한 할당 작업으로 업데이트된 **관리되는 리소스** 테이블.

   ![할당 작업](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>할당 업데이트에 대한 규칙

업데이트된 할당의 배포는 몇 가지 중요한 규칙을 따릅니다. 이러한 규칙은 요청되는 변경 내용과 배포 또는 업데이트되는 아티팩트 리소스 유형에 따라 기존 리소스의 처리 방법을 결정합니다.

- 역할 할당
  - 역할 또는 역할 담당자(사용자, 그룹 또는 앱)가 변경되면 새 역할 할당이 만들어집니다. 이전에 배포된 역할 할당은 그대로 남아 있습니다.
- 정책 할당
  - 정책 할당의 매개 변수가 변경되면 기존 할당이 업데이트됩니다.
  - 정책 할당의 정의가 변경되면 새 정책 할당이 만들어집니다. 이전에 배포된 정책 할당은 그대로 남아 있습니다.
  - 정책 할당 아티팩트가 청사진에서 제거되면 이전에 배포된 정책 할당을 그대로 남아 있습니다.
- Azure 리소스 관리자 템플릿
  - 템플릿은 Resource Manager를 통해 **PUT**으로 처리됩니다. 리소스 유형마다 이를 처리하는 방법이 다르므로, 포함된 각 리소스의 설명서를 검토하여 Blueprints에서 실행할 때 이 작업이 미치는 영향을 확인해야 합니다.

## <a name="possible-errors-on-updating-assignments"></a>할당 업데이트 시 발생 가능한 오류

할당을 업데이트할 때 변경한 내용이 실행 시 오류가 발생할 수 있습니다. 이미 배포된 리소스 그룹의 위치를 변경하는 경우가 이에 해당합니다. [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md)에서 지원되는 변경 작업을 수행할 수 있지만, Azure Resource Manager를 통해 오류로 이어지는 변경 작업은 할당 시에도 오류가 발생합니다.

할당을 업데이트할 수 있는 횟수에는 제한이 없습니다. 따라서 잘못된 매개 변수, 이미 존재하는 개체 또는 Azure Resource Manager에서 허용하지 않는 변경으로 인한 오류가 발생할 경우 오류를 확인하고 할당을 다른 방식으로 업데이트하세요.

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 사용하는 방법 확인
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결
