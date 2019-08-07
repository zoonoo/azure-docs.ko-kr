---
title: Azure Security Center의 권장 사항 수정 | Microsoft Docs
description: 이 문서에서는 Azure 리소스를 보호 하 고 보안 정책을 준수 하는 데 도움이 되는 Azure Security Center의 권장 사항을 수정 하는 방법을 설명 합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778990"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center의 권장 사항 수정

권장 사항은 리소스의 보안을 강화 하는 방법에 대 한 제안을 제공 합니다.  권장 구성에서 제공 하는 수정 단계를 수행 하 여 권장 사항을 구현할 수 있습니다. 

## 수정 단계<a name="remediation-steps"></a>

모든 권장 사항을 검토 한 후 먼저 수정할 항목을 결정 합니다. [보안 점수 효과](security-center-recommendations.md#monitor-recommendations) 를 사용 하 여 먼저 수행할 작업의 우선 순위를 지정 하는 것이 좋습니다.

1. 목록에서 권장 사항을 클릭 합니다.
1. **수정 단계** 섹션의 지침을 따릅니다. 각 권장 사항에는 고유한 지침 집합이 있습니다. 다음에는 HTTPS를 통한 트래픽만 허용 하도록 응용 프로그램을 구성 하는 수정 단계가 나와 있습니다.

    ![권장 사항 세부 정보](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 작업이 완료 되 면 재구성에 성공 하면 알려 주는 알림이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center의 권장 사항을 수정 하는 방법을 보여 줍니다. Security Center에 대해 자세히 알아보려면 다음 항목을 참조 하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
