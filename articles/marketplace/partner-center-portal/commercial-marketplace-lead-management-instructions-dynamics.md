---
title: Dynamics 365 Customer Engagement의 잠재 고객 관리 - Microsoft 상업용 마켓플레이스
description: Dynamics 365 Customer Engagement를 설정하여 Microsoft AppSource 및 Azure Marketplace에서 잠재 고객을 관리하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 92f4bde31af2a7bb4c658141964f238a8c077eef
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120381"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Dynamics 365 Customer Engagement에 대해 잠재 고객 관리 구성

이 문서에서는 Dynamics 365 Customer Engagement(이전 명칭: Dynamics CRM Online)를 설정하는 방법을 설명합니다. [Customer Engagement 및 SharePoint Online에서 서버 기반 인증 구성](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)의 변경 사항에서 상업용 마켓플레이스 제품에서 잠재 고객을 처리하는 방법을 자세히 알아보세요.

>[!NOTE]
>여기에 나와 있는 지침은 Dynamics 365 Customer Engagement용 Microsoft 호스트 클라우드 환경에 해당하는 내용입니다. 현재 Dynamics 온-프레미스 환경에 직접 연결하는 것은 지원되지 않습니다. [HTTPS 엔드포인트](./commercial-marketplace-lead-management-instructions-https.md)나 [Azure 테이블](./commercial-marketplace-lead-management-instructions-azure-table.md)을 구성하는 것과 같이 잠재 고객을 받을 수 있는 다른 옵션이 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 사용자 권한이 필요합니다.

* Dynamics 365 Customer Engagement 인스턴스에 대한 관리자 권한 - 솔루션을 설치하는 데 필요.
* 테넌트 관리자 권한 - 상업용 마켓플레이스 제품에서 잠재 고객을 보내는 데 사용되는 잠재 고객 서비스의 신규 서비스 계정을 만드는 데 필요.
* Office 365 관리 포털에 대한 액세스 권한.
* Azure Portal에 대한 액세스 권한.

## <a name="install-the-solution"></a>솔루션 설치

1. [Microsoft Marketplace 잠재 고객 작성기 솔루션](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)을 다운로드하고 컴퓨터에 로컬로 저장합니다.

1. Dynamics 인스턴스의 URL(예: `https://tenant.crm.dynamics.com`)로 이동하여 Dynamics 365 Customer Engagement를 엽니다.

1. 위쪽 표시줄에서 기어 아이콘을 선택하고 **고급 설정**을 선택합니다.
 
    ![Dynamics 365 고급 설정 메뉴 항목](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. **설정** 페이지에서 위쪽 표시줄의 **설정** 메뉴를 열고 **솔루션**을 선택합니다.

    >[!NOTE]
    >다음 화면에 나와 있는 옵션이 표시되지 않는다면 이 작업을 진행하는 데 필요한 권한이 없는 것입니다. Dynamics 365 Customer Engagement 인스턴스의 관리자에게 문의하세요.

    ![Dynamics 365 솔루션 옵션](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. **솔루션** 페이지에서 **가져오기**를 선택한 다음 1단계에서 **Microsoft Marketplace 잠재 고객 작성기** 솔루션을 저장한 곳으로 이동합니다.

    ![가져오기 단추](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. 솔루션 가져오기 마법사에 따라 솔루션 가져오기를 완료합니다.

## <a name="configure-user-permissions"></a>사용자 권한 구성

Dynamics 365 Customer Engagement 인스턴스에 잠재 고객을 작성하려면 Microsoft와 서비스 계정을 공유하고 해당 계정의 사용 권한을 구성해야 합니다.

다음 단계를 사용하여 서비스 계정을 만들고 사용 권한을 할당합니다. Azure Active Directory 또는 Office 365를 사용할 수 있습니다.

>[!NOTE]
>선택한 인증 옵션에 따라 해당 지침으로 건너뛰세요. [Azure Active Directory](#azure-active-directory) 또는 [Office 365](#office-365)를 참조하세요.

### <a name="azure-active-directory"></a>Azure Active Directory

잠재 고객을 계속 가져오기 위해 사용자 이름 또는 암호를 업데이트할 필요가 없는 이 옵션을 사용하는 것이 좋습니다. Azure Active Directory 옵션을 사용하려면 Active Directory 애플리케이션의 앱 ID, 애플리케이션 키 및 디렉터리 ID를 제공합니다.

Dynamics 365 Customer Engagement에 대해 Azure Active Directory를 구성하려면:

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

1. **속성**을 선택하고 **디렉터리 속성** 페이지에 표시된 **디렉터리 ID** 값을 복사해 둡니다. 이 값은 마켓플레이스 제품에 대한 잠재 고객을 받기 위해 게시 포털에서 입력해야 하므로 저장해 두세요.

    ![Azure Active Directory 속성 메뉴 항목](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Azure Active Directory 왼쪽 창에서 **앱 등록**을 선택하고 이 페이지에서 **새 등록**을 선택합니다.
1. 애플리케이션 이름으로 의미 있는 이름을 입력합니다.
1. **지원되는 계정 유형**에서 **모든 조직 디렉터리의 계정**을 선택합니다.
1. **리디렉션 URI(선택 사항)** 에서 **웹**을 URI(예: `https://contosoapp1/auth`)를 입력합니다. 
1. **등록**을 선택합니다.

    ![애플리케이션 페이지 등록](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. 애플리케이션이 등록되었으므로 애플리케이션의 개요 페이지에 액세스합니다. 이 페이지에 표시되는 **애플리케이션(클라이언트) ID** 값을 복사해 둡니다. 이 값은 마켓플레이스 제품에 대한 잠재 고객을 받기 위해 게시 포털과 Dynamics 365에서 입력해야 하므로 저장해 두세요.

    ![애플리케이션(클라이언트) ID 상자](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. 앱의 왼쪽 창에서 **인증서 및 암호**를 선택하고 **새 클라이언트 암호** 단추를 선택합니다. 클라이언트 암호에 대한 의미 있는 설명을 입력하고 **만료** 아래에서 **안 함** 옵션을 선택합니다. **추가**를 선택하여 클라이언트 암호를 만듭니다.

    ![인증서 및 암호 메뉴 항목](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. 클라이언트 암호가 생성되면 **클라이언트 암호** 값을 복사해 둡니다. 이 페이지를 나가면 값을 검색할 수 없습니다. 이 값은 마켓플레이스 제품에 대한 잠재 고객을 받기 위해 게시 포털에서 입력해야 하므로 저장해 두세요. 
1. 앱의 왼쪽 창에서 **API 사용 권한**을 선택하고 **+ 사용 권한 추가**를 선택합니다.
1. **Microsoft API**를 선택하고 API로 **Dynamics CRM**을 선택합니다.
1. **애플리케이션에 어떤 유형의 권한이 필요한가요?** 아래에서 **위임된 권한**이 선택되어 있는지 확인합니다. 
1. **사용 권한** 아래에서 **Common Data Service에 조직 사용자로 액세스**의 **user_impersonation** 확인란을 선택합니다. **사용 권한 추가**를 선택합니다.

    ![사용 권한 추가 단추](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Azure Portal에서 1~14단계를 완료한 후에는 URL(예: `https://tenant.crm.dynamics.com`)로 이동하여 Dynamics 365 Customer Engagement 인스턴스로 이동합니다.
1. 위쪽 표시줄에서 기어 아이콘을 선택하고 **고급 설정**을 선택합니다.
1. **설정** 페이지에서 위쪽 표시줄의 **설정** 메뉴를 열고 **보안**을 선택합니다.
1. **보안** 페이지에서 **사용자**를 선택합니다. **사용자** 페이지에서 **활성 사용자** 드롭다운 메뉴를 선택하고 **애플리케이션 사용자**를 선택합니다.
1. 새 사용자를 만들려면 **새로 만들기**를 선택합니다. 

    ![새 사용자 만들기](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. **새 사용자** 창에서 **사용자: 애플리케이션 사용자**가 선택되었는지 확인합니다. 이 연결에 사용하려는 사용자의 사용자 이름, 전체 이름 및 메일 주소를 입력합니다. 8단계에서 Azure Portal에서 만든 앱의 **애플리케이션 ID**를 붙여넣습니다. **저장 후 닫기**를 선택하여 사용자 추가를 마칩니다.

    ![새 사용자 창](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. 이 사용자의 연결 구성을 마치려면 이 문서의 “보안 설정” 섹션으로 이동합니다.

### <a name="office-365"></a>Office 365

Azure Active Directory를 사용하지 않으려면 Microsoft 365 관리 센터에서 새 사용자를 등록할 수 있습니다. 잠재 고객을 계속 가져오려면 90일마다 사용자 이름 및 암호를 업데이트해야 합니다.

Dynamics 365 Customer Engagement에 대해 Office 365를 구성하려면:

1. [Microsoft 365 관리 센터](https://admin.microsoft.com)에 로그인합니다.

1. **사용자 추가**를 선택합니다.

    ![Microsoft 365 관리 센터 사용자 추가 옵션](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. 리드 기록기 서비스에 대한 새 사용자를 만듭니다. 다음 설정을 구성합니다.

    * 사용자 이름을 입력합니다.
    * 암호를 입력하고 **이 사용자가 처음 로그인할 때 암호를 변경하도록 설정** 옵션의 선택을 취소합니다.
    * 사용자 역할로 **사용자(관리자 액세스 권한 없음)** 를 선택합니다.
    * 아래 화면에 표시된 것처럼 제품 라이선스로 **Dynamics 365 Customer Engagement 플랜**을 선택합니다. 선택한 라이선스에 대한 요금이 청구됩니다. 

마켓플레이스 제품에 대한 잠재 고객을 받기 위해 게시 포털에서 **사용자 이름** 및 **암호** 값을 입력해야 하므로 이 값을 저장해 두세요.

![Microsoft 365 관리 센터 새 사용자 창](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>보안 설정

마지막 단계는 앞에서 만든 사용자가 잠재 고객을 작성할 수 있도록 설정하는 것입니다.

1. Dynamics 인스턴스의 URL(예: `https://tenant.crm.dynamics.com`)로 이동하여 Dynamics 365 Customer Engagement를 엽니다.
1. 위쪽 표시줄에서 기어 아이콘을 선택하고 **고급 설정**을 선택합니다.
1. **설정** 페이지에서 위쪽 표시줄의 **설정** 메뉴를 열고 **보안**을 선택합니다.
1. **보안** 페이지에서 **사용자**를 선택하고 이 문서의 “사용자 권한 구성” 섹션에서 만든 사용자를 선택합니다. 그런 다음 **역할 관리**를 선택합니다. 

    ![역할 관리 탭](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. 역할 이름 **Microsoft Marketplace 잠재 고객 작성기**를 검색하고 선택하여 사용자에게 이 역할을 할당합니다.

    ![사용자 역할 관리 창](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >이 역할은 사용자가 가져온 솔루션에서 생성되며 잠재 고객을 기록하고 호환성을 위해 솔루션 버전을 추적하는 권한만 갖습니다.

1. **보안** 페이지로 돌아가서 **보안 역할**을 선택합니다. **Microsoft Marketplace 잠재 고객 작성기** 역할을 검색하여 선택합니다.

    ![보안 역할 창](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. 보안 역할에서 **핵심 레코드** 탭을 선택합니다. **사용자 엔터티 UI 설정** 항목을 검색합니다. 만들기, 읽기, 쓰기 사용 권한의 원을 한 번씩 클릭하여 해당 엔터티의 사용자(1/4 노란색 원)에 대해 만들기, 읽기, 쓰기 권한을 설정합니다.

    ![Microsoft Marketplace 리드 작성기 핵심 레코드 탭](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. **사용자 지정** 탭에서 **시스템 작업** 항목을 검색합니다. 읽기, 쓰기, AppendTo 권한의 원을 네 번씩 클릭하여 해당 엔터티의 조직(단색 녹색 원)에 대해 읽기, 쓰기, AppendTo 권한을 설정합니다.

    ![Microsoft Marketplace 잠재 고객 작성기 사용자 지정 탭](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. **저장 후 닫기**를 선택합니다.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Dynamics 365 Customer Engagement로 잠재 고객을 보내도록 제품 구성 

게시 포털에서 제품에 대해 잠재 고객 관리 정보를 구성하려면:

1. 제품의 **제품 설정** 페이지로 이동합니다.
1. **잠재 고객** 섹션에서 **연결**을 선택합니다.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="잠재 고객":::

1. 연결 세부 정보 팝업 창에서 잠재 고객 대상으로 **Dynamics 365 Customer Engagement**를 선택합니다.

    ![잠재 고객 대상 상자](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Dynamics 365 인스턴스의 **URL**(예: `https://contoso.crm4.dynamics.com`)을 입력합니다.

1. **인증 방법**을 Azure Active Directory 또는 Office 365로 선택합니다. 
1. **Azure Active Directory**를 선택한 경우 **애플리케이션(클라이언트) ID**(예: `23456052-aaaa-bbbb-8662-1234df56788f`), **디렉터리 ID**(예: `12345678-8af1-4asf-1234-12234d01db47`) 및 **클라이언트 암호**(예: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`)를 입력합니다.

    ![인증으로 Azure Active Directory가 선택된 모습](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. **Office 365**를 선택한 경우 **사용자 이름**(예: `contoso@contoso.onmicrosoft.com`) 및 **암호**(예: `P@ssw0rd`)를 입력합니다.

    ![Office 365 사용자 이름 상자](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. **연락처 메일**에 새 잠재 고객이 수신되었을 때 메일 알림을 받을 회사 사용자의 메일 주소를 입력합니다. 세미콜론으로 구분하여 여러 메일 주소를 입력할 수 있습니다.
1. **확인**을 선택합니다.

잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 **유효성 검사** 단추를 선택합니다. 성공하면 잠재 고객 대상에 테스트 잠재 고객이 생깁니다.

![연락처 메일 상자](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>제품에 대한 잠재 고객을 받으려면 먼저 제품의 나머지 부분에 대해 구성을 끝낸 후에 게시해야 합니다.
