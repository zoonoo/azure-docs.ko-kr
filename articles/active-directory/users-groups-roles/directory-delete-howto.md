---
title: Azure AD 디렉터리를 삭제할-Azure Active Directory | Microsoft Docs
description: 셀프 서비스 디렉터리를 포함 하 여 삭제에 대 한 Azure AD 디렉터리를 준비 하는 방법에 설명
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
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473197"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Azure Active Directory의 디렉터리를 삭제 합니다.

Azure AD 디렉터리가 삭제 되 면 디렉터리에 포함 된 모든 리소스도 삭제 됩니다. 삭제 하기 전에 연결된 된 리소스를 최소화 하 여 조직의 준비 합니다. Azure Active Directory (Azure AD) 전역 관리자만 포털에서 Azure AD 디렉터리를 삭제할 수 있습니다.

## <a name="prepare-the-directory"></a>디렉터리를 준비 합니다.

몇 가지 검사 전달 될 때까지 Azure AD에서 디렉터리를 삭제할 수 없습니다. 이러한 검사는 부정적인 Azure AD 디렉터리를 삭제 하면 Azure에서 리소스를 액세스 또는 Office 365에 로그인 하는 기능과 같은 사용자 액세스에 영향을 주는 위험을 줄입니다. 예를 들어 구독에 연결 된 디렉터리를 실수로 삭제할 경우 다음 사용자가 액세스할 수 없습니다 해당 구독에 대 한 Azure 리소스. 다음과 같은 조건을 확인합니다.

* 디렉터리를 삭제 하는 한 명의 전역 관리자를 제외 하 고 디렉터리에 사용자가 있을 수 있습니다. 디렉터리를 삭제하려면 먼저 다른 모든 사용자를 삭제해야 합니다. 사용자가 온-프레미스에서 동기화 되 면 다음 동기화 먼저 비활성화 해야, 및 Azure portal 또는 Azure PowerShell cmdlet을 사용 하 여 클라우드 디렉터리에서 사용자를 삭제 해야 합니다.
* 디렉터리에 애플리케이션이 없을 수 있습니다. 디렉터리를 삭제 하기 전에 모든 응용 프로그램을 제거 해야 합니다.
* 없는 multi-factor authentication 공급자를 디렉터리에 연결할 수 있습니다.
* Microsoft Azure, Office 365 또는 Azure AD Premium 등 Microsoft Online Services에 대한 구독이 디렉터리에 연결되어 있지 않습니다. 예를 들어 Azure에서 직접 기본 디렉터리를 만든 경우 Azure 구독에서 인증에 이 디렉터리를 계속 사용하면 이 디렉터리를 삭제할 수 없습니다. 마찬가지로 다른 사용자가 구독을 연결한 경우 디렉터리를 삭제할 수 없습니다.

## <a name="delete-the-directory"></a>디렉터리 삭제

1. 에 로그인 합니다 [Azure AD 관리 센터](https://aad.portal.azure.com) 조직에 대 한 전역 관리자 인 계정입니다.

2. **Azure Active Directory**를 선택합니다.

3. 삭제 하려는 디렉터리로 전환 합니다.
  
   ![조직 삭제 하기 전에 확인](./media/directory-delete-howto/delete-directory-command.png)

4. **디렉터리 삭제**를 선택합니다.
  
   ![조직 삭제 명령을 선택 합니다.](./media/directory-delete-howto/delete-directory-list.png)

5. 디렉터리에 하나 이상의 검사를 통과 하지 못하는 경우 전달 하는 방법에 대 한 자세한 내용은 링크와 함께 제공 됩니다. 모든 검사를 통과한 후 **삭제**를 선택하여 프로세스를 완료합니다.

## <a name="if-you-cant-delete-the-directory"></a>디렉터리를 삭제할 수 없는 경우

Azure AD 디렉터리를 구성할 때 수 또한 활성화 한 라이선스 기반 구독 Azure AD Premium P2, Office 365 Business Premium 또는 Enterprise Mobility + Security E5와 같은 조직에 대 한 합니다. 실수로 인 한 데이터 손실을 방지 하려면 구독이 완전히 삭제 될 때까지 디렉터리를 삭제할 수 없습니다. 구독에 있어야 합니다.는 **프로 비전 해제 됨** 상태 디렉터리 삭제를 허용 합니다. **만료 됨** 또는 **Canceled** 구독으로 이동 합니다 **사용 안 함** 상태 및 최종 단계는는 **프로 비전 해제 됨** 상태입니다.

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대한 자세한 내용은 [비즈니스용 Office 365 구독이 종료되면 내 데이터와 액세스 권한에 어떤 변화가 있나요?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)를 참조하세요. 

구독 상태 | Data | 데이터 액세스
----- | ----- | -----
활성(평가판의 경우 30일) | 모두 데이터에 액세스할 수 있음 | 사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<br>관리자가 Microsoft 365 관리 센터 및 리소스에 대 한 일반적인 액세스 
만료됨(30일) | 모두 데이터에 액세스할 수 있음| 사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<br>관리자가 Microsoft 365 관리 센터 및 리소스에 대 한 일반적인 액세스
사용 안 함(30일) | 관리자만 데이터에 액세스할 수 있음 | 사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<br>관리자 수 Microsoft 365 관리 센터에 액세스 하지만 라이선스를 할당 하거나 업데이트할 수 없습니다 사용자
프로비전 해제됨(사용 안 함으로 설정된 후 30일) | 데이터가 삭제됨(사용 중인 다른 서비스가 없는 경우 자동으로 삭제됨) | 사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<br>관리자는 다른 구독을 구입 및 관리 Microsoft 365 관리 센터를 액세스할 수 있습니다.

## <a name="delete-a-subscription"></a>구독 삭제

Microsoft 365 관리 센터를 사용 하 여 3 일에서 삭제할 프로 비전 해제 됨 상태에 구독을 넣을 수 있습니다.

1. 에 로그인 합니다 [Microsoft 365 관리 센터](https://admin.microsoft.com) 조직의 전역 관리자 인 계정입니다. 초기 기본 도메인 contoso.onmicrosoft.com에 있는 "Contoso" 디렉터리를 삭제 하려는 경우 UPN을 같은 로그인 admin@contoso.onmicrosoft.com합니다.

2. 선택 **청구** 선택한 **구독**를 취소 하려는 구독을 선택 합니다. **취소**를 클릭한 후 페이지를 새로 고칩니다.
  
   ![구독 삭제를 위한 삭제 링크](./media/directory-delete-howto/delete-command.png)
  
3. **삭제**를 선택하여 구독을 삭제하고 사용 약관에 동의합니다. 모든 데이터는 3일 이내에 영구적으로 삭제됩니다. 생각이 바뀌는 경우 이 3일 동안 구독을 다시 활성화할 수 있습니다.
  
   ![신중 하 게 사용 약관 읽기](./media/directory-delete-howto/delete-terms.png)

4. 이제 구독 상태가 변경되었으므로 구독이 삭제되도록 표시됩니다. 구독은 72시간 후에 **프로비전 해제됨** 상태가 됩니다.

5. 72 시간 경과 서명할 수 있습니다 디렉터리에서 구독을 삭제 한 후 Azure AD 관리 센터를 되돌려 다시 및 있습니다 및 여야 합니다 필요한 작업이 없습니다에 디렉터리 삭제를 차단 하는 구독이 없습니다. 성공적으로 Azure AD 디렉터리를 삭제할 수 있어야 합니다.
  
   ![삭제 시 구독 검사 통과 화면](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>삭제를 차단 하는 평가판 구독을가지고

가지 [셀프 서비스 등록 제품](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) Microsoft Power BI, Rights Management Services, Microsoft Powerapps 또는 Dynamics 365 개별 사용자가.dgml 파일에서 인증을 위해 게스트 사용자는 Office 365를 통해 등록할 수와 같은 Azure AD 디렉터리에 있습니다. 데이터 손실을 방지 하기 위해 디렉터리에서 완벽 하 게 삭제 될 때 까지는 이러한 셀프 서비스 제품 디렉터리 삭제를 차단 합니다. 이러한 사용자를 개별적으로 등록 또는 제품에 할당 된 Azure AD 관리자가만 삭제할 수 있습니다.

셀프 서비스 등록 할당 되는 방법에 다음과 같은 두 종류가 있습니다. 

* 조직 수준 할당 합니다. Azure AD 관리자를 전체 조직에 제품을 할당 하 고 사용자 수 적극적으로 사용할 서비스를이 조직 수준 할당을 사용 하 여 개별적으로 라이선스 되지 경우에 키를 누릅니다.
* 사용자 수준 할당 합니다. 셀프 서비스 등록 하는 동안 개별 사용자를 기본적으로 제품을 할당 자체 관리자 없이 조직 관리자가 관리 되 면 (참조 [관리 되지 않는 디렉터리의 관리자 인수](domains-admin-takeover.md), 다음 관리자 셀프 서비스 등록 없는 사용자에 게 직접 제품을 할당할 수 있습니다.  

셀프 서비스 등록 제품의 삭제를 시작 작업을 영구적으로 데이터를 삭제 하 고 서비스에 대 한 모든 사용자 액세스를 제거 합니다. 조직 수준에 개별적으로 또는 제품이 할당 된 모든 사용자는 다음에 로그인 또는 기존 데이터에 액세스에서 차단 됩니다. 셀프 서비스 등록 제품을 사용 하 여 데이터 손실을 방지 하려는 경우와 같은 [Microsoft Power BI 대시보드](https://docs.microsoft.com/power-bi/service-export-to-pbix) 또는 [Rights Management Services 정책 구성을](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), 데이터 백업 되었는지 확인 하 고 다른 장소에 저장 합니다.

현재 사용할 수 있는 셀프 서비스 등록 제품 및 서비스에 대 한 자세한 내용은 참조 하세요. [사용 가능한 셀프 서비스 프로그램](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)합니다.

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대 한 자세한 내용은 참조 하세요. [Office 365 비즈니스 구독에 대 한 종료 되 면 내 데이터 및 액세스 되나요?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)합니다.

제품 상태 | Data | 데이터 액세스
------------- | ---- | --------------
활성(평가판의 경우 30일) | 모두 데이터에 액세스할 수 있음 | 사용자가 셀프 서비스 등록 제품, 파일 또는 앱에 대 한 일반 액세스<br>관리자가 Microsoft 365 관리 센터 및 리소스에 대 한 일반적인 액세스
Deleted | 데이터 삭제 | 사용자는 셀프 서비스 등록 제품, 파일 또는 앱에 액세스할 수 없습니다.<br>관리자는 다른 구독을 구입 및 관리 Microsoft 365 관리 센터를 액세스할 수 있습니다.

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Azure portal에서 셀프 서비스 등록 제품을 삭제 하려면 어떻게 해야 하나요?

Microsoft Power BI 또는에 Azure Rights Management Services와 같은 셀프 서비스 등록 제품을 넣을 수 있습니다는 **삭제** 상태를 Azure AD 포털에서 즉시 삭제 됩니다.

1. 에 로그인 합니다 [Azure AD 관리 센터](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 조직의 전역 관리자 인 계정입니다. 초기 기본 도메인 contoso.onmicrosoft.com에 있는 "Contoso" 디렉터리를 삭제 하려는 경우 로그인 UPN을 가진 같은 admin@contoso.onmicrosoft.com합니다.

2. 선택 **라이선스**를 선택한 후 **셀프 서비스 등록 제품**합니다. 모든 셀프 서비스 등록 제품 사용자 단위 구독에서 개별적으로 볼 수 있습니다. 영구적으로 삭제할 제품을 선택 합니다. Microsoft Power BI에서 예제는 다음과 같습니다.

    ![사용자 이름은 잘못 입력 되었거나 찾을 수 없음](./media/directory-delete-howto/licenses-page.png)

3. 선택 **삭제할** 제품 삭제 조건에 동의 하는 데이터가 즉시 삭제 됩니다 취소할 수 없습니다. 이 삭제 작업은 모든 사용자를 제거 하 고 제품에 대 한 조직 액세스를 제거 합니다. 삭제를 사용 하 여 앞으로 이동 하려면 예 클릭 합니다.  

    ![사용자 이름은 잘못 입력 되었거나 찾을 수 없음](./media/directory-delete-howto/delete-product.png)

4. 선택 하면 **예**, 셀프 서비스 제품의 삭제를 시작 합니다. 사실을 알려 주는 삭제 진행 중 알림이 있습니다.  

    ![사용자 이름은 잘못 입력 되었거나 찾을 수 없음](./media/directory-delete-howto/progress-message.png)

5. 셀프 서비스 등록 제품 상태로 변경 하는 이제 **Deleted**합니다. 페이지를 새로 고치면 제품에서 제거 해야 합니다 **셀프 서비스 등록 제품** 페이지입니다.  

    ![사용자 이름은 잘못 입력 되었거나 찾을 수 없음](./media/directory-delete-howto/product-deleted.png)

6. 모든 제품을 삭제 한 후 다시 Azure AD 관리 센터에 다시 로그인 할 수 있습니다와 필요한 조치가 및 디렉터리 삭제를 차단 하는 제품은 없습니다 있어야 합니다. 성공적으로 Azure AD 디렉터리를 삭제할 수 있어야 합니다.

    ![사용자 이름은 잘못 입력 되었거나 찾을 수 없음](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>다음 단계

[Azure Active Directory 설명서](https://docs.microsoft.com/azure/active-directory/)
