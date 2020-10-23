---
title: Azure Monitor에서 IT 서비스 관리 커넥터-보안 내보내기
description: 이 문서에서는 itsm 제품/서비스를 Azure Monitor의 보안 내보내기와 연결 하 여 ITSM 작업 항목을 중앙에서 모니터링 하 고 관리 하는 방법을 보여 줍니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 64d45861f37e2015b747a4db0feb2d32e68fe893
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427323"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>보안 내보내기를 사용 하 여 Azure를 ITSM 도구에 연결

이 문서에서는 보안 내보내기를 사용 하 여 ITSM (IT Service Management) 제품 또는 서비스 간 연결을 구성 하는 방법을 보여 줍니다.

보안 내보내기는 [ITSMC (IT 서비스 관리 커넥터](./itsmc-overview.md)의 업데이트 된 버전입니다. 두 버전 모두 Azure Monitor에서 경고를 보낼 때 ITSM 도구에서 작업 항목을 만들 수 있습니다. 이 기능에는 메트릭, 로그 및 활동 로그 경고가 포함 됩니다.

ITSMC는 사용자 이름 및 암호 자격 증명을 사용 합니다. 보안 내보내기는 Azure Active Directory (Azure AD)를 사용 하기 때문에 더 강력한 인증이 있습니다. Azure AD는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다. 사용자가 로그인 하 고 내부 또는 외부 리소스에 액세스할 수 있습니다. ITSM에서 Azure AD를 사용 하면 외부 시스템으로 보낸 azure 경고 (Azure AD 응용 프로그램 ID를 통해)를 식별할 수 있습니다.

> [!NOTE]
> 보안 내보내기를 사용 하 여 Azure를 ITSM 도구에 연결 하는 기능은 미리 보기 상태입니다.

## <a name="secure-export-architecture"></a>보안 내보내기 아키텍처

보안 내보내기 아키텍처에는 다음과 같은 새로운 기능이 도입 되었습니다.

* **새 작업 그룹**: itsm가 사용 하는 itsm 작업 그룹 대신 보안 Webhook 작업 그룹을 통해 itsm 도구에 경고를 보냅니다.
* **AZURE ad 인증**: 인증은 사용자 이름/암호 자격 증명 대신 azure ad를 통해 수행 됩니다.

## <a name="secure-export-data-flow"></a>보안 내보내기 데이터 흐름

보안 내보내기 데이터 흐름의 단계는 다음과 같습니다.

1. Azure Monitor는 보안 내보내기를 사용 하도록 구성 된 경고를 보냅니다.
2. 경고 페이로드는 보안 Webhook 작업을 통해 ITSM 도구에 전송 됩니다.
3. Itsm 응용 프로그램은 ITSM 도구를 시작할 수 있는 권한이 있는 경우 Azure AD를 사용 하 여 확인 합니다.
4. 경고에 권한이 부여 되 면 응용 프로그램은 다음을 수행 합니다.
   
   1. ITSM 도구에서 작업 항목 (예: 인시던트)을 만듭니다.
   2. CI (구성 항목)의 ID를 CMDB (고객 관리 데이터베이스)에 바인딩합니다.

![ITSM 도구가 Azure A D, Azure 경고 및 작업 그룹과 통신 하는 방법을 보여 주는 다이어그램입니다.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>보안 내보내기의 이점

통합의 주요 이점은 다음과 같습니다.

* **향상 된 인증**: Azure AD는 itsmc에서 자주 발생 하는 시간 제한 없이 더 안전한 인증을 제공 합니다.
* **ITSM 도구에서 해결 된 경고**: 메트릭 경고는 "발생" 및 "해결 됨" 상태를 구현 합니다. 조건을 충족 하는 경우 경고 상태는 "발생"입니다. 조건이 더 이상 충족 되지 않을 경우 경고 상태는 "해결 됨"입니다. ITSMC에서는 경고를 자동으로 해결할 수 없습니다. 보안 내보내기를 사용 하면 해결 됨 상태가 ITSM 도구로 흘러 자동으로 업데이트 됩니다.
* **[일반적인 경고 스키마](./alerts-common-schema.md)**: itsmc에서 경고 페이로드의 스키마는 경고 유형에 따라 달라 집니다. 보안 내보내기에는 모든 경고 유형에 대 한 공통 스키마가 있습니다. 이 공통 스키마에는 모든 경고 유형에 대 한 CI가 포함 되어 있습니다. 모든 경고 유형은 CI를 CMDB에 바인딩할 수 있습니다.

다음 단계를 통해 ITSM 커넥터 도구 사용을 시작 합니다.

1. Azure AD에 앱을 등록합니다.
2. 보안 Webhook 작업 그룹을 만듭니다.
3. 파트너 환경을 구성 합니다. 

보안 내보내기는 다음 ITSM 도구와의 연결을 지원 합니다.
* [BMC Helix](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#connect-bmc-helix-to-azure-monitor)

## <a name="register-with-azure-active-directory"></a>Azure Active Directory 등록

Azure AD에 응용 프로그램을 등록 하려면 다음 단계를 따르세요.

1. [Microsoft id 플랫폼에 응용 프로그램 등록](../../active-directory/develop/quickstart-register-app.md)의 단계를 따릅니다.
2. Azure AD에서 **응용 프로그램 노출**을 선택 합니다.
3. **응용 프로그램 ID URI**에 대해 **설정** 을 선택 합니다.

   [![응용 프로그램 I D의 U R I를 설정 하는 옵션의 스크린샷](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. **저장**을 선택합니다.

## <a name="create-a-secure-webhook-action-group"></a>보안 Webhook 작업 그룹 만들기

응용 프로그램을 Azure AD에 등록 한 후에는 작업 그룹의 보안 Webhook 작업을 사용 하 여 Azure 경고에 따라 ITSM 도구에서 작업 항목을 만들 수 있습니다.

작업 그룹은 Azure 경고에 대 한 작업을 트리거하는 모듈식 및 재사용 가능한 방법을 제공 합니다. Azure Portal에서 메트릭 경고, 활동 로그 경고 및 Azure Log Analytics 경고와 함께 작업 그룹을 사용할 수 있습니다.
작업 그룹에 대해 자세히 알아보려면 [Azure Portal에서 작업 그룹 만들기 및 관리](./action-groups.md)를 참조하세요.

작업에 webhook를 추가 하려면 보안 Webhook에 대 한 다음 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com/)에서 **모니터**를 검색하여 선택합니다. **모니터** 창은 모든 모니터링 설정과 데이터를 하나의 보기로 통합합니다.
2. **경고**  >  **관리 작업**을 선택 합니다.
3. [작업 그룹 추가](./action-groups.md#create-an-action-group-by-using-the-azure-portal)를 선택하고 필드를 입력합니다.
4. **작업 그룹 이름** 상자에 이름을 입력하고 **약식 이름** 상자에 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.
5. **보안 Webhook**를 선택 합니다.
6. 다음 세부 정보를 선택 합니다.
   1. 등록 한 Azure Active Directory 인스턴스의 개체 ID를 선택 합니다.
   2. URI에 대해 [Itsm 도구 환경](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#configure-the-partner-environment)에서 복사한 webhook URL에 붙여넣습니다.
   3. **일반 경고 스키마 사용** 을 **예**로 설정 합니다. 

   다음 이미지는 샘플 보안 Webhook 작업의 구성을 보여 줍니다.

   ![보안 Webhook 작업을 보여 주는 스크린샷](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>ITSM 도구 환경 구성

구성에는 두 단계가 포함 됩니다.
1. 보안 내보내기 정의에 대 한 URI를 가져옵니다.
2. ITSM 도구의 흐름에 따라 정의 됩니다.

### <a name="connect-bmc-helix-to-azure-monitor"></a>BMC Helix Azure Monitor에 연결

다음 섹션에서는 Azure에서 BMC Helix 제품 및 보안 내보내기를 연결 하는 방법에 대 한 세부 정보를 제공 합니다.

### <a name="prerequisites"></a>필수 구성 요소

다음 필수 구성 요소를 충족 하는지 확인 합니다.

* Azure AD가 등록 됩니다.
* 지원 되는 버전의 BMC Helix 다중 클라우드 서비스 관리 (버전 19.08 이상)가 있습니다.

### <a name="configure-the-bmc-helix-connection"></a>BMC Helix 연결 구성

1. 보안 내보내기에 대 한 URI를 가져오기 위해 BMC Helix 환경에서 다음 절차를 사용 합니다.

   1. Integration Studio에 로그인 합니다.
   2. **Azure 경고 흐름에서 인시던트 만들기** 를 검색 합니다.
   3. Webhook URL을 복사 합니다.
   
   ![Integration Studio의 webhook U R L 스크린샷](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. 버전에 따라 다음 지침을 따르세요.
   * [버전 20.02에 대해 Azure Monitor와의 미리 작성 된 통합을 사용 하도록 설정](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)합니다.
   * [버전 19.11에 대해 Azure Monitor와의 미리 작성 된 통합을 사용 하도록 설정](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)합니다.

3. BMC Helix의 연결 구성의 일부로 통합 BMC 인스턴스로 이동 하 여 다음 지침을 따릅니다.

   1. **카탈로그**를 선택 합니다.
   2. **Azure alerts**를 선택 합니다.
   3. **커넥터**를 선택 합니다.
   4. **구성**을 선택 합니다.
   5. **새 연결 구성 추가** 를 선택 합니다.
   6. 구성 섹션에 대 한 정보를 입력 합니다.
      - **이름**: 직접 설정 합니다.
      - **권한 부여 유형**: **없음**
      - **설명**: 직접 작성 합니다.
      - **사이트**: **클라우드**
      - **인스턴스 수**: **2**(기본값)
      - **선택: 기본적**으로 사용 하도록 설정 합니다.
      - Azure 테 넌 트 ID 및 Azure 응용 프로그램 ID는 앞에서 정의한 응용 프로그램에서 가져옵니다.

![BMC 구성을 보여 주는 스크린샷](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>다음 단계

* [Azure 경고에서 ITSM 작업 항목 만들기](./itsmc-overview.md)
