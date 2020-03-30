---
title: Azure AD 디렉터리 삭제 - Azure Active Directory | 마이크로 소프트 문서
description: 셀프 서비스 디렉터리를 포함하여 삭제를 위해 Azure AD 디렉터리를 준비하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961827"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Azure Active 디렉터리에서 디렉터리 삭제

Azure AD 디렉터리가 삭제되면 디렉터리에 포함된 모든 리소스도 삭제됩니다. 삭제하기 전에 관련 리소스를 최소화하여 조직을 준비합니다. Azure Active Directory(Azure AD) 전역 관리자만 포털에서 Azure AD 디렉터리를 삭제할 수 있습니다.

## <a name="prepare-the-directory"></a>디렉토리 준비

여러 검사를 통과할 때까지 Azure AD에서 디렉터리를 삭제할 수 없습니다. 이러한 검사는 Azure AD 디렉터리 삭제가 Office 365에 로그인하거나 Azure의 리소스에 액세스하는 기능과 같이 사용자 액세스에 부정적인 영향을 주는 위험을 줄입니다. 예를 들어 구독과 연결된 디렉터리가 실수로 삭제된 경우 사용자는 해당 구독에 대한 Azure 리소스에 액세스할 수 없습니다. 다음과 같은 조건을 확인합니다.

* 디렉터리를 삭제하는 글로벌 관리자 를 제외하고는 디렉터리에 사용자가 없을 수 있습니다. 디렉터리를 삭제하려면 먼저 다른 모든 사용자를 삭제해야 합니다. 온-프레미스에서 동기화된 경우 먼저 동기화를 해제해야 하며 Azure Portal 또는 Azure PowerShell cmdlet을 사용하여 클라우드 디렉터리에서 사용자를 삭제해야 합니다.
* 디렉터리에 애플리케이션이 없을 수 있습니다. 디렉터리를 삭제하려면 먼저 모든 응용 프로그램을 제거해야 합니다.
* 디렉터리에 연결된 다단계 인증 공급자가 없을 수 있습니다.
* Microsoft Azure, Office 365 또는 Azure AD Premium 등 Microsoft Online Services에 대한 구독이 디렉터리에 연결되어 있지 않습니다. 예를 들어 Azure에서 직접 기본 디렉터리를 만든 경우 Azure 구독에서 인증에 이 디렉터리를 계속 사용하면 이 디렉터리를 삭제할 수 없습니다. 마찬가지로 다른 사용자가 구독을 연결한 경우 디렉터리를 삭제할 수 없습니다.

## <a name="delete-the-directory"></a>디렉토리 삭제

1. 조직의 전역 [관리자인](https://aad.portal.azure.com) 계정으로 Azure AD 관리자 센터에 로그인합니다.

2. **Azure 활성 디렉터리**를 선택합니다.

3. 삭제할 디렉토리로 전환합니다.
  
   ![삭제하기 전에 조직 확인](./media/directory-delete-howto/delete-directory-command.png)

4. **디렉터리 삭제**를 선택합니다.
  
   ![조직을 삭제하는 명령을 선택합니다.](./media/directory-delete-howto/delete-directory-list.png)

5. 디렉터리가 하나 이상의 검사를 통과하지 못하면 전달하는 방법에 대한 자세한 정보가 제공되는 링크가 제공됩니다. 모든 검사를 통과한 후 **삭제**를 선택하여 프로세스를 완료합니다.

## <a name="if-you-cant-delete-the-directory"></a>디렉토리를 삭제할 수 없는 경우

Azure AD 디렉터리를 구성할 때 Azure AD Premium P2, Office 365 비즈니스 프리미엄 또는 엔터프라이즈 이동성 + 보안 E5와 같은 조직에 대한 라이선스 기반 구독을 활성화했을 수도 있습니다. 실수로 데이터 손실을 방지하기 위해 구독이 완전히 삭제될 때까지 디렉터리를 삭제할 수 없습니다. 구독은 디렉터리 삭제를 허용하기 위해 **프로비저닝 해제된** 상태여야 합니다. **만료되거나** **취소된** 구독은 **사용 안 함** 상태로 이동하고 마지막 단계는 **프로비저닝 해제 상태입니다.**

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대한 자세한 내용은 [비즈니스용 Office 365 구독이 종료되면 내 데이터와 액세스 권한에 어떤 변화가 있나요?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)를 참조하세요. 

구독 상태 | 데이터 | 데이터 액세스
----- | ----- | -----
활성(평가판의 경우 30일) | 모두 데이터에 액세스할 수 있음 | 사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<br>관리자는 Microsoft 365 관리자 센터 및 리소스에 대한 일반 액세스 권한이 있습니다. 
만료됨(30일) | 모두 데이터에 액세스할 수 있음| 사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<br>관리자는 Microsoft 365 관리자 센터 및 리소스에 대한 일반 액세스 권한이 있습니다.
사용 안 함(30일) | 관리자만 데이터에 액세스할 수 있음 | 사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<br>관리자는 Microsoft 365 관리 센터에 액세스할 수 있지만 사용자에게 라이선스를 할당하거나 사용자에게 할당할 수 없습니다.
프로비전 해제됨(사용 안 함으로 설정된 후 30일) | 데이터가 삭제됨(사용 중인 다른 서비스가 없는 경우 자동으로 삭제됨) | 사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<br>관리자는 Microsoft 365 관리 센터에 액세스하여 다른 구독을 구입하고 관리할 수 있습니다.

## <a name="delete-a-subscription"></a>구독 삭제

Microsoft 365 관리 센터를 사용하여 3일 내에 삭제할 **프로비저닝 해제** 된 상태에 구독을 넣을 수 있습니다.

1. 조직의 글로벌 관리자인 계정으로 [Microsoft 365 관리자 센터에](https://admin.microsoft.com) 로그인합니다. 초기 기본 도메인 contoso.onmicrosoft.com 있는 "Contoso" 디렉터리를 삭제하려는 경우 와 같은 admin@contoso.onmicrosoft.comUPN으로 로그인합니다.

2. 새 관리 센터 토글을 사용하도록 설정되어 있는지 확인하여 새 Microsoft 365 **관리 센터를** 미리 봅그리십시오.

   ![새로운 M365 관리 센터 환경 미리 보기](./media/directory-delete-howto/preview-toggle.png)

3. 새 관리 센터를 사용하도록 설정하면 구독을 삭제하려면 구독을 취소해야 합니다. **결제를** 선택하고 **제품 & 서비스를**선택한 다음 취소하려는 구독에 대한 구독 **취소를** 선택합니다. 피드백 페이지로 이동합니다.

   ![취소할 구독 선택](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 피드백 양식을 작성하고 **구독 취소를** 선택하여 구독을 취소합니다.

   ![구독 미리 보기에서 명령 취소](./media/directory-delete-howto/cancel-command.png)

5. 이제 구독을 삭제할 수 있습니다. 삭제하려는 구독에 대해 **삭제를** 선택합니다. **제품 & 서비스** 페이지에서 구독을 찾을 수 없는 경우 구독 **상태를** **모두로**설정되어 있는지 확인합니다.

   ![구독 삭제를 위한 삭제 링크](./media/directory-delete-howto/delete-command.png)

6. **구독 삭제를** 선택하여 구독을 삭제하고 이용 약관에 동의합니다. 모든 데이터는 3일 이내에 영구적으로 삭제됩니다. 마음이 바뀌면 3일 동안 [구독을 다시 활성화할](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) 수 있습니다.
  
   ![이용 약관을 주의 깊게 읽으십시오.](./media/directory-delete-howto/delete-terms.png)

7. 이제 구독 상태가 변경되고 구독이 삭제된 것으로 표시됩니다. 구독은 72시간 후에 **프로비전 해제됨** 상태가 됩니다.

8. 디렉터리에서 구독을 삭제하고 72시간이 경과하면 Azure AD 관리자 센터에 다시 로그인할 수 있으며 디렉터리 삭제를 차단하는 구독과 필수 작업이 없어야 합니다. Azure AD 디렉터리를 성공적으로 삭제할 수 있어야 합니다.
  
   ![삭제 시 구독 검사 통과 화면](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>삭제를 차단하는 평가판 구독이 있습니다.

Microsoft Power BI, 권한 관리 서비스, Microsoft Power Apps 또는 Dynamics 365와 같은 [셀프 서비스 등록 제품이](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) 있으며, 개별 사용자는 Office 365를 통해 등록할 수 있으며 Azure AD 디렉터리에서 인증을 위해 게스트 사용자를 만들 수도 있습니다. 이러한 셀프 서비스 제품은 데이터 손실을 방지하기 위해 디렉터리에서 완전히 삭제될 때까지 디렉터리 삭제를 차단합니다. 사용자가 개별적으로 등록했든 제품을 할당받았는지 여부는 Azure AD 관리자에서만 삭제할 수 있습니다.

셀프 서비스 등록 제품에는 두 가지 유형이 할당되는 방법에 있습니다. 

* 조직 수준 할당: Azure AD 관리자는 전체 조직에 제품을 할당하고 사용자는 개별적으로 라이선스가 부여되지 않은 경우에도 이 조직 수준 할당을 사용하여 서비스를 적극적으로 사용할 수 있습니다.
* 사용자 수준 할당: 셀프 서비스 등록 중에 개별 사용자는 기본적으로 관리자 없이 자신에게 제품을 할당합니다. 조직이 관리자에 의해 [관리되면(관리되지 않는 디렉터리의 관리자 인계](domains-admin-takeover.md)참조) 관리자는 셀프 서비스 등록 없이 사용자에게 제품을 직접 할당할 수 있습니다.  

셀프 서비스 등록 제품의 삭제를 시작하면 이 작업은 데이터를 영구적으로 삭제하고 서비스에 대한 모든 사용자 액세스를 제거합니다. 개별적으로 또는 조직 수준에서 오퍼를 할당한 사용자는 로그인하거나 기존 데이터에 액세스하지 못하도록 차단됩니다. [Microsoft Power BI 대시보드](https://docs.microsoft.com/power-bi/service-export-to-pbix) 또는 권한 관리 서비스 정책 [구성과](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)같은 셀프 서비스 등록 제품으로 데이터 손실을 방지하려면 데이터가 백업되어 다른 곳에 저장되어 있는지 확인합니다.

현재 사용 가능한 셀프 서비스 등록 제품 및 서비스에 대한 자세한 내용은 [사용 가능한 셀프 서비스 프로그램을](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)참조하십시오.

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대한 자세한 내용은 [비즈니스용 Office 365 구독이 종료되면 내 데이터와 액세스 권한에 어떤 변화가 있나요?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)를 참조하세요.

제품 상태 | 데이터 | 데이터 액세스
------------- | ---- | --------------
활성(평가판의 경우 30일) | 모두 데이터에 액세스할 수 있음 | 사용자는 셀프 서비스 등록 제품, 파일 또는 앱에 일반적으로 액세스할 수 있습니다.<br>관리자는 Microsoft 365 관리자 센터 및 리소스에 대한 일반 액세스 권한이 있습니다.
Deleted | 삭제된 데이터 | 사용자는 셀프 서비스 등록 제품, 파일 또는 앱에 액세스할 수 없습니다.<br>관리자는 Microsoft 365 관리 센터에 액세스하여 다른 구독을 구입하고 관리할 수 있습니다.

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Azure 포털에서 셀프 서비스 등록 제품을 삭제하려면 어떻게 해야 합니까?

Microsoft Power BI 또는 Azure 권한 관리 서비스와 같은 셀프 서비스 등록 제품을 **삭제** 상태로 전환하여 Azure AD 포털에서 즉시 삭제할 수 있습니다.

1. 조직의 전역 [관리자인](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 계정으로 Azure AD 관리자 센터에 로그인합니다. 초기 기본 도메인 contoso.onmicrosoft.com 있는 "Contoso" 디렉터리를 삭제하려는 경우 와 같은 admin@contoso.onmicrosoft.comUPN으로 로그온합니다.

2. 라이선스를 선택한 다음 **셀프 서비스 등록 제품을** **선택합니다.** 모든 셀프 서비스 가입 제품은 시트 기반 구독과 별도로 볼 수 있습니다. 영구적으로 삭제할 제품을 선택합니다. 다음은 Microsoft Power BI의 예입니다.

    ![사용자 이름이 잘못 입력되어 있거나 찾을 수 없습니다.](./media/directory-delete-howto/licenses-page.png)

3. 제품을 삭제하려면 **삭제를** 선택하고 데이터가 즉시 취소할 수 없게 삭제되는 약관에 동의합니다. 이 삭제 작업은 모든 사용자를 제거하고 제품에 대한 조직 액세스를 제거합니다. 예를 클릭하여 삭제를 진행합니다.  

    ![사용자 이름이 잘못 입력되어 있거나 찾을 수 없습니다.](./media/directory-delete-howto/delete-product.png)

4. **예를**선택하면 셀프 서비스 제품의 삭제가 시작됩니다. 진행 중인 삭제를 알려주는 알림이 있습니다.  

    ![사용자 이름이 잘못 입력되어 있거나 찾을 수 없습니다.](./media/directory-delete-howto/progress-message.png)

5. 이제 셀프 서비스 등록 제품 상태가 **삭제**됨으로 변경되었습니다. 페이지를 새로 고치면 셀프 서비스 등록 **제품** 페이지에서 제품을 제거해야 합니다.  

    ![사용자 이름이 잘못 입력되어 있거나 찾을 수 없습니다.](./media/directory-delete-howto/product-deleted.png)

6. 모든 제품을 삭제한 후에는 Azure AD 관리자 센터에 다시 로그인할 수 있으며 필요한 작업이 없고 디렉터리 삭제를 차단하는 제품이 없어야 합니다. Azure AD 디렉터리를 성공적으로 삭제할 수 있어야 합니다.

    ![사용자 이름이 잘못 입력되어 있거나 찾을 수 없습니다.](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>다음 단계

[Azure Active 디렉터리 설명서](https://docs.microsoft.com/azure/active-directory/)
