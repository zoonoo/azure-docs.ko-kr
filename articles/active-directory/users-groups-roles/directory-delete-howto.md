---
title: Azure AD 디렉터리 삭제-Azure Active Directory | Microsoft Docs
description: 셀프 서비스 디렉터리를 포함 하 여 Azure AD 디렉터리에서 삭제를 준비 하는 방법을 설명 합니다.
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
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961827"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Azure Active Directory에서 디렉터리를 삭제 합니다.

Azure AD 디렉터리를 삭제 하면 디렉터리에 포함 된 모든 리소스도 삭제 됩니다. 삭제 하기 전에 연결 된 리소스를 최소화 하 여 조직을 준비 합니다. Azure AD (Azure Active Directory) 전역 관리자만 포털에서 Azure AD 디렉터리를 삭제할 수 있습니다.

## <a name="prepare-the-directory"></a>디렉터리 준비

Azure AD에서 디렉터리를 삭제 하려면 여러 검사를 통과 해야 합니다. 이러한 검사를 통해 azure AD 디렉터리를 삭제 하면 Office 365에 로그인 하거나 Azure의 리소스에 액세스 하는 기능과 같은 사용자 액세스에 부정적인 영향을 줄 가능성이 줄어듭니다. 예를 들어 구독과 연결 된 디렉터리가 의도 하지 않게 삭제 된 경우 사용자가 해당 구독에 대 한 Azure 리소스에 액세스할 수 없습니다. 다음과 같은 조건을 확인합니다.

* 디렉터리에 디렉터리를 삭제 하는 한 명의 전역 관리자를 제외 하 고는 디렉터리에 사용자가 없을 수 있습니다. 디렉터리를 삭제하려면 먼저 다른 모든 사용자를 삭제해야 합니다. 사용자가 온-프레미스에서 동기화 되는 경우 먼저 동기화를 해제 해야 하며 Azure Portal 또는 Azure PowerShell cmdlet을 사용 하 여 클라우드 디렉터리에서 사용자를 삭제 해야 합니다.
* 디렉터리에 애플리케이션이 없을 수 있습니다. 모든 응용 프로그램을 제거한 후에 야 디렉터리를 삭제할 수 있습니다.
* 디렉터리에 연결 된 multi-factor authentication 공급자가 없을 수 있습니다.
* Microsoft Azure, Office 365 또는 Azure AD Premium 등 Microsoft Online Services에 대한 구독이 디렉터리에 연결되어 있지 않습니다. 예를 들어 Azure에서 직접 기본 디렉터리를 만든 경우 Azure 구독에서 인증에 이 디렉터리를 계속 사용하면 이 디렉터리를 삭제할 수 없습니다. 마찬가지로 다른 사용자가 구독을 연결한 경우 디렉터리를 삭제할 수 없습니다.

## <a name="delete-the-directory"></a>디렉터리 삭제

1. 조직의 전역 관리자 인 계정으로 [AZURE AD 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.

2. **Azure Active Directory**를 선택합니다.

3. 삭제 하려는 디렉터리로 전환 합니다.
  
   ![삭제 전 조직 확인](./media/directory-delete-howto/delete-directory-command.png)

4. **디렉터리 삭제**를 선택합니다.
  
   ![조직을 삭제 하는 명령을 선택 합니다.](./media/directory-delete-howto/delete-directory-list.png)

5. 디렉터리에서 하나 이상의 검사를 통과 하지 못하는 경우에는를 전달 하는 방법에 대 한 자세한 내용을 볼 수 있는 링크가 제공 됩니다. 모든 검사를 통과한 후 **삭제**를 선택하여 프로세스를 완료합니다.

## <a name="if-you-cant-delete-the-directory"></a>디렉터리를 삭제할 수 없는 경우

Azure AD 디렉터리를 구성할 때 Azure AD Premium P2, Office 365 Business Premium 또는 Enterprise Mobility + Security E5와 같은 조직에 대 한 라이선스 기반 구독을 활성화 했을 수도 있습니다. 실수로 인 한 데이터 손실을 방지 하기 위해 구독이 완전히 삭제 될 때까지 디렉터리를 삭제할 수 없습니다. 디렉터리 삭제를 허용 하려면 구독이 **프로 비전 해제** 상태 여야 합니다. **만료** 되거나 **취소** 된 구독은 **비활성화** 된 상태로 이동 되 고 최종 단계는 **프로 비전 해제** 상태입니다.

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대한 자세한 내용은 [비즈니스용 Office 365 구독이 종료되면 내 데이터와 액세스 권한에 어떤 변화가 있나요?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)를 참조하세요. 

구독 상태 | Data | 데이터 액세스
----- | ----- | -----
활성(평가판의 경우 30일) | 모두 데이터에 액세스할 수 있음 | 사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<br>관리자는 Microsoft 365 관리 센터 및 리소스에 대 한 일반적인 액세스 권한을 가집니다. 
만료됨(30일) | 모두 데이터에 액세스할 수 있음| 사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<br>관리자는 Microsoft 365 관리 센터 및 리소스에 대 한 일반적인 액세스 권한을 가집니다.
사용 안 함(30일) | 관리자만 데이터에 액세스할 수 있음 | 사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<br>관리자가 Microsoft 365 관리 센터에 액세스할 수 있지만 사용자에 게 라이선스를 할당 하거나 사용자를 업데이트할 수 없음
프로비전 해제됨(사용 안 함으로 설정된 후 30일) | 데이터가 삭제됨(사용 중인 다른 서비스가 없는 경우 자동으로 삭제됨) | 사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<br>관리자는 Microsoft 365 관리 센터에 액세스 하 여 다른 구독을 구매 하 고 관리할 수 있습니다.

## <a name="delete-a-subscription"></a>구독 삭제

Microsoft 365 관리 센터를 사용 하 여 3 일 이내에 구독을 **프로 비전 해제** 상태로 전환할 수 있습니다.

1. 조직의 전역 관리자 인 계정을 사용 하 여 [Microsoft 365 관리 센터](https://admin.microsoft.com) 에 로그인 합니다. 초기 기본 도메인 contoso.onmicrosoft.com을 포함 하는 "Contoso" 디렉터리를 삭제 하려는 경우 admin@contoso.onmicrosoft.com와 같은 UPN을 사용 하 여 로그인 합니다.

2. 새 **관리 센터 전환 시도** 를 사용 하도록 설정 하 여 새 Microsoft 365 관리 센터를 미리 봅니다.

   ![새 M365 관리 센터 환경 미리 보기](./media/directory-delete-howto/preview-toggle.png)

3. 새 관리 센터를 사용 하도록 설정한 후에는 구독을 취소 해야 삭제할 수 있습니다. **청구** 를 선택 하 고 **제품 & 서비스**를 선택한 다음 취소 하려는 구독에 대 한 **구독 취소** 를 선택 합니다. 사용자 의견 페이지가 표시 됩니다.

   ![취소할 구독 선택](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 피드백 양식을 작성 하 고 구독 **취소** 를 선택 하 여 구독을 취소 합니다.

   ![구독 미리 보기의 취소 명령](./media/directory-delete-howto/cancel-command.png)

5. 이제 구독을 삭제할 수 있습니다. 삭제 하려는 구독에 대해 **삭제** 를 선택 합니다. **Products & services** 페이지에서 구독을 찾을 수 없는 경우 **구독 상태** 를 **모두**로 설정 했는지 확인 합니다.

   ![구독 삭제를 위한 삭제 링크](./media/directory-delete-howto/delete-command.png)

6. 구독 **삭제** 를 선택 하 여 구독을 삭제 하 고 사용 약관에 동의 합니다. 모든 데이터는 3일 이내에 영구적으로 삭제됩니다. 사용자의 생각이 바뀌면 3 일 동안 [구독을 다시 활성화할](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) 수 있습니다.
  
   ![사용 약관을 자세히 읽습니다.](./media/directory-delete-howto/delete-terms.png)

7. 이제 구독 상태가 변경 되었으며 구독이 삭제 되도록 표시 되었습니다. 구독은 72시간 후에 **프로비전 해제됨** 상태가 됩니다.

8. 디렉터리에서 구독을 삭제 하 고 72 시간이 경과 하면 Azure AD 관리 센터에 다시 로그인 할 수 있으며, 필수 작업이 없으며 디렉터리 삭제를 차단 하는 구독이 없어야 합니다. Azure AD 디렉터리를 성공적으로 삭제할 수 있어야 합니다.
  
   ![삭제 시 구독 검사 통과 화면](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>삭제를 차단 하는 평가판 구독이 있습니다.

Microsoft Power BI, Rights Management 서비스, Microsoft Power Apps 또는 Dynamics 365와 같은 [셀프 서비스 등록 제품이](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) 있습니다. 개별 사용자는 Office 365를 통해 등록할 수 있으며, Azure AD 디렉터리에서 인증을 위해 게스트 사용자도 만듭니다. 이러한 셀프 서비스 제품은 디렉터리에서 완전히 삭제 될 때까지 디렉터리 삭제를 차단 하 여 데이터 손실을 방지 합니다. 사용자가 개별적으로 등록 하거나 제품을 할당 했는지 여부에 관계 없이 Azure AD 관리자만 삭제할 수 있습니다.

할당 되는 방법에는 두 가지 유형의 셀프 서비스 등록 제품이 있습니다. 

* 조직 수준 할당: Azure AD 관리자는 전체 조직에 제품을 할당 하 고, 사용자는 개별적으로 사용이 허가 되지 않은 경우에도이 조직 수준 할당으로 서비스를 적극적으로 사용할 수 있습니다.
* 사용자 수준 할당: 셀프 서비스 등록 중 개별 사용자는 기본적으로 관리자 없이 제품을 할당 합니다. 조직이 관리자에 의해 관리 되 면 ( [관리 되지 않는 디렉터리의 관리자 인수](domains-admin-takeover.md)참조) 관리자는 셀프 서비스 등록 없이 사용자에 게 제품을 직접 할당할 수 있습니다.  

셀프 서비스 등록 제품의 삭제를 시작 하면 작업에서 데이터를 영구적으로 삭제 하 고 서비스에 대 한 모든 사용자 액세스를 제거 합니다. 제품을 개별적으로 또는 조직 수준에서 할당 한 모든 사용자는 로그인 하거나 기존 데이터에 액세스 하지 못하도록 차단 됩니다. [Microsoft Power BI 대시보드](https://docs.microsoft.com/power-bi/service-export-to-pbix) 또는 [Rights Management 서비스 정책 구성과](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)같은 셀프 서비스 등록 제품에서 데이터 손실을 방지 하려면 데이터를 다른 위치에 백업 하 고 저장 해야 합니다.

현재 사용할 수 있는 셀프 서비스 등록 제품 및 서비스에 대 한 자세한 내용은 [사용 가능한 셀프 서비스 프로그램](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)을 참조 하십시오.

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대한 자세한 내용은 [비즈니스용 Office 365 구독이 종료되면 내 데이터와 액세스 권한에 어떤 변화가 있나요?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)를 참조하세요.

제품 상태 | Data | 데이터 액세스
------------- | ---- | --------------
활성(평가판의 경우 30일) | 모두 데이터에 액세스할 수 있음 | 사용자는 셀프 서비스 등록 제품, 파일 또는 앱에 대 한 일반적인 액세스 권한이 있습니다.<br>관리자는 Microsoft 365 관리 센터 및 리소스에 대 한 일반적인 액세스 권한을 가집니다.
Deleted | 삭제 된 데이터 | 사용자는 셀프 서비스 등록 제품, 파일 또는 앱에 액세스할 수 없습니다.<br>관리자는 Microsoft 365 관리 센터에 액세스 하 여 다른 구독을 구매 하 고 관리할 수 있습니다.

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Azure Portal에서 셀프 서비스 등록 제품을 삭제 하려면 어떻게 해야 하나요?

Microsoft Power BI 또는 Azure Rights Management 서비스와 같은 셀프 서비스 등록 제품을 Azure AD 포털에서 즉시 삭제할 **삭제** 상태로 전환할 수 있습니다.

1. 조직의 전역 관리자 인 계정으로 [AZURE AD 관리 센터](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 에 로그인 합니다. 초기 기본 도메인 contoso.onmicrosoft.com을 포함 하는 "Contoso" 디렉터리를 삭제 하려는 경우 admin@contoso.onmicrosoft.com와 같은 UPN을 사용 하 여 로그온 합니다.

2. **라이선스**를 선택 하 고 **셀프 서비스 등록 제품**을 선택 합니다. 사용자 기반 구독에서 별도로 모든 셀프 서비스 등록 제품을 볼 수 있습니다. 영구적으로 삭제 하려는 제품을 선택 합니다. Microsoft Power BI의 예제는 다음과 같습니다.

    ![사용자 이름이 잘못 입력 되었거나 찾을 수 없습니다.](./media/directory-delete-howto/licenses-page.png)

3. **삭제** 를 선택 하 여 제품을 삭제 하 고 데이터를 즉시 삭제 하 고 영구적으로 삭제 하는 조건에 동의 합니다. 이 삭제 작업은 모든 사용자를 제거 하 고 제품에 대 한 조직 액세스를 제거 합니다. 예를 클릭 하 여 삭제를 진행 합니다.  

    ![사용자 이름이 잘못 입력 되었거나 찾을 수 없습니다.](./media/directory-delete-howto/delete-product.png)

4. **예**를 선택 하면 셀프 서비스 제품 삭제가 시작 됩니다. 진행 중인 삭제를 알려 주는 알림이 표시 됩니다.  

    ![사용자 이름이 잘못 입력 되었거나 찾을 수 없습니다.](./media/directory-delete-howto/progress-message.png)

5. 이제 셀프 서비스 등록 제품 상태가 **삭제**됨으로 변경 되었습니다. 페이지를 새로 고치면 **셀프 서비스 등록 제품** 페이지에서 제품을 제거 해야 합니다.  

    ![사용자 이름이 잘못 입력 되었거나 찾을 수 없습니다.](./media/directory-delete-howto/product-deleted.png)

6. 모든 제품을 삭제 한 후에는 Azure AD 관리 센터에 다시 로그인 할 수 있으며, 필요한 조치가 없으며 디렉터리 삭제를 차단 하는 제품이 없습니다. Azure AD 디렉터리를 성공적으로 삭제할 수 있어야 합니다.

    ![사용자 이름이 잘못 입력 되었거나 찾을 수 없습니다.](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>다음 단계

[Azure Active Directory 설명서](https://docs.microsoft.com/azure/active-directory/)
