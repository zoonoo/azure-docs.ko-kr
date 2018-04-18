---
title: Azure 리소스에 대해 Privileged Identity Management의 액세스 검토를 수행하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure 리소스에 대한 PIM의 액세스 검토를 수행하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: f1bcf114b997c3056016b84cafc28253ea1af28e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="resource-dashboards"></a>리소스 대시보드

[관리자 보기] 대시보드에는 4가지 기본 구성 요소가 있습니다. 지난 7일 동안의 리소스 역할 활성화에 대한 그래픽 표현입니다. 이 데이터에는 선택한 리소스에 대한 범위가 지정되어 있으며 가장 일반적인 역할(소유자, 참가자, 사용자 액세스 관리자) 및 결합된 모든 역할에 대한 활성화가 표시되어 있습니다.

활성화 그래프의 오른쪽에는 사용자 및 그룹 모두에 대한 할당 유형별 역할 할당 분포를 표시하는 두 개의 차트가 있습니다. 차트 조각을 선택하면 값이 백분율로 변경됩니다(또는 그 반대로).

![](media/azure-pim-resource-rbac/rbac-overview-top.png)

차트 아래에는 지난 30일 동안 새 역할 할당이 있는 사용자 및 그룹의 수(왼쪽)와 총 할당 수로 정렬된 역할 목록(내림차순)이 표시됩니다.

![](media/azure-pim-resource-rbac/role-settings.png)
