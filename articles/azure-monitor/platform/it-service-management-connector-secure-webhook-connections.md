---
title: Azure Monitor에서 IT 서비스 관리 커넥터-보안 내보내기
description: 이 문서에서는 itsm 제품/서비스를 Azure Monitor의 보안 내보내기와 연결 하 여 ITSM 작업 항목을 중앙에서 모니터링 하 고 관리 하는 방법에 대 한 정보를 제공 합니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568645"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>보안 내보내기를 사용 하 여 ITSM 도구에 Azure 연결

이 문서에서는 보안 내보내기를 사용 하 여 ITSM 제품/서비스 간의 연결을 구성 하는 방법에 대 한 정보를 제공 합니다.

보안 내보내기는 [Itsmc](./itsmc-overview.md) (IT Service Management)의 업데이트 된 버전입니다. 두 버전 모두 Azure Monitor 경고가 발생 하면 ITSM 도구에서 작업 항목을 만들 수 있습니다. 이 기능에는 메트릭, 로그 및 활동 로그 경고가 포함 됩니다.

[Itsmc](./itsmc-overview.md) 는 사용자 및 암호 자격 증명을 사용 하지만, 보안 내보내기는 Azure Active Directory (Azure AD)를 사용 하기 때문에 더 강력한 인증이 있습니다. Azure AD(Azure Active Directory)는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다. 사용자가 로그인 하 고 내부 또는 외부 리소스에 액세스할 수 있습니다. ITSM에서 Azure AD를 사용 하면 외부 시스템으로 보낸 azure 경고 (Azure AD 응용 프로그램 ID 사용)를 식별할 수 있습니다.

> [!NOTE]
> 보안 내보내기를 사용 하 여 ITSM tools에 Azure 연결은 미리 보기로 제공 됩니다.

## <a name="secure-export-architecture"></a>보안 내보내기 아키텍처

보안 내보내기 아키텍처에는 다음과 같은 새로운 기능이 도입 되었습니다.

* **새 작업 그룹** -경고는 itsm에서를 사용 하는 Itsm 작업 그룹 대신 보안 webhook 작업 그룹을 사용 하 여 itsm 도구에 전송 됩니다.
* **AZURE ad 인증** -사용자/암호 자격 증명 대신 azure ad를 사용 하 여 인증이 발생 합니다.

## <a name="secure-export-data-flow"></a>보안 내보내기 데이터 흐름

보안 내보내기 데이터 흐름 단계는 다음과 같습니다.

1) 보안 내보내기를 사용 하도록 구성 된 경고는 Azure Monitor에서 발생 합니다.
2) 경고 페이로드가 ITSM 도구에 보안 webhook 작업으로 전송 됩니다.
3) Itsm 응용 프로그램은 ITSM 도구를 시작할 수 있는 권한이 있는 경우 Azure AD를 사용 하 여 확인 합니다.
4) 경고에 대 한 권한이 부여 된 경우 응용 프로그램:
    1) ITSM 도구에서 작업 항목 (예: 인시던트)을 만듭니다.
    2) CI (구성 항목) ID를 CMDB (고객 관리 데이터베이스)에 바인딩합니다.
![Itsm 다이어그램](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>BMC Helix 연결

보안 내보내기는 BMC Helix을 지원 합니다. 통합의 몇 가지 이점은 다음과 같습니다.

* **향상 된 인증** – Azure AD는 itsmc에서 자주 발생 하는 시간 제한 없이 더 안전한 인증을 제공 합니다.
* **ITSM 도구에서 해결 된 경고** -메트릭 경고는 "발생" 및 "해결 됨" 상태를 구현 합니다. 조건을 충족 하는 경우 경고 상태는 "발생"입니다. 조건이 더 이상 충족 되지 않을 경우 경고 상태는 "해결 됨"입니다. ITSMC에서 경고를 자동으로 해결할 수 없습니다. 보안 내보내기를 사용 하면 해결 됨 상태가 ITSM 도구로 흘러 자동으로 업데이트 됩니다.
* **[Common Schema 허용](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** – itsmc에서 경고 페이로드의 스키마는 경고 유형에 따라 다릅니다. 보안 내보내기에서는 모든 경고 유형에 대 한 공통 스키마를 제공 합니다. 이 새 공통 스키마에는 모든 경고 유형에 대 한 CI가 포함 되어 있습니다. 모든 경고 유형은 CI를 CMDB에 바인딩할 수 있습니다.

다음 단계를 사용 하 여 ITSM 커넥터 사용을 시작 합니다.

1. Azure Active Directory에 앱을 등록 합니다.
2. 보안 webhook 작업 그룹을 만듭니다.
3. 파트너 환경을 구성 합니다.

## <a name="register-with-azure-active-directory"></a>Azure Active Directory 등록

Azure Active Directory에 Azure AD 응용 프로그램을 등록 하려면 다음 단계를 따르세요.

1) [Azure AD 만들기](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) Azure Active Directory에서 "응용 프로그램 공개"를 선택 합니다.
3) 응용 프로그램 ID URI [ ![ Azure AD](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox) 에서 설정을 선택 합니다.
4) 저장을 클릭합니다.

## <a name="create-a-secure-webhook-action-group"></a>보안 Webhook 작업 그룹 만들기

Azure AD를 등록 한 후에는 작업 그룹의 보안 Webhook 작업을 사용 하 여 Azure 경고에 따라 ITSM 도구에서 작업 항목을 만들 수 있습니다.
작업 그룹은 Azure Alerts를 위해 모듈 방식으로 다시 사용할 수 있는 방법을 제공합니다. Azure Portal에서 메트릭 경고, 활동 로그 경고 및 Azure Log Analytics 알림과 함께 작업 그룹을 사용할 수 있습니다.
작업 그룹에 대해 자세히 알아보려면 [Azure Portal에서 작업 그룹 만들기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)를 참조하세요.
이렇게 하려면 다음 절차를 수행합니다.

BMC Helix 환경에서:

1. Integration Studio에 로그인 합니다.
2. Azure 경고 흐름에서 인시던트 만들기를 검색 합니다.
3. WebHook URL을 복사 합니다.
![BMC URL](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

작업에 webhook를 추가 하려면 보안 webhook에 대 한 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com/)에서 **모니터**를 검색하여 선택합니다. **모니터** 창은 모든 모니터링 설정과 데이터를 하나의 보기로 통합합니다.
2. **경고**를 선택한 다음, **작업 관리**를 선택합니다.
3. [작업 그룹 추가](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)를 선택하고 필드를 입력합니다.
4. **작업 그룹 이름** 상자에 이름을 입력하고 **약식 이름** 상자에 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.
5. **보안 Webhook** 선택
6. 세부 정보 편집을 선택 합니다. 다음 이미지는 샘플 보안 webhook 작업을 보여 줍니다.
    1. 등록 한 Azure Active Directory의 올바른 개체 ID를 선택 합니다.
    2. "BMC Helix 환경"에서 복사한 WebHook URL을 URI 필드에 붙여넣습니다.
    3. **일반적인 경고 스키마** 를 **예**로 설정 합니다. 
7. 다음 이미지는 ![ 보안 webhook 보안 webhook 작업 구성을 보여 줍니다.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>파트너 환경 구성

### <a name="connect-bmc-helix-to-azure-monitor"></a>BMC Helix Azure Monitor에 연결

다음 섹션에서는 Azure에서 BMC Helix 제품 및 보안 내보내기를 연결 하는 방법에 대해 자세히 설명 합니다.

### <a name="prerequisites"></a>사전 요구 사항

다음 필수 조건이 충족되는지 확인합니다.

* Azure AD가 등록 됩니다.
* 지원 되는 버전의 BMC Helix 다중 클라우드 서비스 관리: 20.02 이상 버전

BMC Helix 연결을 구성 하려면:

1) [버전 20.2에 대 한 Azure Monitor와 미리 작성 된 통합 사용](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) BMC Helix 연결 구성의 일부로 통합 BMC 인스턴스로 이동 하 여 지침을 따릅니다.

1. **카탈로그** 선택
2. **Azure 경고** 선택
3. **커넥터** 선택
4. **구성** 선택
5. **새 연결 구성 추가** 를 선택 합니다.
6. 구성 섹션에 대 한 정보를 채웁니다.
    1. **이름** -직접 구성
    2. **권한 부여 유형** -없음
    3. **설명**-직접 구성
    4. **사이트**-클라우드
    5. **인스턴스 수** -2 – 기본값
    6. **선택-기본적** 으로 선택 하 고 사용을 설정 합니다.
    7. Azure 테 넌 트 ID, Azure 응용 프로그램 ID는 "생성 된 Azure Active Directory" 단계에 정의 된 응용 프로그램에서 가져옵니다.
![BMC 구성](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>다음 단계

* [Azure 경고에서 ITSM 작업 항목 만들기](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
