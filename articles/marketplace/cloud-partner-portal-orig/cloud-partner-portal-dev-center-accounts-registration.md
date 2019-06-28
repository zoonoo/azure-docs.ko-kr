---
title: Microsoft 개발자 계정 만들기 | Azure Marketplace
description: Microsoft 개발자 계정을 만들기 위한 요구 사항 및 단계입니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 4fde5d81fb97bec23fdb46ff53b05874c88d9d67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935857"
---
<a name="create-a-microsoft-developer-account"></a>Microsoft 개발자 계정 만들기
====================================

이 문서에서는 Azure Marketplace 게시가 승인된 Microsoft 개발자가 되는 방법을 설명합니다.

## <a name="create-a-microsoft-account"></a>Microsoft 계정 만들기

게시 프로세스를 시작하려면 **Microsoft 개발자 센터** 등록을 완료해야 합니다. **[Cloud 파트너 포털](https://cloudpartner.azure.com/)** 에서 동일한 등록 계정을 사용하여 게시 프로세스를 시작합니다.

### <a name="general-account-guidelines"></a>일반 계정 가이드라인

Azure Marketplace 제공에 대해 하나의 Microsoft 계정만 사용하는 것이 좋습니다. 이 계정은 서비스 또는 제품에 한정되지 않아야 합니다.

사용자 이름을 구성하는 주소는 귀사의 도메인에 속하고 귀사의 IT 팀에서 제어해야 합니다. 모든 게시 관련 작업은 이 계정을 통해 수행되어야 합니다.

>[!WARNING]
>“Azure” 및 “Microsoft”와 같은 단어는 Microsoft 계정 등록에 대해 지원되지 않습니다. 계정 만들기 및 등록 프로세스를 완료하려면 이러한 단어를 사용하지 마십시오.

### <a name="company-account-guidelines"></a>회사 계정 가이드라인

둘 이상의 사용자가 계정을 연 Microsoft 계정으로 로그인하여 계정에 액세스해야 하는 경우 다음 지침을 따릅니다.

>[!IMPORTANT]
>여러 사용자가 개발자 센터 계정에 액세스할 수 있도록 하려면 Azure Active Directory를 사용하여 각 사용자에게 역할을 할당하는 것이 좋습니다. 개별 Azure AD 자격 증명으로 로그인하여 계정에 액세스할 수 있습니다. 자세한 내용은 [계정 사용자 관리](https://docs.microsoft.com/windows/uwp/publish/manage-account-users)를 참조하세요.

-   한 명의 개인이 아닌 회사 도메인에 속하는 메일 주소를 사용하여 Microsoft 계정을 만듭니다. 예를 들어, windowsapps\@fabrikam.com입니다.
-   이 Microsoft 계정에 대한 액세스를 최대한 적은 수의 개발자로 제한합니다.
-   개발자 계정에 액세스해야 하는 모든 사람을 포함하는 회사 전자 메일 배포 목록을 설정하고 보안 정보에 이 전자 메일 주소를 추가합니다. 이렇게 하면 목록에 있는 모든 직원이 필요할 때 보안 코드를 받고 Microsoft 계정의 보안 정보를 관리할 수 있습니다. 배포 목록을 설정할 수 없는 경우 개인 메일 계정 소유자는 메시지가 표시되면(예: 새 보안 정보가 계정에 추가되거나 새 디바이스에서 액세스해야 하는 경우) 보안 코드에 액세스하여 공유할 수 있어야 합니다.
-   내선번호가 필요가 없고 핵심 팀 구성원에 연락 가능한 회사 전화 번호를 추가합니다.
-   일반적으로 개발자가 신뢰할 수 있는 디바이스를 사용하여 회사의 개발자 계정에 로그인하도록 합니다. 모든 핵심 팀 구성원은 이러한 신뢰할 수 있는 디바이스에 액세스할 수 있어야 합니다. 이렇게 하면 계정에 액세스할 때 보안 코드를 전송할 필요성이 줄어듭니다.
-   신뢰할 수 없는 PC에서 계정에 액세스할 수 있도록 해야 하는 경우 해당 액세스를 최대 5명의 개발자로 제한합니다. 이상적으로는 이러한 개발자가 동일한 지리적 네트워크 위치를 공유하는 컴퓨터에서 계정에 액세스해야 합니다.
-   [회사의 보안 정보](https://account.live.com/proofs/Manage)를 자주 검토하여 최신 정보인지 확인합니다.

>[!IMPORTANT]
>개발자 계정은 주로 신뢰할 수 있는 PC에서 액세스해야 합니다. 이는 주당 계정별로 생성되는 코드 수에 제한이 있기 때문에 중요합니다. 또한 가장 원활한 로그인 환경을 가능하게 합니다.
>
>자세한 내용은 [추가 개발자 계정 지침 및 보안](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts)을 참조하세요.

### <a name="to-create-a-microsoft-account"></a>Microsoft 계정을 만들려면

1.  새 Chrome Incognito 또는 Internet Explorer InPrivate 브라우징 세션을 열어 기존 계정에 로그인되지 않았는지 확인합니다.
2.  [이 링크](https://signup.live.com/signup.aspx)를 사용하여 메일(이전 가이드라인 사용)을 Microsoft 계정으로 등록합니다. 다음 등록 지침을 수행합니다.

    - 계정을 Microsoft 계정으로 등록할 때 시스템에서 문자 메시지 또는 자동 응답 통화 형태로 계정 확인 코드를 보낼 올바른 전화번호를 제공해야 합니다.
    - 계정을 Microsoft 계정으로 등록할 때 계정 확인을 위해 자동 메일을 수신하기 위한 유효한 메일 ID를 제공해야 합니다.
    - DL에 전송된 메일 주소를 확인합니다.

    이제 Microsoft 개발자 센터에서 새 Microsoft 계정을 사용할 준비가 되었습니다.

## <a name="register-your-account-in-microsoft-developer-center"></a>Microsoft 개발자 센터에서 계정 등록

Microsoft 개발자 센터는 회사 정보를 등록하는 데 한 번 사용됩니다. 등록자는 회사의 정식 담당자여야 하며 신원을 확인할 수 있는 개인 정보를 제공해야 합니다. 등록자는 회사에서 공유되는 Microsoft 계정을 사용해야 하며 **클라우드 파트너 포털에서도 동일한 계정을 사용해야 합니다.** 계정 만들기를 시작하기 전에 회사에 Microsoft 개발자 센터 계정이 아직 없는지 확인해야 합니다. 프로세스 중에 회사 주소 정보, 은행 계좌 정보 및 세금 정보를 수집합니다. 이러한 정보는 일반적으로 재무 또는 업무 연락처에서 확인할 수 있습니다.

>[!IMPORTANT]
>제품 만들기 및 배포의 여러 단계를 진행하기 위해서는 다음 개발자 프로필 구성 요소를 완료해야 합니다.

| 개발자 프로필     | 초안 시작    | 스테이징       | 무료 게시 및 솔루션 템플릿   | 상업적 게시   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| 회사 등록  | 필수         | 필수     | 필수                             | 필수             |
| 세금 프로필 ID        | 옵션          | 옵션      | 옵션                              | 필수             |
| 은행 계좌          | 옵션          | 옵션      | 옵션                              | 필수             |

>[!NOTE]
>BYOL(사용자 라이선스 필요)은 가상 머신에만 지원되며 무료 제공됩니다.

### <a name="register-your-company-account"></a>회사 계정 등록

1. 새 Chrome Incognito 또는 Internet Explorer InPrivate 브라우징 세션을 열어 개인 계정에 로그인되지 않았는지 확인합니다.

2. [Windows 개발자 센터](https://dev.windows.com/registration?accountprogram=azure)로 이동하여 판매자로 등록합니다. 계속 진행하기 전에 다음 중요 정보를 읽어 보세요.

   ![Microsoft 계정 확인](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >개발자 센터에서 등록하는 데 사용할 전자 메일 ID 또는 메일 그룹(개인 종속성을 제거하려면 메일 그룹이 권장됨)을 Microsoft 계정으로 처음 등록해야 합니다. 그러지 않은 경우 이 링크를 사용하여 등록하세요. 또한 개발자 센터 등록 시 Microsoft 회사 도메인의 메일 ID는 사용할 수 없습니다.

   ![개발자 센터 로그인](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. “계정 보호 지원” 마법사를 실행하여 전화 번호 또는 메일 주소를 통해 신원을 확인합니다.

4. 등록 계정 정보의 드롭다운 목록에서 해당 **계정 국가/지역**을 선택하고 **다음**을 선택합니다.

   ![국가/지역 선택](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >국가/지역 "판매처": Azure Marketplace에서 서비스를 판매 하기 위해 등록 된 법인은 승인 된 "판매처" 국가/지역 드롭다운 목록에 표시 된 것 중 하나에서 가능 해야 합니다. 이 제한은 지급액 및 세금 때문에 있습니다. 자세한 내용은 Marketplace 참가 정책을 참조하세요.

5. **회사**를 “계정 유형”으로 선택하고 **다음**을 선택합니다.

    >[!IMPORTANT]
    >계정 유형을 더 잘 이해하고 가장 적합한 유형을 결정하려면 다음 화면 캡처에 표시된 계정 유형, 위치 및 요금 페이지를 봅니다.

    ![판매자를 위한 계정 유형](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. **게시자 표시 이름**을 입력합니다. 일반적으로 해당 회사의 이름입니다.

    >[!NOTE]
    >개발자 센터에 입력한 게시자 표시 이름은 제품이 나열될 때 Azure Marketplace에 표시되지 않습니다. 그러나 등록 프로세스를 완료하려면 이 정보가 필요합니다.

7. 계정 확인을 위한 **연락처 정보** 를 입력합니다.

    >[!IMPORTANT]
    >개발자 센터에서 회사가 승인되기 위해 확인 프로세스에 사용되므로 정확한 연락처 정보를 제공해야 합니다.

8. **회사 승인자**의 연락처 정보를 입력합니다. 회사 승인자는 조직을 대신하여 개발자 센터에서 계정을 만드는 권한이 있는 사람을 확인할 수 있는 사람입니다. 이 정보를 입력한 후 **다음**을 선택하여 **결제 섹션**으로 이동합니다.

    ![회사 승인자 식별](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. 해당 계정에 대한 결제 정보를 입력합니다. 등록 비용을 부담하는 프로모션 코드를 가지고 있는 경우 여기에 프로모션 코드를 입력할 수 있습니다. 그렇지 않은 경우 신용 카드 정보(또는 지원되는 시장에서 PayPal)를 제공합니다. **다음**을 선택하여 최종 **검토**로 이동합니다.

   ![결제 등록](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. 계정 정보를 검토하고 모든 정보가 올바른지 확인합니다. [Microsoft Azure Marketplace 게시자 계약](https://go.microsoft.com/fwlink/?LinkID=699560)의 사용 약관을 읽고 동의합니다. 확인란을 선택하여 이러한 약관을 읽고 동의했음을 나타냅니다.

11. **마침** 을 클릭하여 등록을 확인합니다. 확인 메시지가 해당 메일 주소로 전송됩니다.

12. 무료 제품만 게시하려는 경우 [Cloud 파트너 포털로 이동](https://cloudpartner.azure.com/)을 선택하고 이 문서의 “Cloud 파트너 포털에서 계정 등록”으로 건너뜁니다.

### <a name="commercial-offers"></a>상업용 제품

시간당 청구 모델을 사용하여 가상 머신 제품과 같은 상업용 제품을 게시하려는 경우 세금 및 은행 정보를 입력해야 합니다. 이렇게 하려면 개발자 센터 계정에 로그인하고 **계정 정보 업데이트**를 선택합니다. 다음 섹션인 “은행 및 세금 정보 추가”의 지침을 따릅니다.

>[!IMPORTANT]
>은행 계좌 및 세금 정보를 제공하지 않으면 상업용 제품을 프로덕션에 푸시할 수 없습니다.

나중에 은행 및 세금 정보를 업데이트하려는 경우 이 문서의 “Cloud 파트너 포털에서 계정 등록”으로 건너뛸 수 있습니다.

>[!NOTE]
>세금 정보의 유효성을 검사하는 데 시간이 걸리므로 가능한 한 빨리 은행 계좌 및 세금 정보를 제공하는 것이 좋습니다.

### <a name="add-banking-and-tax-information"></a>은행 및 세금 정보 추가

구매를 위한 상업용 제품을 게시하려면 지급액 및 세금 정보를 추가하고 개발자 센터에서 유효성을 검사할 수 있도록 제출해야 합니다.

**은행 정보를 제공하려면**

1.  Microsoft 계정을 사용하여 [Microsoft 개발자 센터](https://dev.windows.com/registration?accountprogram=azure) 에 로그인합니다.
2.  왼쪽 메뉴에서 **지급 계좌**를 선택하고 **결제 방법 선택** 아래에서 **은행 계좌** 또는 **PayPal**을 선택합니다.

    >[!NOTE]
    >Marketplace에서 고객이 구매하는 상업용 제품인 경우 이 계좌로 해당 구매에 대한 지급액을 받게 됩니다.
3.  결제 정보를 입력하고 **저장**을 선택합니다.

    >[!IMPORTANT]
    >지급 계좌를 업데이트하거나 변경해야 하는 경우 앞의 단계를 따라 현재 정보를 새 정보로 바꿉니다.
    >
    >지급 계좌를 변경하면 한 번의 지불 주기까지 지불을 연기할 수 있습니다. 이러한 지불 연기는 처음 지급 계좌를 설정할 때와 마찬가지로 계정 변경 내용을 확인해야 하기 때문에 발생합니다. 계정 확인 작업이 끝난 후 여전히 전체 금액에 대해 비용을 지불해야 하는 경우 현재 지불 주기에 대한 모든 지불 대금이 다음 주기에 추가됩니다.

4.  **다음**을 선택합니다.

**세금 정보를 제공하려면**

1.  Microsoft 계정을 사용하여(필요한 경우) [Microsoft 개발자 센터](https://dev.windows.com/registration?accountprogram=azure) 에 로그인합니다.
2.  왼쪽 메뉴에서 **세금 프로필**을 선택합니다.
3.  **세금 양식 설정** 페이지에서 다음을 수행합니다.
    - 영주권이 있는 국가 또는 지역을 선택합니다.
    - 주요 시민권이 있는 국가 또는 지역을 선택합니다.
    - **다음**을 선택합니다.
4.  세금 세부 정보를 입력하고 **다음**을 선택합니다.

>[!WARNING]
>Microsoft 개발자 센터 계정에 은행 계좌 및 세금 정보를 제공하지 않으면 상업용 제품을 프로덕션으로 푸시할 수 없습니다.

### <a name="developer-center-registration-issues"></a>개발자 센터 등록 문제

개발자 센터 등록에 문제가 있는 경우 다음 단계를 사용하여 지원 티켓을 엽니다.

1.  [지원 링크](https://developer.microsoft.com/windows/support)로 이동합니다.
2.  **문의하기** 아래에서 **인시던트 제출**을 선택합니다.
    ![티켓 열기](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  **문제 유형**에서 “개발자 센터에 대한 도움말”을 선택하고 **범주**에서 “앱 게시 및 관리”를 선택합니다. **메일 시작**을 선택합니다.

    ![문제 유형 식별](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  로그인 페이지가 제공됩니다. Microsoft 계정을 사용하여 로그인합니다. Microsoft 계정이 없는 경우 [계정을 만듭니다](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).

5.  문제에 대한 자세한 정보를 제공하고 **제출**을 선택하여 티켓을 보냅니다.

    ![티켓 제출](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>클라우드 파트너 포털에서 계정 등록

[Cloud 파트너 포털](https://cloudpartner.azure.com/)을 사용하여 제품을 게시하고 관리합니다.

1.  새 Chrome Incognito 또는 Internet Explorer InPrivate 검색 세션을 열어 개인 계정에 로그인되지 않았는지 확인합니다.
2.  [클라우드 파트너 포털](https://cloudpartner.azure.com/)로 이동합니다
3.  새 사용자이며 [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 처음으로 로그인하는 경우 개발자 센터 계정에 등록된 ID와 동일한 메일 ID로 로그인해야 합니다. 이렇게 하면 개발자 센터 계정 및 Cloud 파트너 포털 계정이 서로 연결됩니다.

나중에 애플리케이션에서 작업 중인 회사의 다른 구성원을 추가할 수 있습니다. 다음 섹션의 단계를 수행하여 Cloud 파트너 포털에서 해당 구성원을 참가자 또는 소유자로 지정할 수 있습니다.

클라우드 파트너 포털에 참가자/소유자로 추가되면 자신의 계정을 사용하여 로그인할 수 있습니다.

>[!TIP]
>참가 정책은 Azure 웹 사이트에 설명되어 있습니다.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Cloud 파트너 포털에서 소유자 또는 참가자로 사용자 관리

[클라우드 파트너 포털에서 사용자를 관리하는 단계](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>다음 단계

계정을 만들고 등록했으니, 이제 Azure Marketplace 게시 프로세스를 시작할 수 있습니다.
