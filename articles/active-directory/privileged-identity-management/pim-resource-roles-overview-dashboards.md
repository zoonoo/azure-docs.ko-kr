---
title: 리소스 대시보드를 사용하여 액세스 검토 수행 - Azure | Microsoft Docs
description: 리소스 대시보드를 사용하여 Azure AD PIM(Privileged Identity Management)에서 액세스 검토를 수행하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 20172cf7413397aedc4b3c32d0f1419531a2588a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188500"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>리소스 대시보드를 사용하여 액세스 검토 수행

리소스 대시보드를 사용하여 Azure 리소스에 대한 PIM(Privileged Identity Management)에서 액세스 검토를 수행할 수 있습니다. [관리자 보기] 대시보드에는 다음과 같은 세 가지 기본 구성 요소가 있습니다.

- 리소스 역할 활성화에 대한 그래픽 표현.
- 할당 유형에 따른 역할 할당의 분포를 표시하는 차트 2개.
- 새 역할 할당에 관련된 데이터 영역.

![그래프 및 차트를 보여주는 [관리자 보기] 대시보드의 스크린샷](media/azure-pim-resource-rbac/rbac-overview-top.png)

![데이터 목록을 보여주는 [관리자 보기] 대시보드의 스크린샷](media/azure-pim-resource-rbac/role-settings.png)

리소스 역할 활성화에 대한 그래픽 표현은 지난 7일 동안의 데이터를 다룹니다. 이 데이터에는 선택한 리소스에 대한 범위가 지정되어 있으며 가장 일반적인 역할(소유자, 기여자, 사용자 액세스 관리자) 및 결합된 모든 역할에 대한 활성화가 표시되어 있습니다.

활성화 그래프의 오른쪽에는 사용자 및 그룹 모두에 대한 할당 유형별 역할 할당 분포를 표시하는 두 개의 차트가 있습니다. 차트 조각을 선택하면 값이 백분율로 변경됩니다(또는 그 반대로).

차트 아래에는 지난 30일 동안 새 역할 할당이 있는 사용자 및 그룹의 수와 총 할당 수로 정렬된 역할 목록(내림차순)이 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할에 대한 액세스 검토 시작](pim-resource-roles-start-access-review.md) 
