---
title: 고객 참여를 위한 Dynamics 365의 리드 관리 | Azure 마켓플레이스
description: 고객 참여를 위해 Dynamics 365에 대한 잠재 고객 관리를 구성합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5b3e35b6d19905e3c5262dfea3e52511510c9ffe
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252785"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>고객 참여를 위해 Dynamics 365에 대한 잠재 고객 관리 구성

이 문서에서는 고객 참여(이전 Dynamics CRM Online)에 대해 Dynamics 365를 설정하는 방법을 설명하고, 마켓플레이스 오퍼에서 판매 잠재 고객을 처리하기 위한 변경 에 대해 자세히 [설명합니다.](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) 

>[!Note]
>이 지침은 고객 참여 환경에 대해 Microsoft 호스팅 클라우드 Dynamics 365에 대해 특정합니다. Dynamics on-prem 환경에 직접 연결하는 것은 현재 지원되지 않으며, 잠재 고객을 수신할 [https 끝점](./commercial-marketplace-lead-management-instructions-https.md) 또는 [Azure 테이블을](./commercial-marketplace-lead-management-instructions-azure-table.md) 구성하는 등의 잠재 고객을 받을 수 있는 다른 옵션이 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하는 데 다음 사용자 권한이 필요합니다.

* 솔루션을 설치하고 다음 지침을 따르려면 고객 참여 인스턴스의 Dynamics 365의 관리자여야 합니다.
* 마켓플레이스 오퍼에서 잠재 고객을 보내는 데 사용되는 잠재 고객 서비스에 대한 새 서비스 계정을 만들려면 테넌트 관리자여야 합니다.
* Office 365 관리 포털에 액세스할 수 있어야 합니다.
* Azure 포털에 액세스할 수 있어야 합니다.

## <a name="install-the-solution"></a>솔루션 설치

1.  Microsoft [마켓플레이스 수석 기록기 솔루션을](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) 다운로드하여 컴퓨터에 로컬로 저장합니다.

2.  동적 인스턴스의 URL(예: `https://tenant.crm.dynamics.com`)으로 이동하여 고객 참여를 위한 Dynamics 365를 엽니다.

3.  상단 탐색 모음에서 기어 아이콘과 **고급 설정을** 선택하여 설정에 액세스합니다.
 
    ![역학 - 고급 설정](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  설정 페이지에서 상단 탐색 모음의 설정 메뉴에 액세스하고 **해결**을 선택합니다.

    >[!Note]
    >다음 화면 캡처에 옵션이 표시되지 않으면 진행해야 하는 권한이 없습니다. Dynamics 365에서 고객 참여 인스턴스에 대해 관리자에게 문의하세요.

    ![역학 365 - 솔루션](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. 솔루션 페이지에서 **가져오기를** 선택하고 1단계에서 다운로드한 *Microsoft 마켓플레이스 잠재 고객* 용 기록기 솔루션을 저장한 곳으로 이동합니다.

    ![고객 참여를 위한 Dynamics 365 - 가져오기](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. 가져오기 솔루션 마법사를 따라 솔루션 가져오기를 완료합니다.

## <a name="configure-user-permissions"></a>사용자 권한 구성

고객 참여 인스턴스에 대한 Dynamics 365에 잠재 고객을 작성하려면 서비스 계정을 당사와 공유하고 계정에 대한 권한을 구성해야 합니다.

다음 단계를 사용하여 서비스 계정을 만들고 사용 권한을 할당합니다. **Azure Active Directory** 또는 **Office 365**를 사용할 수 있습니다.

>[!Note]
>선택한 인증 옵션에 따라 선택한 지침에 따라 해당 지침을 건너뛸 수 있습니다. [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) 또는 [Office 365를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)참조하십시오.

### <a name="azure-active-directory"></a>Azure Active Directory

잠재 고객을 계속 얻기 위해 사용자 이름/암호를 업데이트할 필요가 없다는 추가 이점을 얻을 수 있으므로 이 옵션을 권장합니다. Azure Active Directory 옵션을 사용하려면 Active Directory 응용 프로그램에서 앱 ID, 응용 프로그램 키 및 디렉터리 ID를 제공합니다.

다음 단계를 사용하여 고객 참여를 위해 동적 365에 대한 Azure Active Directory를 구성합니다.

1. [Azure 포털에](https://portal.azure.com/)로그인한 다음 왼쪽 탐색에서 Azure Active Directory 서비스를 선택합니다.

2. Azure Active Directory 왼쪽 탐색에서 **속성을** 선택하고 해당 페이지에서 **디렉터리 ID** 값을 복사합니다. 마켓플레이스 오퍼에 대한 잠재 고객을 받기 위해 게시 포털에 제공해야 하는 *디렉터리 ID* 값이기 때문에 이 값을 저장합니다.

    ![Azure 활성 디렉터리 - 속성](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Azure Active Directory 왼쪽 탐색에서 **앱 등록을** 선택한 다음 해당 페이지에서 **새 등록을** 선택합니다.
4. 응용 프로그램 이름에 대한 이름을 입력합니다. 의미 있는 응용 프로그램 이름을 제공합니다.
5. 지원되는 계정 **유형에서 조직 디렉터리에서 계정을**선택합니다.
6. 리디렉션 URI에서 **웹을** 선택하고 URI(예:)를 `https://contosoapp1/auth`제공합니다. 
7. **등록**을 선택합니다.

    ![애플리케이션 등록](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. 이제 응용 프로그램이 등록되었으므로 응용 프로그램의 개요 페이지에 액세스하고 해당 페이지에서 **응용 프로그램(클라이언트) ID** 값을 복사합니다. 마켓플레이스 오퍼에 대한 잠재 고객을 받으려면 게시 포털 및 Dynamics에서 제공해야 하는 *응용 프로그램(클라이언트) ID* 값이기 때문에 이 값을 저장합니다.

    ![애플리케이션(클라이언트) ID](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. 앱의 왼쪽 탐색에서 **인증서 및 비밀을** 선택하고 해당 페이지에서 새 클라이언트 **비밀을** 선택합니다. 클라이언트 보안 에 대한 의미 있는 설명을 입력하고 만료 에서 **절대 옵션을** 선택합니다. 클라이언트 보안 설정을 만들려면 **추가를** 선택합니다.

    ![응용 프로그램 - 인증 및 비밀](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. 클라이언트 보안이 성공적으로 만들어지자마자 **클라이언트 보안 값을 복사합니다.** 페이지에서 멀리 이동한 후에는 값을 검색할 수 없습니다. 마켓플레이스 오퍼에 대한 잠재 고객을 받기 위해 게시 포털에서 제공해야 하는 *클라이언트 비밀* 값이기 때문에 이 값을 저장합니다. 
11. 앱의 왼쪽 탐색에서 **API 권한을** 선택한 다음 **권한 추가를**선택합니다.
12. Microsoft API를 선택한 다음 **동적 CRM을** API로 선택합니다.
13. *응용 프로그램에 필요한 권한 유형에서* **위임된 사용 권한이** 선택되었는지 확인합니다. user_impersonation 공통 데이터 서비스에 **대한** *권한(조직 사용자)을*확인합니다. **권한 추가**를 선택합니다.

    ![권한 추가](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Azure 포털에서 1-13단계를 완료한 후 URL(예:)으로 `https://tenant.crm.dynamics.com`이동하여 고객 참여 인스턴스에 대한 Dynamics 365로 이동합니다.
15. 기어 아이콘을 선택하여 설정에 액세스하고 상단 탐색 모음의 **고급 설정을 선택합니다.**
16. 설정 페이지에서 상단 탐색 모음에서 설정 메뉴에 액세스하고 **보안**을 선택합니다.
17. 보안 페이지에서 **사용자를**선택합니다.  사용자 페이지에서 "사용 가능한 사용자" 드롭다운을 선택하여 **응용 프로그램 사용자로**전환합니다.
18. 새 사용자를 만들려면 **새로 만들기**를 선택합니다. 

    ![새 사용자 만들기](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. **새 사용자에서는**사용자: 응용 프로그램 사용자가 선택되어 있는지 확인합니다. 이 연결에서 사용할 사용자에 대한 사용자 이름, 전체 이름 및 전자 메일 주소를 제공합니다. 또한 8단계에서 Azure 포털에서 만든 앱의 **응용 프로그램 ID에** 붙여넣습니다. 저장 **및 닫기를** 선택하여 사용자 추가를 완료합니다.

    ![새 사용자](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. 이 사용자의 연결 구성을 완료하려면 이 문서의 “보안 설정”으로 이동합니다.

### <a name="office-365"></a>Office 365

Azure Active Directory를 사용하지 않으려면 *Microsoft 365 관리 센터에*새 사용자를 등록할 수 있습니다. 잠재 고객을 계속 가져오려면 90일마다 사용자 이름/암호를 업데이트해야 합니다.

다음 단계를 사용하여 고객 참여를 위해 Dynamics 365에 대해 Office 365를 구성합니다.

1. [Microsoft 365 관리 센터](https://admin.microsoft.com)에 로그인합니다.

2. **사용자 추가**를 선택합니다.

    ![Microsoft 365 관리자 센터 - 사용자 추가](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. 리드 기록기 서비스에 대한 새 사용자를 만듭니다. 다음 설정을 구성합니다.

    * 사용자 이름 제공
    * 암호를 제공하고 "이 사용자가 처음 로그인할 때 암호를 변경하도록" 옵션을 선택 해제합니다.
    * 사용자 역할로 “사용자(관리자 액세스 권한 없음)”를 선택합니다.
    * 다음 화면 캡처에 표시된 제품 라이선스로 "Dynamics 365 고객 참여 계획"을 선택합니다. 선택한 라이선스에 대한 요금이 청구됩니다. 

마켓플레이스 오퍼의 잠재 고객을 받기 위해 게시 포털에서 제공해야 하는 *사용자 이름 및 암호* 값으로 이러한 값을 저장합니다.

![마이크로 소프트 365 관리 센터 - 새로운 사용자](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>보안 설정

최종 단계는 만든 사용자가 잠재 고객을 기록할 수 있도록 하는 것입니다.

1. 동적 인스턴스의 URL(예: `https://tenant.crm.dynamics.com`)으로 이동하여 고객 참여를 위한 Dynamics 365를 엽니다.
2. 상단 탐색 모음에서 기어 아이콘과 **고급 설정을** 선택하여 설정에 액세스합니다.
3. 설정 페이지에서 상단 탐색 모음에서 설정 메뉴에 액세스하고 **보안**을 선택합니다.
4. 보안 페이지에서 **사용자를** 선택하고 이 문서의 사용자 권한 구성 섹션에서 만든 사용자를 선택한 다음 **역할 관리를**선택합니다. 

    ![역할 관리](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. 역할 이름 "Microsoft 마켓플레이스 잠재 기록기"를 검색하고 사용자 역할을 할당하도록 선택합니다.

    ![사용자 역할 관리](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >이 역할은 사용자가 가져온 솔루션에서 생성되며 잠재 고객을 기록하고 호환성을 위해 솔루션 버전을 추적하는 권한만 갖습니다.

6. 보안 페이지로 돌아가보안 **역할을**선택합니다. "Microsoft 마켓플레이스 리드 라이터" 역할을 검색하고 선택합니다.

    ![보안 역할](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. 보안 역할에 들어가면 **핵심 레코드** 탭을 선택합니다. "사용자 엔터티 UI 설정" 엔터티검색 및 해당 엔터티에 대한 사용자 권한 만들기, 읽기 및 쓰기 권한(1/4 노란색 원)을 사용하여 해당 엔터티에 대한 사용 권한을 각각 한 번 클릭하여 사용하도록 설정합니다.

    ![마이크로소프트 마켓플레이스 리드 라이터 - 핵심 기록](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. 이제 사용자 **지정** 탭으로 이동합니다. "시스템 작업" 엔터티를 검색하고 해당 각 서클에 네 번 클릭하여 해당 엔터티에 대한 읽기, 쓰기 및 AppendTo 권한을 조직에 사용할 수 있습니다.

    ![마이크로소프트 마켓플레이스 리드 라이터 - 사용자 지정](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **저장 및 닫습니다.**

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>고객 참여를 위해 Dynamics 365로 잠재 고객을 보낼 오퍼 구성

게시 포털에서 오퍼의 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 따르십시오.

1. **오퍼의 제안 설정** 페이지로 이동합니다.
2. 잠재 고객 관리 섹션에서 **연결을** 선택합니다.

    ![리드 매니지먼트에 연결](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. 연결 세부 정보 팝업 창에서 잠재 고객 **대상에 대한 고객 참여에 대해 Dynamics 365를** 선택합니다.

    ![연결 세부 정보 - 리드 대상](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. 다음과 같은 `https://contoso.crm4.dynamics.com` **역학 365 인스턴스 URL을** 제공합니다.

5. **인증**방법, Azure Active Directory 또는 Office 365를 선택합니다. 
6. Azure Active Directory를 선택한 경우 **응용 프로그램(클라이언트)** `23456052-aaaa-bbbb-8662-1234df56788f`ID(예: ), `12345678-8af1-4asf-1234-12234d01db47` **디렉터리** ID(예:) 및 **클라이언트 보안(예:** `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`)을 제공합니다.

    ![연결 세부 정보 - Azure Active 디렉터리](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Office 365를 선택한 경우 **사용자 이름(예:** `contoso@contoso.onmicrosoft.com`) 및 `P@ssw0rd`암호(예: )를 제공합니다.

    ![연결 세부 정보 - 사용자 이름](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

8. **연락처 이메일** - 새 잠재 고객으로 연결될 때 이메일 알림을 받아야 하는 회사의 사람들에게 이메일을 제공합니다. 세미콜론으로 구분하여 여러 이메일을 제공할 수 있습니다.
9. **확인**을 선택합니다.

잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 유효성 검사 단추를 클릭합니다. 성공하면 잠재 고객 대상에 테스트 잠재 고객도 있습니다.

![잠재 고객 관리 - 연결 세부 정보 저장소 계정](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!Note]
>오퍼의 나머지 부분을 구성을 완료하고 게시해야 쿠폰에 대한 잠재 고객을 받을 수 있습니다.
