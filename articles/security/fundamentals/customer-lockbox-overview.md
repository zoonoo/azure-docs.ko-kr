---
title: Microsoft Azure에 대 한 고객 Lockbox
description: Microsoft에서 고객 데이터에 액세스 해야 할 때 클라우드 공급자 액세스에 대 한 제어를 제공 하는 Microsoft Azure에 대 한 고객 Lockbox 기술 개요입니다.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 09/09/2020
ms.openlocfilehash: 5c24bd80721f626e38dcb886e89231c0b86056df
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650966"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure에 대 한 고객 Lockbox

> [!NOTE]
> 이 기능을 사용 하려면 조직에 최소한의 **개발자**로 [Azure 지원 계획이](https://azure.microsoft.com/support/plans/) 있어야 합니다.

Microsoft Azure 고객 Lockbox는 고객이 고객 데이터 액세스 요청을 검토 하 고 승인 하거나 거부할 수 있는 인터페이스를 제공 합니다. 지원 요청 시 Microsoft 엔지니어가 고객 데이터에 액세스 해야 하는 경우에 사용 됩니다.

이 문서에서는 이후의 검토 및 감사를 위해 고객 Lockbox 요청을 시작, 추적 및 저장 하는 방법을 설명 합니다.

고객 Lockbox 이제 일반 공급 되며, 현재 가상 컴퓨터에 대 한 원격 데스크톱 액세스를 사용할 수 있습니다.

## <a name="supported-services-and-scenarios-in-preview"></a>미리 보기에서 지원 되는 서비스 및 시나리오

다음 서비스는 현재 고객 Lockbox에 대해 미리 보기로 제공 됩니다.

- API Management
- Azure App Service
- Azure Database for MySQL
- Azure Databricks
- Azure Synapse Analytics
- Cognitive Services
- Container Registry
- Azure 데이터 팩터리
- Azure Database for PostgreSQL
- Azure Kubernetes Service
- Azure Data Box
- HDInsight
- Functions
- Azure Storage
- Azure SQL DB
- Azure Data Explorer
- 가상 컴퓨터 (이제 메모리 덤프 및 관리 디스크에 대 한 액세스도 포함)
- Azure 구독 전송

조직에서 이러한 미리 보기 제품에 대 한 고객 Lockbox를 사용 하도록 설정 하려면 [고객 Lockbox Azure 공개 미리 보기](https://aka.ms/customerlockbox/insiderprogram)에 등록 합니다.

## <a name="supported-services-and-scenarios-in-general-availability"></a>일반 공급에서 지원 되는 서비스 및 시나리오

다음 서비스 및 시나리오는 현재 고객 Lockbox에 대 한 일반 공급으로 제공 됩니다.

### <a name="remote-desktop-access-to-virtual-machines"></a>가상 컴퓨터에 대 한 원격 데스크톱 액세스

현재 가상 컴퓨터에 대 한 원격 데스크톱 액세스 요청에 대해 고객 Lockbox를 사용할 수 있습니다. 지원 되는 작업은 다음과 같습니다.
- PaaS (Platform as a service)-Azure Cloud Services (웹 역할 및 작업자 역할)
- IaaS (Infrastructure as a service)-Windows 및 Linux (Azure Resource Manager에만 해당)
- 가상 머신 확장 집합-Windows 및 Linux

> [!NOTE]
> IaaS 클래식 인스턴스는 고객 Lockbox에서 지원 되지 않습니다. IaaS 클래식 인스턴스에서 실행 되는 워크 로드가 있는 경우 클래식에서 리소스 관리자 배포 모델로 마이그레이션하는 것이 좋습니다. 지침은 [클래식에서 Azure Resource Manager IaaS 리소스의 플랫폼 지원 마이그레이션](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)을 참조 하세요.

#### <a name="detailed-audit-logs"></a>자세한 감사 로그

원격 데스크톱 액세스와 관련 된 시나리오의 경우 Windows 이벤트 로그를 사용 하 여 Microsoft 엔지니어가 수행한 작업을 검토할 수 있습니다. Azure Security Center 사용 하 여 이벤트 로그를 수집 하 고 분석을 위해 작업 영역에 데이터를 복사 하는 것이 좋습니다. 자세한 내용은 [Azure Security Center에서 데이터 수집](../../security-center/security-center-enable-data-collection.md)을 참조 하세요.

## <a name="workflow"></a>워크플로

다음 단계에서는 고객 Lockbox 요청에 대 한 일반적인 워크플로를 간략하게 설명 합니다.

1. 조직의 누군가가 Azure 워크 로드에 문제가 있습니다.

2. 이 사람이 문제를 해결 했지만 해결할 수 없는 경우 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 지원 티켓을 엽니다. 티켓은 Azure 고객 지원 엔지니어에 게 할당 됩니다.

3. Azure 지원 엔지니어가 서비스 요청을 검토 하 고 문제를 해결 하기 위한 다음 단계를 결정 합니다.

4. 지원 엔지니어가 표준 도구 및 원격 분석을 사용 하 여 문제를 해결할 수 없는 경우 다음 단계는 JIT (Just-in-time) 액세스 서비스를 사용 하 여 승격 된 권한을 요청 하는 것입니다. 이 요청은 원래 지원 엔지니어가 사용할 수 있습니다. 또는 문제가 Azure DevOps 팀에 에스컬레이션 되기 때문에 다른 엔지니어가이를 수 있습니다.

5. Azure 엔지니어가 액세스 요청을 제출한 후 Just-in-time 서비스는 다음과 같은 요인을 고려 하 여 요청을 평가 합니다.
    - 리소스의 범위입니다.
    - 요청 자가 격리 된 id 인지 아니면 multi-factor authentication을 사용 하는지 여부
    - 권한 수준

    JIT 규칙에 따라이 요청에는 내부 Microsoft 승인자의 승인이 포함 될 수도 있습니다. 예를 들어 승인자는 고객 지원 리더 또는 DevOps 관리자가 될 수 있습니다.

6. 요청을 통해 고객 데이터에 직접 액세스 해야 하는 경우에는 고객 Lockbox 요청이 시작 됩니다. 예를 들어 고객의 가상 컴퓨터에 대 한 원격 데스크톱 액세스를 사용할 수 있습니다.

    요청은 이제 고객에 게 **알림** 상태 이며, 액세스 권한을 부여 하기 전에 고객의 승인을 기다리고 있습니다.

7. 고객 조직에서 Azure 구독에 대 한 [소유자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) 을 가진 사용자는 Microsoft에서 전자 메일을 받아 보류 중인 액세스 요청에 대해 알립니다. 고객 Lockbox 요청의 경우이 사람은 지정 된 승인자입니다.

    전자 메일 예:

    ![Azure 고객 Lockbox-전자 메일 알림](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 전자 메일 알림은 Azure Portal의 **고객 Lockbox** 블레이드에 대 한 링크를 제공 합니다. 지정 된 승인자는이 링크를 사용 하 여 Azure Portal에 로그인 하 여 조직에서 고객 Lockbox 하는 보류 중인 요청을 확인 합니다.

    ![Azure 고객 Lockbox-방문 페이지](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   요청은 4 일간 고객 큐에 유지 됩니다. 이 시간이 지나면 액세스 요청이 자동으로 만료 되 고 Microsoft 엔지니어에 게 액세스 권한이 부여 되지 않습니다.

9. 보류 중인 요청의 세부 정보를 가져오기 위해 지정 된 승인자는 **보류 중인 요청**에서 lockbox 요청을 선택할 수 있습니다.

    ![Azure 고객 Lockbox-보류 중인 요청 보기](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 지정 된 승인자는 **서비스 요청 ID** 를 선택 하 여 원래 사용자가 만든 지원 티켓 요청을 볼 수도 있습니다. 이 정보는 Microsoft 지원가 개입 된 이유와 보고 된 문제의 기록에 대 한 컨텍스트를 제공 합니다. 다음은 그 예입니다. 

    ![Azure 고객 Lockbox-지원 티켓 요청 보기](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 요청을 검토 한 후 지정 된 승인자가 **승인** 또는 **거부**를 선택 합니다.

    ![Azure 고객 Lockbox-승인 또는 거부를 선택 합니다.](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    선택의 결과로 다음과 같이 합니다.
    - **승인**: Microsoft 엔지니어에 게 액세스 권한이 부여 됩니다. 기본적으로 8 시간 동안 액세스 권한이 부여 됩니다.
    - **거부**: Microsoft 엔지니어가 관리자 권한으로 요청한 요청이 거부 되 고 추가 작업은 수행 되지 않습니다.

감사를 위해이 워크플로에서 수행 되는 작업은 [요청 로그 고객 Lockbox](#auditing-logs)에 기록 됩니다.

## <a name="auditing-logs"></a>감사 로그

고객 Lockbox 로그는 활동 로그에 저장 됩니다. Azure Portal에서 **활동 로그** 를 선택 하 여 고객 Lockbox 요청과 관련 된 감사 정보를 확인 합니다. 다음과 같은 특정 작업을 필터링 할 수 있습니다.
- **Lockbox 요청 거부**
- **Lockbox 요청 만들기**
- **Lockbox 요청 승인**
- **Lockbox 요청 만료**

예를 들어

![Azure 고객 Lockbox-활동 로그](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Azure Security 벤치 마크와 고객 Lockbox 통합

Azure 보안 벤치 마크에 고객 Lockbox 적용 가능성을 다루는 새로운 기준 제어 ([3.13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios))가 도입 되었습니다. 이제 고객은 벤치 마크를 활용 하 여 서비스에 대 한 고객 Lockbox 적용 가능성을 검토할 수 있습니다.

## <a name="exclusions"></a>제외

다음 엔지니어링 지원 시나리오에서는 고객 Lockbox 요청이 트리거되지 않습니다.

- Microsoft 엔지니어가 표준 운영 절차를 벗어나는 작업을 수행 해야 합니다. 예를 들어 예기치 않거나 예측할 수 없는 시나리오에서 서비스를 복구 하거나 복원 합니다.

- Microsoft 엔지니어가 문제 해결의 일환으로 Azure 플랫폼에 액세스 하 고 실수로 고객 데이터에 액세스할 수 있습니다. 예를 들어 Azure 네트워크 팀은 네트워크 장치에서 패킷 캡처를 발생 시키는 문제 해결을 수행 합니다. 그러나 고객이 전송 중인 데이터를 암호화 한 경우 엔지니어가 데이터를 읽을 수 없습니다.

## <a name="next-steps"></a>다음 단계

고객 Lockbox는 최소한의 **개발자**로 [Azure 지원 계획](https://azure.microsoft.com/support/plans/) 을 보유 하 고 있는 모든 고객에 게 자동으로 제공 됩니다.

적격 지원 계획을 사용 하는 경우 고객 Lockbox 사용 하도록 설정 하는 작업이 필요 하지 않습니다. 이 작업이 조직의 다른 사용자가 작성 한 지원 티켓을 진행 하는 데 필요한 경우 Microsoft 엔지니어가 고객 Lockbox 요청을 시작 합니다.
