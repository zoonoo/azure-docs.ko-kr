---
title: Azure Active 디렉터리 도메인 서비스의 상태 확인 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스(Azure AD DS) 관리 도메인의 상태를 확인하고 Azure 포털을 사용하여 상태 메시지를 이해하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655632"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active 디렉터리 도메인 서비스 관리 도메인의 상태 확인

Azure Active Directory 도메인 서비스(Azure AD DS)는 관리되는 도메인을 정상 상태로 유지하고 최신 상태로 유지하기 위해 몇 가지 백그라운드 작업을 실행합니다. 이러한 작업에는 백업 수행, 보안 업데이트 적용 및 Azure AD의 데이터 동기화가 포함됩니다. Azure AD DS 관리 도메인에 문제가 있는 경우 이러한 작업이 성공적으로 완료되지 않을 수 있습니다. 문제를 검토하고 해결하려면 Azure 포털을 사용하여 Azure AD DS 관리 도메인의 상태 상태를 확인할 수 있습니다.

이 문서에서는 Azure AD DS 상태를 보고 표시된 정보 또는 경고를 이해하는 방법을 보여 주며 있습니다.

## <a name="view-the-health-status"></a>상태 보기

Azure AD DS 관리 도메인의 상태 상태는 Azure 포털을 사용하여 표시됩니다. 관리되는 도메인의 상태 문제를 나타내는 경고와 함께 Azure AD와의 마지막 백업 시간 및 동기화에 대한 정보를 볼 수 있습니다. Azure AD DS 관리 도메인의 상태 상태를 보려면 다음 단계를 완료하십시오.

1. Azure 포털에서 **Azure AD 도메인 서비스를**검색하고 선택합니다.
1. *aaddscontoso.com*와 같은 Azure AD DS 관리 도메인을 선택합니다.
1. Azure AD DS 리소스 창의 왼쪽에서 **상태를**선택합니다. 다음 예제 스크린샷은 정상 Azure AD DS 관리 도메인과 마지막 백업 및 Azure AD 동기화 의 상태를 보여 주며 다음과 같은

    ![Azure Active Directory 도메인 서비스 상태를 보여 주면 Azure 포털의 상태 페이지 개요](./media/check-health/health-page.png)

상태 페이지의 *마지막으로 평가된* 타임스탬프는 Azure AD DS 관리 도메인이 마지막으로 확인된 시기를 표시합니다. Azure AD DS 관리 도메인의 상태는 매시간 평가됩니다. Azure AD DS 관리 도메인을 변경하면 다음 평가 주기까지 기다렸다가 업데이트된 상태 상태를 확인합니다.

오른쪽 상단의 상태는 Azure AD DS 관리 도메인의 전반적인 상태를 나타냅니다. 상태는 도메인의 모든 기존 경고를 고려합니다. 다음 표에는 사용 가능한 상태 표시기가 자세히 설명되어 있습니다.

| 상태 | 아이콘 | 설명 |
| --- | :----: | --- |
| 실행 중 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Azure AD DS 관리 도메인이 올바르게 실행되고 있으며 중요 또는 경고 경고가 없습니다. 도메인에 정보 경고가 있을 수 있습니다. |
| 주의가 필요(경고) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Azure AD DS 관리 도메인에는 중요한 경고가 없지만 해결해야 할 경고 경고가 하나 이상 있습니다. |
| 주의가 필요(중요) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Azure AD DS 관리 도메인에 해결해야 하는 중요한 경고가 하나 이상 있습니다. 경고 및/또는 정보 경고가 있을 수도 있습니다. |
| 배포 중 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Azure AD DS 도메인이 배포되고 있습니다. |

## <a name="understand-monitors-and-alerts"></a>모니터 및 경고 이해

Azure AD DS 관리 도메인의 상태 상태에는 *모니터*및 *경고의*두 가지 유형의 정보가 표시됩니다. 모니터는 핵심 백그라운드 작업이 완료된 시간을 표시합니다. 경고는 관리되는 도메인의 안정성을 개선하기 위한 정보 또는 제안 사항을 제공합니다.

### <a name="monitors"></a>모니터

모니터는 정기적으로 검사되는 Azure AD DS 관리 도메인의 영역입니다. Azure AD DS 관리 도메인에 대한 활성 경고가 있는 경우 모니터 중 하나가 문제를 보고할 수 있습니다. Azure AD 도메인 서비스에는 현재 다음 영역에 대한 모니터가 있습니다.

* Backup
* Azure AD와 동기화

#### <a name="backup-monitor"></a>백업 모니터

백업 모니터는 Azure AD DS 관리 도메인의 자동 정기 백업이 성공적으로 실행되는지 확인합니다. 다음 표는 사용 가능한 백업 모니터 상태에 대해 자세히 설명합니다.

| 세부 정보 값 | 설명 |
| --- | --- |
| 백업안 해 | 새 Azure AD DS 관리 도메인에 대해 이 상태는 정상입니다. 첫 번째 백업은 Azure AD DS 관리 도메인이 배포된 후 24시간 후에 만들어져야 합니다. 이 상태가 지속되면 [Azure 지원 요청을 엽니다.][azure-support] |
| 1-14일 전에 마지막 백업 수행 | 이 시간 범위는 백업 모니터에 대한 예상 상태입니다. 이 기간에는 자동 정기 백업이 수행되어야 합니다. |
| 마지막 백업이 수행된 후 14일이 넘었습니다. | 기간이 2보다 길면 자동화된 일반 백업에 문제가 있음을 나타냅니다. 활성 중요 경고는 Azure AD DS 관리 도메인이 백업되지 않도록 할 수 있습니다. Azure AD DS 관리 도메인에 대한 활성 경고를 해결합니다. 백업 모니터가 최근 백업을 보고하도록 상태를 업데이트하지 않으면 [Azure 지원 요청을 엽니다.][azure-support] |

#### <a name="synchronization-with-azure-ad-monitor"></a>Azure AD 모니터와의 동기화

Azure AD DS 관리 도메인은 Azure Active Directory와 정기적으로 동기화됩니다. 사용자 및 그룹 개체 수와 마지막 동기화 이후 Azure AD 디렉터리에서 변경한 횟수는 동기화하는 데 걸리는 길이에 영향을 줍니다. Azure AD DS 관리 도메인이 3일 전에 마지막으로 동기화된 경우 활성 경고를 확인하고 해결합니다. 동기화 모니터가 활성 경고를 해결한 후 최근 동기화를 표시하도록 상태를 업데이트하지 않으면 [Azure 지원 요청을 엽니다.][azure-support]

### <a name="alerts"></a>경고

서비스가 올바르게 실행되도록 처리해야 하는 Azure AD DS 관리 도메인의 문제에 대한 경고가 생성됩니다. 각 경고는 문제를 설명하고 문제를 해결하기 위한 특정 단계를 간략하게 설명하는 URL을 제공합니다. 가능한 경고 및 해결 방법에 대한 자세한 내용은 [문제 해결 경고를](troubleshoot-alerts.md)참조하십시오.

상태 상태 경고는 다음과 같은 심각도 수준으로 분류됩니다.

 * **중요 경고는** Azure AD DS 관리 도메인에 심각한 영향을 주는 문제입니다. 이러한 경고는 즉시 해결해야 합니다. Azure 플랫폼은 문제가 해결될 때까지 관리되는 도메인을 모니터링, 관리, 패치 및 동기화할 수 없습니다.
 * **경고 경고는** 문제가 지속될 경우 Azure AD DS 관리 도메인 작업에 영향을 미칠 수 있는 문제를 알려줍니다. 이러한 경고는 관리되는 도메인을 보호하기 위한 권장 사항도 제공합니다.
 * **정보 경고는** Azure AD DS 관리 도메인에 부정적인 영향을 미치지 않는 알림입니다. 정보 경고는 관리되는 도메인에서 발생하는 일에 대한 몇 가지 통찰력을 제공합니다.

## <a name="next-steps"></a>다음 단계

상태 상태 페이지에 표시되는 경고에 대한 자세한 내용은 [관리되는 도메인의 경고 해결을 참조하세요.][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
