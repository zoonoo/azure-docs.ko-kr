---
title: PIM - Azure AD에서 액세스 검토를 위한 리소스 대시보드 | 마이크로 소프트 문서
description: 리소스 대시보드를 사용하여 Azure AD PIM(Privileged Identity Management)에서 액세스 검토를 수행하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847023"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>리소스 대시보드를 사용하여 권한 있는 ID 관리에서 액세스 검토를 수행합니다.

리소스 대시보드를 사용하여 PIM(권한 있는 ID 관리)에서 액세스 검토를 수행할 수 있습니다. Azure Active Directory(Azure AD)의 관리자 보기 대시보드에는 세 가지 기본 구성 요소가 있습니다.

- 리소스 역할 활성화의 그래픽 표현
- 할당 유형별로 역할 할당 분포를 표시하는 차트
- 새 역할 할당에 대한 정보가 포함된 데이터 영역

![그래프 및 차트를 보여주는 [관리자 보기] 대시보드의 스크린샷](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![데이터 목록을 보여주는 [관리자 보기] 대시보드의 스크린샷](media/pim-resource-roles-overview-dashboards/role-settings.png)

리소스 역할 활성화에 대한 그래픽 표현은 지난 7일 동안의 데이터를 다룹니다. 이 데이터에는 선택한 리소스에 대한 범위가 지정되어 있으며 가장 일반적인 역할(소유자, 기여자, 사용자 액세스 관리자) 및 결합된 모든 역할에 대한 활성화가 표시되어 있습니다.

활성화 그래프의 한쪽에는 두 차트가 할당 유형별로 사용자 및 그룹 모두에 대한 역할 할당 분포를 표시합니다. 차트 조각을 선택하면 값이 백분율로 변경됩니다(또는 그 반대로).

차트 아래에는 지난 30일 동안 새 역할 할당이 있는 사용자 및 그룹 수와 내림차순으로 총 할당별로 정렬된 역할이 나열됩니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할에 대한 액세스 검토를 시작합니다.](pim-resource-roles-start-access-review.md)
