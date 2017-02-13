---
title: "Azure Security Center의 권한 | Microsoft Docs"
description: "이 문서는 Azure Security Center가 역할 기반 액세스 제어를 사용하여 사용자에게 권한을 할당하는 방법과 각 역할에 대해 허용된 작업을 식별하는 방법에 대해 설명합니다."
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5c4a08d96175d431e0a29dfc5927b64567c40117
ms.openlocfilehash: d1c8a39bbcda7d22fdce08d122098e994ca87451


---

# <a name="permissions-in-azure-security-center"></a>Azure Security Center의 권한

Azure Security Center는 Azure에서 사용자, 그룹 및 서비스에 [기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 제공하는 [RBAC(역할 기반 액세스 제어)](../active-directory/role-based-access-control-configure.md)를 사용합니다.

Security Center는 리소스 구성을 평가하여 보안 문제 및 취약성을 식별합니다. Security Center에서는 리소스가 속한 구독이나 리소스 그룹에 대한 소유자, 참가자 또는 독자 역할을 할당 받을 때 리소스와 관련된 항목만 볼 수 있습니다.

## <a name="roles-and-allowed-actions"></a>역할 및 허용되는 작업

다음 표는 Security Center의 역할 및 허용되는 작업을 보여 줍니다. X는 작업이 해당 역할에 대해 허용된다는 것을 나타냅니다.

| 역할 | 보안 정책 편집 | 리소스에 보안 권장 사항 적용 | 경고 재구성 또는 해제 | 구독에서 경고 보기 | 특정 리소스에 대한 경고 보기 |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 구독 소유자 | X | X | X | X | X |
| 구독 참가자 | X | X | X | X | X |
| 리소스 그룹 소유자 | -- | X | -- | -- | X |
| 리소스 그룹 참가자 | -- | X | -- | -- | X |
| 판독기 | -- | -- | -- | X | X |

> [!NOTE]
> 사용자가 자신의 작업을 완료하는 데 필요한 최소한의 역할을 할당하는 것이 좋습니다. 예를 들어, 리소스의 보안 상태에 대한 정보를 보기만 하고 권장 사항 적용이나 정책 편집 등의 조치는 취하지 않는 사용자에게 독자 역할을 할당합니다.
>
>

## <a name="next-steps"></a>다음 단계
이 문서는 Security Center에서 RBAC를 사용하여 사용자에게 사용 권한을 할당하고 각 역할에 대해 허용된 작업을 식별하는 방법에 대해 설명합니다. 이제 구독의 보안 상태를 모니터링하고, 보안 정책을 편집하고, 권장 사항을 적용하는 데 필요한 역할 할당에 익숙해졌으므로 다음을 수행하는 방법을 알아봅니다.

- [Security Center에서 보안 정책 설정](security-center-policies.md)
- [Security Center의 보안 권장 사항 관리](security-center-recommendations.md)
- [Azure 리소스의 보안 상태 모니터링](security-center-monitoring.md)
- [Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
- [파트너 보안 솔루션 모니터링](security-center-partner-solutions.md)



<!--HONumber=Dec16_HO2-->


