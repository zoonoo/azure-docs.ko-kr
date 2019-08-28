---
title: Azure 센티널 Preview에서 fusion 사용 | Microsoft Docs
description: Azure 센티널에서 fusion을 사용 하도록 설정 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780448"
---
# <a name="enable-fusion"></a>퓨전 사용

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 센티널의 Machine Learning는 처음부터 오른쪽에 기본 제공 됩니다. 신중 보안 분석가, 보안 데이터 과학자 및 엔지니어의 생산성을 높이기 위해 ML 혁신을 제공 하는 시스템을 설계 했습니다. 이러한 혁신 중 하나는 특히 경고 피로을 줄이기 위해 구축 된 Azure 센티널 Fusion입니다.

Fusion은 그래프 기반 기계 학습 알고리즘을 사용 하 여 Azure AD ID 보호 및 Microsoft Cloud App Security와 같은 다양 한 제품에서 수백만의 낮은 충실도 비정상 활동을 상호 연결 하 여 관리 가능한 수의 수로 결합 합니다. 흥미로운 보안 인시던트.

## <a name="enable-fusion"></a>퓨전 사용

1. Azure Portal에서 아이콘을 선택 하 여 Cloud Shell를 엽니다.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  아래에서 열리는 **Cloud Shell 창 시작** 에서 PowerShell을 선택 합니다.

3.  Azure 센티널을 배포한 구독을 선택 하 고 저장소를 **만듭니다**.

4. 인증 하 고 Azure 드라이브를 작성 한 후 명령 프롬프트에서 다음 명령을 실행 합니다.

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Fusion 사용 안 함

위와 동일한 절차를 수행 하 고 다음 명령을 실행 합니다.

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Fusion 상태 보기

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에서 Fusion을 사용 하도록 설정 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats.md)시작 합니다.

