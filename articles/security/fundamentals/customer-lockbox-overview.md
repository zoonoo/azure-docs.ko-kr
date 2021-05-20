---
title: Microsoft Azure에 대한 고객 Lockbox
description: Microsoft에서 고객 데이터에 액세스해야 할 때 클라우드 공급자 액세스에 대한 제어를 제공하는 Microsoft Azure에 대한 고객 Lockbox 기술 개요입니다.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 04/05/2021
ms.openlocfilehash: 80d1e4f39d69f761b801ccec834c0228057e4847
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448528"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure에 대한 고객 Lockbox

> [!NOTE]
> 이 기능을 사용하려면 조직에 최소 수준의 **개발자** 가 포함된 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)이 있어야 합니다.

Microsoft 직원과 하위 프로세서에서 수행하는 대부분의 작업, 지원 및 문제 해결에는 고객 데이터에 대한 액세스 권한이 필요하지 않습니다. 이러한 액세스가 필요한 드문 상황에서 Microsoft Azure에 대한 고객 Lockbox는 고객이 고객 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있는 인터페이스를 제공합니다. 고객이 시작한 지원 티켓에 대한 응답이나 Microsoft에서 확인한 문제에 따라 Microsoft 엔지니어가 고객 데이터에 액세스해야 하는 경우에 사용됩니다.

이 문서에서는 고객 Lockbox를 사용하도록 설정하는 방법과 이후 검토 및 감사를 위해 Lockbox 요청을 시작, 추적 및 저장하는 방법을 설명합니다.

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios"></a>지원되는 서비스 및 시나리오

### <a name="general-availability"></a>일반 공급
다음 서비스는 일반적으로 고객 Lockbox에서 사용할 수 있습니다.

- Azure API Management
- Azure App Service
- Azure Cognitive Services
- Azure Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure Data Explorer
- Azure 데이터 팩터리
- Azure Database for PostgreSQL
- Azure 기능
- Azure HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL Database
- Azure 구독 전송
- Azure Synapse Analytics
- Azure의 가상 머신(원격 데스크톱 액세스, 메모리 덤프에 대한 액세스 및 관리 디스크 포함)

### <a name="public-preview"></a>공개 미리 보기
다음 서비스는 현재 고객 Lockbox에 대해 미리 보기로 제공됩니다.

- Azure Machine Learning
- Azure Batch

## <a name="enable-customer-lockbox"></a>고객 Lockbox 사용

이제 고객 Lockbox 블레이드의 [관리 모듈](https://aka.ms/customerlockbox/administration)에서 고객 Lockbox를 사용하도록 설정할 수 있습니다.  

> [!NOTE]
> 고객 Lockbox를 사용하도록 설정하려면 사용자 계정에 [전역 관리자 역할이 할당](../../active-directory/roles/manage-roles-portal.md)되어야 합니다.

## <a name="workflow"></a>워크플로

다음 단계에서는 고객 Lockbox 요청에 대한 일반적인 워크플로를 간략하게 설명합니다.

1. 조직의 누군가가 Azure 워크로드에 문제가 있습니다.

2. 이 사람이 문제를 해결했지만 해결할 수 없는 경우 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 지원 티켓을 엽니다. 티켓은 Azure 고객 지원 엔지니어에게 할당됩니다.

3. Azure 지원 엔지니어가 서비스 요청을 검토하고 문제를 해결하기 위한 다음 단계를 결정합니다.

4. 지원 엔지니어가 표준 도구 및 서비스 생성 데이터를 사용하여 문제를 해결할 수 없는 경우 다음 단계는 JIT(Just-In-Time) 액세스 서비스를 사용하여 승격된 권한을 요청하는 것입니다. 이 요청은 Azure DevOps 팀에 문제를 제기하기 때문에 원래 지원 엔지니어 또는 다른 엔지니어가 사용할 수 있습니다.

5. Azure 엔지니어가 액세스 요청을 제출한 후 Just-In-Time 서비스는 다음과 같은 요인을 고려하여 요청을 평가합니다.
    - 리소스의 범위
    - 요청자가 격리된 ID인지 아니면 다단계 인증을 사용하는지 여부
    - 권한 수준

    JIT 규칙에 따라 이 요청에는 내부 Microsoft 승인자의 승인이 포함될 수도 있습니다. 예를 들어 승인자는 고객 지원 리더 또는 DevOps 관리자가 될 수 있습니다.

6. 요청을 통해 고객 데이터에 직접 액세스해야 하는 경우에는 고객 Lockbox 요청이 시작됩니다. 예를 들어 고객의 가상 머신에 대한 원격 데스크톱 액세스입니다.

    요청은 이제 **고객에게 알림** 상태이며, 액세스 권한을 부여하기 전에 고객의 승인을 기다리고 있습니다.

7. 고객 조직에서 Azure 구독에 대한 [소유자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)을 가진 사용자는 Microsoft에서 이메일을 받아 보류 중인 액세스 요청에 대해 알립니다. 고객 Lockbox 요청의 경우 이 사람은 지정된 승인자입니다.

    전자 메일 예:

    ![Azure 고객 Lockbox - 이메일 알림](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 이메일 알림은 관리 모듈의 **고객 Lockbox** 블레이드에 대한 링크를 제공합니다. 지정된 승인자는 이 링크를 사용하여 Azure Portal에 로그인하여 조직이 고객 Lockbox에 대해 보류 중인 요청을 확인합니다.

    ![Azure 고객 Lockbox - 방문 페이지](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   요청은 4일간 고객 큐에 유지됩니다. 이 시간이 지나면 액세스 요청이 자동으로 만료되고 Microsoft 엔지니어에게 액세스 권한이 부여되지 않습니다.

9. 보류 중인 요청의 세부 정보를 가져오기 위해 지정된 승인자는 **보류 중인 요청** 에서 lockbox 요청을 선택할 수 있습니다.

    ![Azure 고객 Lockbox - 보류 중인 요청 보기](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 지정된 승인자는 **서비스 요청 ID** 를 선택하여 원래 사용자가 만든 지원 티켓 요청을 볼 수도 있습니다. 이 정보는 Microsoft 지원이 개입된 이유와 보고된 문제의 기록에 대한 컨텍스트를 제공합니다. 예:

    ![Azure 고객 Lockbox - 지원 티켓 요청 보기](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 요청을 검토한 후 지정된 승인자가 **승인** 또는 **거부** 를 선택합니다.

    ![Azure 고객 Lockbox - 승인 또는 거부 선택](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    선택의 결과:
    - **승인**: Microsoft 엔지니어에게 액세스 권한이 부여됩니다. 기본적으로 8시간 동안 액세스 권한이 부여됩니다.
    - **거부**: Microsoft 엔지니어의 상승된 액세스 권한 요청이 거부되고 추가 작업은 수행되지 않습니다.

감사를 위해 이 워크플로에서 수행되는 작업은 [고객 Lockbox 요청 로그](#auditing-logs)에 기록됩니다.

## <a name="auditing-logs"></a>감사 로그

고객 Lockbox 로그는 활동 로그에 저장됩니다. Azure Portal에서 **활동 로그** 를 선택하여 고객 Lockbox 요청과 관련된 감사 정보를 확인합니다. 다음과 같은 특정 작업을 필터링할 수 있습니다.
- **Lockbox 요청 거부**
- **Lockbox 요청 만들기**
- **Lockbox 요청 승인**
- **Lockbox 요청 만료**

예를 들어

![Azure 고객 Lockbox - 활동 로그](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Azure Security 벤치마크와 고객 Lockbox 통합

Azure 보안 벤치마크에 고객 Lockbox 적용 가능성을 다루는 새로운 기준 제어([3.13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios))를 도입했습니다. 이제 고객은 벤치마크를 활용하여 서비스에 대한 고객 Lockbox 적용 가능성을 검토할 수 있습니다.

## <a name="exclusions"></a>제외

다음 엔지니어링 지원 시나리오에서는 고객 Lockbox 요청이 트리거되지 않습니다.

- 표준 운영 절차를 벗어난 응급 시나리오 예를 들어 예기치 않은 시나리오에서 서비스를 복구하거나 복원하려면 주요 서비스 중단에 즉각적인 주의가 필요합니다. 이러한 "중단" 이벤트는 드물게 발생하며, 대부분의 경우에는 문제를 해결하기 위해 고객 데이터에 액세스할 필요가 없습니다.
- Microsoft 엔지니어가 문제 해결의 일환으로 Azure 플랫폼에 액세스하고 실수로 고객 데이터에 노출될 수 있습니다. 예를 들어 Azure 네트워크 팀은 네트워크 디바이스에서 패킷 캡처를 발생시키는 문제를 해결합니다. 이러한 시나리오는 중요한 고객 데이터에 대한 액세스를 초래하는 경우가 거의 없습니다. 고객은 전송 중 및 미사용 암호화를 사용하여 데이터를 더욱 안전하게 보호할 수 있습니다.

또한 고객 Lockbox 요청은 데이터에 대한 외부 법적 요구에 의해 트리거되지 않습니다. 자세한 내용은 Microsoft 보안 센터에서 [데이터에 대한 정부 요청](https://www.microsoft.com/trust-center/)에 대한 설명을 참조하세요.

## <a name="next-steps"></a>다음 단계

고객 Lockbox는 최소한의 **개발자** 로 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)을 보유하고 있는 모든 고객에게 제공됩니다. 고객 Lockbox 블레이드의 [관리 모듈](https://aka.ms/customerlockbox/administration)에서 고객 Lockbox를 사용하도록 설정할 수 있습니다.

이 작업이 지원 사례를 진행하는 데 필요한 경우 Microsoft 엔지니어가 고객 Lockbox 요청을 시작합니다.
