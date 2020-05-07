---
title: Dynamics 365 고객 참여를 위한 리드 관리-Microsoft 상업적 marketplace
description: Microsoft AppSource 및 Azure Marketplace에서 리드를 관리 하도록 Dynamics 365 고객 Engagement를 설정 하는 방법을 알아봅니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 985b3258eb0b957242d529945f32ed9704a91e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791003"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Dynamics 365 고객 Engagement에 대 한 리드 관리 구성

이 문서에서는 dynamics 365 고객 참여를 설정 하는 방법을 설명 합니다 (이전에는 Dynamics CRM Online 이라고 명명). 상업적 marketplace 제품의 판매 잠재 고객을 처리 하려면 [고객 참여 및 SharePoint Online으로 서버 기반 인증 구성](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) 의 변경 내용에 대해 자세히 알아보세요.

>[!NOTE]
>이러한 지침은 Dynamics 365 고객 참여를 위한 Microsoft 호스팅 클라우드 환경에만 적용 됩니다. Dynamics 온-프레미스 환경에 직접 연결 하는 것은 현재 지원 되지 않습니다. 예를 들어 [HTTPS 끝점](./commercial-marketplace-lead-management-instructions-https.md) 또는 [Azure 테이블](./commercial-marketplace-lead-management-instructions-azure-table.md)을 구성 하는 등의 다른 옵션을 통해 잠재 고객을 받을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료 하려면 다음 사용자 권한이 필요 합니다.

* 솔루션을 설치할 수 있는 Dynamics 365 고객 Engagement 인스턴스에 대 한 관리자 권한
* 상업적 marketplace 제품에서 잠재 고객을 보내는 데 사용 되는 잠재 고객 서비스에 대 한 새 서비스 계정을 만들 수 있는 테 넌 트 관리자 권한입니다.
* Office 365 관리 포털에 액세스 합니다.
* Azure Portal에 대 한 액세스.

## <a name="install-the-solution"></a>솔루션 설치

1. [Microsoft Marketplace 리드 기록기 솔루션](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)을 다운로드 하 고 컴퓨터에 로컬로 저장 합니다.

1. Dynamics 인스턴스의 URL (예:)로 이동 하 여 Dynamics 365 Customer Engagement를 엽니다 `https://tenant.crm.dynamics.com`.

1. 위쪽 막대에서 기어 아이콘을 선택 하 고 **고급 설정**을 선택 합니다.
 
    ![Dynamics 365 고급 설정 메뉴 항목](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. **설정** 페이지에서 위쪽 표시줄의 **설정** 메뉴를 열고 **솔루션**을 선택 합니다.

    >[!NOTE]
    >다음 화면에 옵션이 표시 되지 않으면 계속 하는 데 필요한 권한이 없습니다. Dynamics 365 고객 참여 인스턴스의 관리자에 게 문의 하세요.

    ![Dynamics 365 솔루션 옵션](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. **솔루션** 페이지에서 **가져오기** 를 선택 하 고 1 단계에서 다운로드 한 **Microsoft Marketplace 잠재 고객 기록기** 솔루션을 저장 한 위치로 이동 합니다.

    ![가져오기 단추](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. 솔루션 가져오기 마법사를 따라 솔루션 가져오기를 완료 합니다.

## <a name="configure-user-permissions"></a>사용자 권한 구성

Dynamics 365 고객 Engagement 인스턴스에 리드를 쓰려면 서비스 계정을 Microsoft와 공유 하 고 계정에 대 한 사용 권한을 구성 해야 합니다.

다음 단계를 사용하여 서비스 계정을 만들고 사용 권한을 할당합니다. Azure Active Directory 또는 Office 365를 사용할 수 있습니다.

>[!NOTE]
>선택한 인증 옵션에 따라 해당 지침으로 건너뜁니다. [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) 또는 [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)을 참조 하세요.

### <a name="azure-active-directory"></a>Azure Active Directory

잠재 고객을 계속 받을 수 있도록 사용자 이름 또는 암호를 업데이트할 필요가 없기 때문에이 옵션을 권장 합니다. Azure Active Directory 옵션을 사용 하려면 Active Directory 응용 프로그램에서 앱 ID, 응용 프로그램 키 및 디렉터리 ID를 제공 합니다.

Dynamics 365 고객 Engagement에 대 한 Azure Active Directory를 구성 하려면:

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

1. **속성**을 선택 하 고 **디렉터리 속성** 페이지에서 **디렉터리 ID** 값을 복사 합니다. Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에 제공 해야 하기 때문에이 값을 저장 합니다.

    ![Azure Active Directory 속성 메뉴 항목](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Azure Active Directory 왼쪽 창에서 **앱 등록** 을 선택 하 고 해당 페이지에서 **새 등록** 을 선택 합니다.
1. 응용 프로그램 이름에 대 한 의미 있는 이름을 입력 합니다.
1. **지원 되는 계정 유형**아래에서 **조직 디렉터리의 계정**을 선택 합니다.
1. **Uri 리디렉션 (선택 사항)** 에서 **웹** 을 선택 하 고 URI를 입력 `https://contosoapp1/auth`합니다 (예:). 
1. **등록**을 선택합니다.

    ![응용 프로그램 등록 페이지](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. 이제 응용 프로그램이 등록 되었으므로 응용 프로그램의 개요 페이지에 액세스 합니다. 해당 페이지에서 **응용 프로그램 (클라이언트) ID** 값을 복사 합니다. Marketplace 제품에 대 한 잠재 고객을 받으려면 게시 포털 및 Dynamics 365에서이 값을 제공 해야 하기 때문에이 값을 저장 합니다.

    ![응용 프로그램 (클라이언트) ID 상자](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. 앱의 왼쪽 창에서 **인증서 & 암호** 를 선택 하 고 **새 클라이언트 암호** 단추를 선택 합니다. 클라이언트 암호에 대 한 의미 있는 설명을 입력 하 고 **만료**에서 **안 함** 옵션을 선택 합니다. **추가** 를 선택 하 여 클라이언트 암호를 만듭니다.

    ![인증서 & 비밀 메뉴 항목](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. 클라이언트 암호를 성공적으로 만든 후에 **클라이언트 암호** 값을 복사 합니다. 페이지를 나간 후에는 값을 검색할 수 없습니다. Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에 제공 해야 하기 때문에이 값을 저장 합니다. 
1. 앱의 왼쪽 창에서 **API 권한** 을 선택 하 고 **+ 권한 추가**를 선택 합니다.
1. **Microsoft api**를 선택 하 고 **DYNAMICS CRM** 을 API로 선택 합니다.
1. **응용 프로그램에 필요한 사용 권한 유형**에서 **위임 된 권한** 이 선택 되어 있는지 확인 합니다. 
1. **권한**아래에서 **조직 사용자로 Common Data Service 액세스**에 대 한 **user_impersonation** 확인란을 선택 합니다. 그런 다음 **권한 추가**를 선택합니다.

    ![권한 추가 단추](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Azure Portal에서 1 ~ 14 단계를 완료 한 후에는 URL (예:)로 이동 하 여 Dynamics 365 Customer Engagement 인스턴스로 이동 `https://tenant.crm.dynamics.com`합니다.
1. 위쪽 막대에서 기어 아이콘을 선택 하 고 **고급 설정**을 선택 합니다.
1. **설정** 페이지에서 위쪽 표시줄의 **설정** 메뉴를 열고 **보안**을 선택 합니다.
1. **보안** 페이지에서 **사용자**를 선택 합니다. **사용자** 페이지에서 **사용 하도록 설정 된 사용자** 드롭다운을 선택 하 고 **응용 프로그램 사용자**를 선택 합니다.
1. 새 사용자를 만들려면 **새로 만들기**를 선택합니다. 

    ![새 사용자 만들기](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. **새 사용자** 창에서 **사용자: 응용 프로그램 사용자** 가 선택 되어 있는지 확인 합니다. 이 연결에 사용 하려는 사용자의 사용자 이름, 전체 이름 및 전자 메일 주소를 제공 합니다. 또한 8 단계에서 만든 Azure Portal에서 만든 앱의 **응용 프로그램 ID** 를 붙여 넣습니다. **저장 & 닫기** 를 선택 하 여 사용자 추가를 완료 합니다.

    ![새 사용자 창](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. 이 문서의 "보안 설정" 섹션으로 이동 하 여이 사용자에 대 한 연결 구성을 완료 합니다.

### <a name="office-365"></a>Office 365

Azure Active Directory 사용 하지 않으려는 경우 Microsoft 365 관리 센터에서 새 사용자를 등록할 수 있습니다. 잠재 고객을 계속 받기 위해 90 일 마다 사용자 이름 및 암호를 업데이트 해야 합니다.

Dynamics 365 고객 Engagement에 대해 Office 365을 구성 하려면:

1. [Microsoft 365 관리 센터](https://admin.microsoft.com)에 로그인합니다.

1. **사용자 추가**를 선택합니다.

    ![Microsoft 365 관리 센터 사용자 추가 옵션](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. 리드 기록기 서비스에 대한 새 사용자를 만듭니다. 다음 설정을 구성합니다.

    * 사용자 이름을 입력 합니다.
    * 암호를 입력 하 고 **이 사용자가 처음 로그인 할 때 암호를 변경 하도록 설정** 합니다. 옵션을 선택 취소 합니다.
    * 사용자에 대 한 역할로 **사용자 (관리자 액세스 권한 없음)** 를 선택 합니다.
    * 다음 화면에 표시 된 것 처럼 **Dynamics 365 Customer Engagement 요금제** 를 제품 라이선스로 선택 합니다. 선택한 라이선스에 대한 요금이 청구됩니다. 

Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에서 **사용자 이름** 및 **암호** 값을 제공 해야 하기 때문에 이러한 값을 저장 합니다.

![Microsoft 365 관리 센터 새 사용자 창](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>보안 설정

마지막 단계는 만든 사용자가 리드를 작성 하도록 설정 하는 것입니다.

1. Dynamics 인스턴스의 URL (예:)로 이동 하 여 Dynamics 365 Customer Engagement를 엽니다 `https://tenant.crm.dynamics.com`.
1. 위쪽 막대에서 기어 아이콘을 선택 하 고 **고급 설정**을 선택 합니다.
1. **설정** 페이지에서 위쪽 표시줄의 **설정** 메뉴를 열고 **보안**을 선택 합니다.
1. **보안** 페이지에서 **사용자** 를 선택 하 고이 문서의 "사용자 권한 구성" 섹션에서 만든 사용자를 선택 합니다. 그런 다음 **역할 관리**를 선택 합니다. 

    ![역할 관리 탭](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. 역할 이름 **Microsoft Marketplace 리드 기록기**를 검색 하 고 선택 하 여 사용자에 게 역할을 할당 합니다.

    ![사용자 역할 관리 창](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >이 역할은 사용자가 가져온 솔루션에서 생성되며 잠재 고객을 기록하고 호환성을 위해 솔루션 버전을 추적하는 권한만 갖습니다.

1. **보안** 페이지로 돌아가서 **보안 역할**을 선택 합니다. 역할 **Microsoft Marketplace 잠재 고객 기록기**를 검색 하 고 선택 합니다.

    ![보안 역할 창](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. 보안 역할에서 **핵심 레코드** 탭을 선택 합니다. **사용자 엔터티 UI 설정** 항목을 검색 합니다. 해당 하는 각 원에서 한 번 클릭 하 여 해당 엔터티에 대 한 사용자 (1/4 노란색 원)에 대해 만들기, 읽기 및 쓰기 권한을 설정 합니다.

    ![Microsoft Marketplace 리드 기록기 코어 레코드 탭](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. **사용자 지정** 탭에서 **시스템 작업** 항목을 검색 합니다. 해당 하는 각 원에서 네 번 클릭 하 여 해당 엔터티에 대 한 읽기, 쓰기 및 AppendTo 권한을 조직 (단색 녹색 원)으로 설정 합니다.

    ![Microsoft Marketplace 리드 기록기 사용자 지정 탭](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. **저장 후 닫기**를 선택합니다.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Dynamics 365 고객 Engagement에 잠재 고객을 보내도록 제품 구성 

게시 포털에서 제품에 대 한 리드 관리 정보를 구성 하려면 다음을 수행 합니다.

1. 제품에 대 한 **제품 설정** 페이지로 이동 합니다.
1. **리드 관리** 섹션에서 **연결** 을 선택 합니다.

    ![리드 관리 섹션 연결 단추](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

1. 연결 세부 정보 팝업 창에서 **Dynamics 365 고객 Engagement** 를 선택 하 여 잠재 고객을 대상으로 합니다.

    ![리드 대상 상자](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Dynamics 365 인스턴스의 **URL** (예:)을 입력 합니다 `https://contoso.crm4.dynamics.com`.

1. Azure Active Directory 또는 Office 365 중에서 **인증**방법을 선택 합니다. 
1. **Azure Active Directory**를 선택한 경우 **응용 프로그램 (클라이언트) id** ( `23456052-aaaa-bbbb-8662-1234df56788f`예:), **디렉터리 id** ( `12345678-8af1-4asf-1234-12234d01db47`예:) 및 **클라이언트 비밀** (예: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`)을 입력 합니다.

    ![Azure Active Directory 선택 된 인증](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. **Office 365**을 선택한 경우 **사용자 이름** (예: `contoso@contoso.onmicrosoft.com`)을 입력 하 고 **암호** 를 입력 합니다 (예 `P@ssw0rd`:).

    ![Office 365 사용자 이름 상자](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. **연락처 전자 메일**에는 새 잠재 고객이 수신 될 때 전자 메일 알림을 받을 회사의 사용자에 대 한 전자 메일 주소를 입력 합니다. 세미콜론으로 구분 하 여 여러 전자 메일 주소를 입력할 수 있습니다.
1. **확인**을 선택합니다.

선행 대상에 성공적으로 연결 되었는지 확인 하려면 **유효성 검사** 단추를 선택 합니다. 성공 하면 잠재 고객 대상에 테스트 리드를 갖게 됩니다.

![연락처 전자 메일 상자](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다.
