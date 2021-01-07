---
title: PIM의 액세스 검토에 대 한 리소스 대시보드-Azure AD | Microsoft Docs
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
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e95eaa5b0e86a7470fc48edc23b2dbfb47e4b10c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743731"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>리소스 대시보드를 사용 하 여 Privileged Identity Management에서 액세스 검토 수행

리소스 대시보드를 사용 하 여 PIM (Privileged Identity Management)에서 액세스 검토를 수행할 수 있습니다. Azure Active Directory (Azure AD)의 관리 보기 대시보드에는 다음과 같은 세 가지 주요 구성 요소가 있습니다.

- 리소스 역할 활성화의 그래픽 표현
- 할당 유형별로 역할 할당의 분포를 표시 하는 차트
- 새 역할 할당에 대 한 정보를 포함 하는 데이터 영역

![그래프 및 차트를 보여주는 [관리자 보기] 대시보드의 스크린샷](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![데이터 목록을 보여주는 [관리자 보기] 대시보드의 스크린샷](media/pim-resource-roles-overview-dashboards/role-settings.png)

리소스 역할 활성화에 대한 그래픽 표현은 지난 7일 동안의 데이터를 다룹니다. 이 데이터에는 선택한 리소스에 대한 범위가 지정되어 있으며 가장 일반적인 역할(소유자, 기여자, 사용자 액세스 관리자) 및 결합된 모든 역할에 대한 활성화가 표시되어 있습니다.

활성화 그래프의 한쪽에서 두 차트는 사용자와 그룹 모두에 대해 할당 유형별로 역할 할당의 분포를 표시 합니다. 차트 조각을 선택하면 값이 백분율로 변경됩니다(또는 그 반대로).

차트 아래에는 지난 30 일 동안 새 역할 할당이 있는 사용자 및 그룹의 수와 총 할당을 기준으로 내림차순으로 정렬 된 역할이 나열 됩니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할에 대 한 액세스 검토를 시작 합니다.](pim-resource-roles-start-access-review.md)
