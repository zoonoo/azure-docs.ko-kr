---
title: 마이크로소프트 Azure를 위한 고객 잠금 상자
description: Microsoft가 고객 데이터에 액세스해야 할 때 클라우드 공급자 액세스를 제어할 수 있는 Microsoft Azure용 고객 잠금 상자의 기술 개요입니다.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561972"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>마이크로소프트 Azure를 위한 고객 잠금 상자

> [!NOTE]
> 이 기능을 사용하려면 조직에 최소한의 **개발자**수준으로 [Azure 지원 계획이](https://azure.microsoft.com/support/plans/) 있어야 합니다.

Microsoft Azure용 고객 잠금 상자는 고객이 고객 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있는 인터페이스를 제공합니다. Microsoft 엔지니어가 지원 요청 중에 고객 데이터에 액세스해야 하는 경우에 사용됩니다.

이 문서에서는 고객 Lockbox 요청을 시작, 추적 및 나중에 검토 및 감사를 위해 저장하는 방법을 다룹니다.

이제 고객 Lockbox를 일반적으로 사용할 수 있으며 현재 가상 시스템에 대한 원격 데스크톱 액세스를 사용할 수 있습니다.

## <a name="workflow"></a>워크플로

다음 단계에서는 고객 잠금 상자 요청에 대한 일반적인 워크플로를 간략하게 설명합니다.

1. 조직의 누군가가 Azure 워크로드에 문제가 있습니다.

2. 이 사람이 문제를 해결하지만 해결할 수 없는 후에는 [Azure 포털에서](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)지원 티켓을 엽니다. 티켓은 Azure 고객 지원 엔지니어에게 할당됩니다.

3. Azure 지원 엔지니어는 서비스 요청을 검토하고 문제를 해결하기 위한 다음 단계를 결정합니다.

4. 지원 엔지니어가 표준 도구 및 원격 분석을 사용하여 문제를 해결할 수 없는 경우 다음 단계는 JIT(Just-In-Time) 액세스 서비스를 사용하여 높은 권한을 요청하는 것입니다. 이 요청은 원래 지원 엔지니어의 요청일 수 있습니다. 또는 문제가 Azure DevOps 팀으로 에스컬레이션되기 때문에 다른 엔지니어가 사용할 수 있습니다.

5. Azure Engineer에서 액세스 요청을 제출한 후 Just-In-Time 서비스는 다음과 같은 요소를 고려하여 요청을 평가합니다.
    - 리소스의 범위
    - 요청자가 격리된 ID인지 아니면 다단계 인증을 사용하는지 여부
    - 권한 수준

    JIT 규칙에 따라 이 요청에는 내부 Microsoft 승인자의 승인도 포함될 수 있습니다. 예를 들어 승인자는 고객 지원 책임자 또는 DevOps 관리자일 수 있습니다.

6. 요청에서 고객 데이터에 직접 액세스해야 하는 경우 고객 잠금 상자 요청이 시작됩니다. 예를 들어, 고객의 가상 컴퓨터에 대한 원격 데스크톱 액세스.

    이제 요청이 **고객 알림** 상태로 되어 액세스 권한을 부여하기 전에 고객의 승인을 기다리고 있습니다.

7. 고객 조직에서 Azure 구독에 대한 [소유자 역할이](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) 있는 사용자는 보류 중인 액세스 요청에 대해 알리는 전자 메일을 Microsoft로부터 받습니다. 고객 잠금 상자 요청의 경우 이 담당자는 지정된 승인자입니다.

    전자 메일 예:

    ![Azure 고객 잠금 상자 - 전자 메일 알림](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 전자 메일 알림은 Azure 포털의 **고객 잠금 상자** 블레이드에 대한 링크를 제공합니다. 이 링크를 사용하면 지정된 승인자가 Azure Portal에 서명하여 해당 조직에서 고객 잠금 상자에 대해 가지고 있는 보류 중인 요청을 볼 수 있습니다.

    ![Azure 고객 잠금 상자 - 방문 페이지](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   요청은 4일 동안 고객 큐에 남아 있습니다. 이 시간 이후에는 액세스 요청이 자동으로 만료되고 Microsoft 엔지니어에게 액세스 권한이 부여되지 않습니다.

9. 보류 중인 요청의 세부 정보를 얻으려면 지정된 승인자가 보류 **중인 요청에서**잠금 상자 요청을 선택할 수 있습니다.

    ![Azure 고객 잠금 - 보류 중인 요청을 봅니다.](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 지정된 승인자가 **서비스 요청 ID를** 선택하여 원래 사용자가 만든 지원 티켓 요청을 볼 수도 있습니다. 이 정보는 Microsoft 지원이 종사하는 이유와 보고된 문제의 기록에 대한 컨텍스트를 제공합니다. 예를 들어:

    ![Azure 고객 잠금 - 지원 티켓 요청 보기](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 요청을 검토한 후 지정된 승인자는 **승인** 또는 **거부를**선택합니다.

    ![Azure 고객 잠금 상자 - 승인 또는 거부를 선택합니다.](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    선택의 결과로:
    - **승인**: 액세스는 Microsoft 엔지니어에게 부여됩니다. 액세스는 8시간의 기본 기간 동안 부여됩니다.
    - **거부**: Microsoft 엔지니어의 높은 액세스 요청이 거부되고 추가 작업이 수행되지 않습니다.

감사를 위해 이 워크플로에서 수행된 작업은 [고객 Lockbox 요청 로그에 기록됩니다.](#auditing-logs)

## <a name="auditing-logs"></a>감사 로그

고객 잠금 상자 로그는 활동 로그에 저장됩니다. Azure 포털에서 **활동 로그를** 선택하여 고객 잠금 상자 요청과 관련된 감사 정보를 볼 수 있습니다. 다음과 같은 특정 작업을 필터링할 수 있습니다.
- **잠금 상자 요청 거부**
- **잠금 상자 요청 만들기**
- **잠금 상자 요청 승인**
- **잠금 상자 요청 만료**

예를 들어

![Azure 고객 잠금 상자 - 활동 로그](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>일반 공급 지원 되는 서비스 및 시나리오

다음 서비스 및 시나리오는 현재 고객 Lockbox에 대 한 일반 공급.

### <a name="remote-desktop-access-to-virtual-machines"></a>가상 머신에 대한 원격 데스크톱 액세스

고객 잠금 상자는 현재 가상 컴퓨터에 대한 원격 데스크톱 액세스 요청에 대해 활성화되어 있습니다. 다음 워크로드가 지원됩니다.
- 서비스로서의 플랫폼(PaaS) - Azure 클라우드 서비스(웹 역할 및 작업자 역할)
- 서비스로서의 인프라(IaaS) - Windows 및 Linux(Azure 리소스 관리자만)
- 가상 머신 스케일 세트 - 윈도우와 리눅스

> [!NOTE]
> IaaS 클래식 인스턴스는 고객 잠금 상자에서 지원되지 않습니다. IaaS Classic 인스턴스에서 실행 중인 워크로드가 있는 경우 클래식에서 리소스 관리자 배포 모델로 마이그레이션하는 것이 좋습니다. 지침은 [클래식에서 Azure 리소스 관리자로의 IaaS 리소스의 플랫폼 지원 마이그레이션을](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)참조하십시오.

#### <a name="detailed-audit-logs"></a>자세한 감사 로그

원격 데스크톱 액세스와 관련된 시나리오의 경우 Windows 이벤트 로그를 사용하여 Microsoft 엔지니어가 수행한 작업을 검토할 수 있습니다. Azure 보안 센터를 사용하여 이벤트 로그를 수집하고 분석을 위해 데이터를 작업 영역에 복사하는 것이 좋습니다. 자세한 내용은 [Azure 보안 센터의 데이터 수집을](../../security-center/security-center-enable-data-collection.md)참조하십시오.

## <a name="supported-services-and-scenarios-in-preview"></a>미리 보기에서 지원되는 서비스 및 시나리오

고객 잠금 상자에 대 한 현재 미리 보기에 다음 서비스:

- Azure Storage

- Azure SQL DB

- Azure Data Explorer

- 가상 컴퓨터(이제 메모리 덤프 및 관리 디스크에 대한 액세스도 포함)

- Azure 구독 전송

조직에 대한 이러한 미리 보기 제품에 대한 고객 잠금 상자를 사용하려면 [Azure 공용 미리 보기에 대한 고객 잠금 상자에](https://aka.ms/customerlockbox/insiderprogram)등록합니다.


## <a name="exclusions"></a>제외

다음과 같은 엔지니어링 지원 시나리오에서는 고객 잠금 상자 요청이 트리거되지 않습니다.

- Microsoft 엔지니어는 표준 운영 절차를 벗어난 작업을 수행해야 합니다. 예를 들어 예기치 않거나 예기치 않은 시나리오에서 서비스를 복구하거나 복원합니다.

- Microsoft 엔지니어는 문제 해결의 일환으로 Azure 플랫폼에 액세스하고 실수로 고객 데이터에 액세스할 수 있습니다. 예를 들어 Azure 네트워크 팀은 네트워크 장치에서 패킷 캡처를 초래하는 문제 해결을 수행합니다. 그러나 고객이 전송 중에 데이터를 암호화한 경우 엔지니어는 데이터를 읽을 수 없습니다.

## <a name="next-steps"></a>다음 단계

고객 잠금 상자는 최소한의 **개발자**수준으로 [Azure 지원 계획이](https://azure.microsoft.com/support/plans/) 있는 모든 고객에 대해 자동으로 사용할 수 있습니다.

적격 한 지원 계획이 있는 경우 고객 Lockbox를 사용하도록 설정 하기 위해 아무 조치도 필요 하지 않습니다. 이 작업이 조직의 누군가로부터 제출된 지원 티켓을 진행하기 위해 필요한 경우 Microsoft 엔지니어가 고객 잠금 상자 요청을 시작합니다.
