---
title: Azure Active Directory에서 관리자 역할 사용 권한 | Microsoft Docs
description: 관리자 역할은 사용자를 추가하고, 관리자 역할을 할당하며, 사용자 암호를 다시 설정하고, 사용자 라이선스 또는 도메인을 관리하는 데 사용할 수 있습니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 10/26/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 75fe35a22ccae249b734f05bd4adcaf8ddcab9f8
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995102"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할 사용 권한

Azure AD(Azure Active Directory)를 사용하여 다른 기능을 담당하도록 별도의 관리자를 지정할 수 있습니다. 관리자는 Azure AD 포털에서 사용자 추가 또는 변경, 관리 역할 할당, 사용자 암호 재설정, 사용자 라이선스 관리 및 도메인 이름 관리와 같은 작업을 수행하도록 지정할 수 있습니다.

전역 관리자는 모든 관리 기능에 액세스할 수 있습니다. 기본적으로 Azure 구독에 등록하는 사람에게는 디렉터리에 대한 글로벌 관리자 역할이 할당됩니다. 글로벌 관리자와 권한 있는 역할 관리자만 관리자 역할을 위임할 수 있습니다.

## <a name="assign-or-remove-administrator-roles"></a>관리자 역할 할당 또는 제거

Azure Active Directory에서 사용자에게 관리 역할을 할당하는 방법을 알아보려면 [Azure Active Directory에서 관리자 역할 보기 및 할당](directory-manage-roles-portal.md)을 참조하세요.

## <a name="available-roles"></a>사용 가능한 역할

다음과 같은 관리자 역할을 사용할 수 있습니다.

* **[애플리케이션 관리자](#application-administrator)**: 이 역할의 사용자는 엔터프라이즈 애플리케이션, 애플리케이션 등록 및 애플리케이션 프록시 설정의 모든 측면을 만들고 관리할 수 있습니다. 또한 이 역할은 위임된 권한 및 Microsoft Graph와 Azure AD Graph를 제외한 애플리케이션 사용 권한에 동의하는 기능을 부여합니다. 이 역할의 구성원은 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가되지 않습니다.

  <b>중요</b>: 이 역할은 애플리케이션 자격 증명을 관리하는 기능을 부여합니다. 이 역할이 할당된 사용자는 애플리케이션에 자격 증명을 추가하고 해당 자격 증명을 사용하여 애플리케이션의 ID를 가장할 수 있습니다. 애플리케이션의 ID에 사용자 또는 다른 개체를 만들거나 업데이트하는 기능과 같이 Azure Active Directory에 대한 액세스 권한이 부여된 경우 이 역할에 할당된 사용자는 애플리케이션을 가장하는 동안 이러한 작업을 수행할 수 있습니다. 애플리케이션의 ID를 가장하는 이 기능은 Azure AD에서 본인의 역할 할당을 통해 사용자가 수행할 수 있는 권한 상승이 될 수 있습니다. 애플리케이션 관리자 역할에 사용자를 할당하면 애플리케이션의 ID를 가장하는 기능이 해당 사용자에게 부여된다는 점을 이해해야 합니다.

* **[애플리케이션 개발자](#application-developer)**: 이 역할의 사용자는 "사용자가 애플리케이션을 등록할 수 있음" 설정이 아니오로 설정된 경우 애플리케이션 등록을 만들 수 있습니다. 또한 이 역할은 “사용자가 앱이 사용자 대신 회사 데이터에 액세스하는 것에 동의할 수 있음” 설정이 아니오로 설정된 경우 구성원이 직접 동의하도록 허용합니다. 이 역할의 구성원은 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가됩니다.

* **[대금 청구 관리자](#billing-administrator)**: 구매, 구독 관리, 지원 티켓 관리 및 서비스 상태 모니터링을 수행할 수 있습니다.

* **[클라우드 애플리케이션 관리자](#cloud-application-administrator)**: 이 역할의 사용자는 애플리케이션 관리자 역할과 동일한 권한을 가집니다. 다만 애플리케이션 프록시를 관리하는 권한은 없습니다. 이 역할은 엔터프라이즈 애플리케이션 및 애플리케이션 등록의 모든 측면을 만들고 관리하는 기능을 부여합니다. 또한 이 역할은 위임된 권한 및 Microsoft Graph와 Azure AD Graph를 제외한 애플리케이션 사용 권한에 동의하는 기능을 부여합니다. 이 역할의 구성원은 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가되지 않습니다.

  <b>중요</b>: 이 역할은 애플리케이션 자격 증명을 관리하는 기능을 부여합니다. 이 역할이 할당된 사용자는 애플리케이션에 자격 증명을 추가하고 해당 자격 증명을 사용하여 애플리케이션의 ID를 가장할 수 있습니다. 애플리케이션의 ID에 사용자 또는 다른 개체를 만들거나 업데이트하는 기능과 같이 Azure Active Directory에 대한 액세스 권한이 부여된 경우 이 역할에 할당된 사용자는 애플리케이션을 가장하는 동안 이러한 작업을 수행할 수 있습니다. 애플리케이션의 ID를 가장하는 이 기능은 Azure AD에서 본인의 역할 할당을 통해 사용자가 수행할 수 있는 권한 상승이 될 수 있습니다. 클라우드 애플리케이션 관리자 역할에 사용자를 할당하면 애플리케이션의 ID를 가장하는 기능이 해당 사용자에게 부여된다는 점을 이해해야 합니다.

* **[클라우드 디바이스 관리자](#cloud-device-administrator)**: 이 역할의 사용자는 Azure AD에서 디바이스를 사용하고 사용하지 않도록 설정하며, 삭제하고, Azure Portal에서 Windows 10 BitLocker 키(있는 경우)를 읽을 수 있습니다. 역할은 디바이스에서 다른 속성을 관리하는 사용 권한을 부여하지 않습니다.

* **[규정 준수 관리자](#compliance-administrator)**: 이 역할을 가진 사용자는 Office 365 보안 및 준수 센터와 Exchange 관리 센터 내의 관리 권한을 소유합니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **[조건부 액세스 관리자](#conditional-access-administrator)**: 이 역할을 가진 사용자는 Azure Active Directory 조건부 액세스 설정을 관리할 수 있습니다.
  > [!NOTE]
  > Azure에서 Exchange ActiveSync 조건부 액세스 정책을 배포하려면 사용자도 글로벌 관리자여야 합니다.
  
* **[디바이스 관리자](#device-administrators)**: 이 역할은 [디바이스 설정](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)의 추가 로컬 관리자로서 할당을 위해서만 사용할 수 있습니다. 이 역할을 가진 사용자가 Azure Active Directory에 가입된 모든 Windows 10 디바이스에서 로컬 컴퓨터 관리자가 됩니다. Azure Active Directory의 디바이스 개체를 관리하는 기능이 없습니다. 

* **[디렉터리 읽기 권한자](#directory-readers)**: [동의 프레임워크](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)를 지원하지 않는 애플리케이션에 할당될 레거시 역할입니다. 이 역할은 어느 사용자에게나 할당되면 안 됩니다.

* **[디렉터리 동기화 계정](#directory-synchronization-accounts)**: 사용 안 함. 이 역할은 Azure AD Connect 서비스에 자동으로 할당되고 다른 사용에 적합하거나 지원되지 않습니다.

* **[디렉터리 작성자](#directory-writers)**: [동의 프레임워크](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)를 지원하지 않는 애플리케이션에 할당될 레거시 역할입니다. 이 역할은 어느 사용자에게나 할당되면 안 됩니다.

* **[Dynamics 365 관리자/CRM 관리자](#dynamics-365-administrator)**: 이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Dynamics 365 Online 내에서 글로벌 사용 권한을 가질 뿐만 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [서비스 관리자 역할을 사용하여 테넌트 관리](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)를 참조하세요.
  > [!NOTE] 
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 “Dynamics 365 서비스 관리자”로 식별됩니다. Azure Portal에서는 “Dynamics 365 관리자”입니다.

* **[Exchange 관리자](#exchange-administrator)**: 이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Exchange Online 내에서 글로벌 사용 권한을 갖습니다. 모든 Office 365 그룹 만들기 및 관리 기능뿐만 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능도 포함합니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.
  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 “Exchange 서비스 관리자”로 식별됩니다. Azure Portal에서는 “Exchange 관리자”입니다.

* **[글로벌 관리자/회사 관리자](#company-administrator)**: 이 역할의 사용자는 Azure Active Directory의 모든 관리 기능뿐만 아니라 Exchange Online, SharePoint Online 및 비즈니스용 Skype Online과 같은 Azure Active DirectoryID를 사용하는 서비스에 대한 액세스 권한을 갖습니다. Azure Active Directory 테넌트에 등록하는 사람이 전역 관리자가 됩니다. 전역 관리자만 다른 관리자 역할을 할당할 수 있습니다. 회사에 여러 전역 관리자가 있을 수 있습니다. 전역 관리자는 모든 사용자 및 모든 다른 관리자의 암호를 다시 설정할 수 있습니다.

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "회사 관리자"로 식별됩니다. [Azure portal](https://portal.azure.com)에서는 "전역 관리자"입니다.
  >
  >

* **[게스트 초대자](#guest-inviter)**: 이 역할의 사용자는 **멤버가 초대할 수 있음** 사용자 설정을 아니요로 설정하는 경우 Azure Active Directory B2B 게스트 사용자 초대를 관리할 수 있습니다. B2B 공동 작업에 대한 자세한 내용은 [Azure AD B2B 공동 작업 정보](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 참조하세요. 다른 권한은 포함되지 않습니다.

* **[Information Protection 관리자](#information-protection-administrator)**: 이 역할을 가진 사용자는 Azure Information Protection 서비스에 대한 모든 사용 권한을 갖습니다. 이 역할은 Azure Information Protection 정책에 대한 레이블을 구성하고, 보호 템플릿을 관리하고, 보호를 활성화하는 권한을 갖습니다. 이 역할은 ID 보호 센터, Privileged Identity Management, Office 365 Service Health 또는 Office 365 보안 및 준수 센터에서 어느 권한도 부여하지 않습니다.

* **[Intune 관리자](#intune-administrator)**: 이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Intune Online 내에서 글로벌 사용 권한을 갖습니다. 또한 이 역할은 정책을 연결하고 그룹을 만들고 관리하기 위해 사용자와 디바이스를 관리하는 기능을 포함합니다. 자세한 내용은 [Microsoft Intune에서 RBAC(역할 기반 관리 제어)](https://docs.microsoft.com/intune/role-based-access-control)를 참조하세요
  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 “Intune 서비스 관리자”로 식별됩니다. Azure Portal에서는 “Intune 관리자”입니다.

* **[라이선스 관리자](#license-administrator)**: 이 역할의 사용자는 사용자 및 그룹의(그룹 기반 라이선스 사용) 라이선스 할당을 추가, 제거 및 업데이트하고, 사용자의 사용 위치를 관리합니다. 이 역할은 사용 위치를 벗어나서 구독을 구매 또는 관리하고, 그룹을 만들거나 관리하고, 사용자를 만들거나 관리하는 기능을 부여하지 않습니다.

* **[메시지 센터 읽기 권한자](#message-center-reader)**: 이 역할의 사용자는 Exchange, Intune 및 Microsoft Teams와 같은 구성된 서비스에서 조직에 대한 [Office 365 메시지 센터](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)의 알림 및 자문 상태 업데이트를 모니터링할 수 있습니다. 메시지 센터 읽기 권한자는 게시물 및 업데이트 이메일 다이제스트를 매주 수신하며, 메시지 센터 게시물을 Office 365에서 공유할 수 있습니다. Azure AD에서 이 역할에 할당된 사용자는 Azure AD 서비스에서 사용자 및 그룹처럼 읽기 전용 권한만 있습니다. 

* **[파트너 계층1 지원](#partner-tier1-support)**: 사용 안 함. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

* **[파트너 계층2 지원](#partner-tier2-support)**: 사용 안 함. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

* **[암호 관리자/기술 지원팀 관리자](#helpdesk-administrator)**: 이 역할을 가진 사용자는 암호를 변경하고, 새로 고침 토큰을 무효화하고, 서비스 요청을 관리하며, 서비스 상태를 모니터링할 수 있습니다. 새로 고침 토큰을 무효화하면 사용자가 다시 로그인해야 합니다. 기술 지원팀 관리자는 암호를 재설정하고 다음 역할의 멤버 또는 관리자가 아닌 다른 사용자의 새로 고침 토큰을 무효화할 수 있습니다.
  * 디렉터리 읽기 권한자
  * 게스트 초대자
  * 기술 지원팀 관리자
  * 메시지 센터 읽기 권한자
  * 보고서 구독자
  
  <b>중요</b>: 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 개인 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 암호를 변경할 수 있습니다. 사용자의 암호를 변경한다는 것은 사용자의 ID 및 권한을 가정할 수 있다는 것을 의미합니다. 예: 
  * 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 권한이 부여되었을 수 있으며, 다른 위치에서는 기술 지원 팀 관리자에 권한이 부여되지 않습니다. 이 경로를 통해 기술 지원 팀 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
  * Azure 구독 소유자: Azure에서 중요한 개인 정보 또는 중요한 구성에 액세스할 수 있습니다.
  * 보안 그룹 및 Office 365 그룹 소유자: 그룹 멤버 자격을 관리할 수 있습니다. 해당 그룹은 중요한 개인 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
  * Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
  * 중요한 개인 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "기술 지원팀 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com/)에서 "암호 관리자"입니다.
  >
  
* **[Power BI 관리자](#power-bi-administrator)**: 이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Power BI 내에서 글로벌 사용 권한을 가질 뿐만 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [Power BI 관리자 역할 이해](https://docs.microsoft.com/power-bi/service-admin-role)를 참조하세요.
  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 “Power BI 서비스 관리자”로 식별됩니다. Azure Portal에서는 “Power BI 관리자”입니다.

* **[권한 있는 역할 관리자](#privileged-role-administrator)**: 이 역할을 가진 사용자는 Azure Active Directory 및 Azure AD Privileged Identity Management에서 역할 할당을 관리할 수 있습니다. 또한 이 역할을 통해 Privileged Identity Management의 모든 측면을 관리할 수 있습니다.

  <b>중요</b>: 이 역할은 글로벌 관리자 역할을 포함하여 모든 Azure AD 역할의 멤버 자격을 관리하는 기능을 부여합니다. 이 역할은 사용자 생성 또는 업데이트와 같은 Azure AD의 다른 모든 권한 있는 기능을 포함하지는 않습니다. 그러나 이 역할에 할당된 사용자는 추가 역할을 할당하여 본인 또는 다른 사용자에게 추가 권한을 부여할 수 있습니다.

* **[보고서 읽기 권한자](#reports-reader)**: 이 역할을 가진 사용자는 Office 365 관리 센터의 사용 현황 보고 데이터 및 보고서 대시보드와 Power BI의 채택 컨텍스트 팩을 볼 수 있습니다. 또한 역할은 Azure AD의 로그온 보고서 및 활동과 함께 Microsoft Graph Reporting API에서 반환되는 데이터에 대한 액세스를 제공합니다. 보고서 구독자 역할에 할당된 사용자는 관련된 사용량 및 채택 메트릭에만 액세스할 수 있습니다. 설정을 구성하거나 Exchange와 같은 제품 특정 관리 센터에 액세스할 수 있는 관리자 권한은 없습니다. 

* **[보안 관리자](#security-administrator)**: 이 역할을 가진 사용자는 보안 읽기 권한자 역할의 모든 읽기 전용 권한을 갖는 동시에 다음과 같은 보안 관련 서비스에 대한 구성을 관리할 수 있습니다. Azure Active Directory Identity Protection, Azure Information Protection 및 Office 365 보안 및 준수 센터 Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에서 제공됩니다.
  
  | 그런 다음 | 가능한 작업 |
  | --- | --- |
  | ID 보호 센터 |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>또한 암호 재설정을 제외한 모든 IPC 작업을 수행할 능력. |
  | Privileged Identity Management |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>**없습니다** . |
  | <p>Office 365 서비스 상태 모니터링</p><p>Office 365 보안 및 규정 준수 센터 |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>고급 위협 방지 기능(맬웨어 및 바이러스 보호, 악성 URL 구성, URL 추적 등)에서 모든 설정을 구성할 수 있습니다. |
  
* **[보안 읽기 권한자](#security-reader)**: 이 역할의 사용자는 Azure Active Directory, Identity Protection, Privileged Identity Management의 모든 정보를 포함하여 글로벌 읽기 전용 액세스 권한을 갖을 뿐만 아니라 Azure Active Directory 로그인 보고서 및 감사 로그를 읽을 수 있습니다. 또한 역할은 Office 365 보안 및 규정 준수 센터의 읽기 전용 권한을 부여합니다. Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에서 제공됩니다.

  | 그런 다음 | 가능한 작업 |
  | --- | --- |
  | ID 보호 센터 |모든 보안 보고서 및 보안 기능에 대한 설정 정보를 읽습니다.<ul><li>스팸 방지<li>암호화<li>데이터 손실 방지<li>맬웨어 방지<li>Advanced Threat Protection<li>피싱 방지<li>메일 흐름 규칙 |
  | Privileged Identity Management |<p>Azure AD PIM에 표시되는 다음과 같은 모든 정보에 대한 읽기 전용 액세스 권한을 갖습니다. Azure AD 역할 할당에 대한 정책 및 보고서, 보안 검토, Azure AD 역할 할당 외의 시나리오에 대한 정책 데이터 및 보고서에 대한 향후 읽기 액세스 권한<p>Azure AD PIM에 로그인을 하거나 어떠한 변경도 할 수 **없습니다**. PIM 포털이나 PowerShell을 통해, 이 역할을 가진 담당자는 사용자가 후보자일 경우 추가 역할(예: 전역 관리자 또는 권한 있는 역할 관리자)을 활성화할 수 있습니다. |
  | <p>Office 365 서비스 상태 모니터링</p><p>Office 365 보안 및 규정 준수 센터</p> |<ul><li>경고 읽기 및 관리<li>보안 정책 읽기<li>위협 인텔리전스, 클라우드 앱 검색 및 검색 및 조사의 격리 읽기<li>모든 보고서 읽기 |

* **[서비스 지원 관리자](#service-support-administrator)**: 이 역할을 가진 사용자는 Microsoft for Azure 및 Office 365 서비스에서 지원 요청을 열 수 있으며, Azure Portal 및 Office 365 관리 포털에서 서비스 대시보드 및 메시지 센터를 볼 수 있습니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **[SharePoint 관리자](#sharepoint-administrator)**: 이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft SharePoint Online 내에서 글로벌 사용 권한을 가질 뿐만 아니라 모든 Office 365 그룹을 생성 및 관리하고, 지원 티켓을 관리하고, 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.
  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 “SharePoint 서비스 관리자”로 식별됩니다. Azure Portal에서는 “SharePoint 관리자”입니다.

* **[비즈니스용 Skype/Lync 관리자](#skype-for-business-administrator)**: 이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft 비즈니스용 Skype 내에서 글로벌 사용 권한을 가질 뿐만 아니라 Azure Active Directory에서 Skype 관련 사용자 특성을 관리할 수 있습니다. 또한 이 역할은 지원 티켓을 관리하고 서비스 상태를 모니터링하고, Teams 및 Business용 Skype 관리 센터에 액세스하는 기능을 부여합니다. 계정에는 Teams에 대한 라이선스가 있어야 합니다. 그렇지 않으면 Teams PowerShell cmdlet을 실행할 수 없습니다. [비즈니스용 Skype 관리자 역할 정보](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)의 자세한 내용 및 [비즈니스용 Skype 및 Microsoft Teams 추가 기능 라이선스](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)의 Teams 라이선스 정보

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "Lync Service 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com/)에서는 “비즈니스용 Skype 관리자”입니다.

* **[Teams 통신 관리자](#teams-communications-administrator)**: 이 역할의 사용자는 음성 및 전화 통신과 관련된 Microsoft Teams 워크로드의 측면을 관리할 수 있습니다. 여기에는 전화 번호 할당, 음성 및 회의 정책 및 호출 분석 도구 집합에 대한 전체 액세스를 위한 관리 도구가 포함됩니다.

* **[Teams 통신 지원 엔지니어](#teams-communications-support-engineer)**: 이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터에서 사용자 호출 문제 해결 도구를 사용하여 Microsoft Teams 및 비즈니스용 Skype 내에서 통신 문제를 해결할 수 있습니다. 이 역할의 사용자는 관련된 모든 참가자에 대한 전체 호출 레코드 정보를 볼 수 있습니다.

* **[Teams 통신 지원 전문가](#teams-communications-support-specialist)**: 이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터에서 사용자 호출 문제 해결 도구를 사용하여 Microsoft Teams 및 비즈니스용 Skype 내에서 통신 문제를 해결할 수 있습니다. 이 역할의 사용자는 조회하는 특정 사용자에 대한 호출에서 사용자 세부 정보를 보기만 할 수 있습니다.

* **[Teams 관리자](#teams-administrator)**: 이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터와 해당하는 PowerShell 모듈을 통해 Microsoft Teams 워크로드의 모든 측면을 관리할 수 있습니다. 여기에는 다른 영역 중 전화 통신, 메시징, 회의 및 팀 자체와 관련된 모든 관리 도구가 포함됩니다. 이 역할은 추가적으로 모든 Office 365 그룹 만들기 및 관리 기능뿐만 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능도 부여합니다.
  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 “Teams 서비스 관리자”로 식별됩니다. Azure Portal에서는 “Teams 관리자”입니다.

* **[사용자 계정 관리자](#user-account-administrator)**: 이 역할의 사용자는 사용자를 만들고, 사용자의 모든 측면을 제한적으로 관리할 수 있습니다(아래 참조). 또한 이 역할의 사용자는 모든 그룹을 만들고 관리할 수 있습니다. 이 역할은 사용자 보기를 만들고 관리하며, 지원 티켓을 관리하고, 서비스 상태를 모니터링하는 기능도 포함합니다.

  | | |
  | --- | --- |
  |일반적인 사용 권한|<p>사용자 및 그룹 만들기</p><p>사용자 보기 만들기 및 관리</p><p>Office 지원 티켓 관리|
  |<p>모든 관리자를 포함한 모든 사용자에게</p>|<p>라이선스 관리</p><p>사용자 계정 이름을 제외한 모든 사용자 속성 관리</p>
  |비관리자 또는 다음의 제한된 관리자 역할의 사용자에만 적용:<ul><li>디렉터리 읽기 권한자<li>게스트 초대자<li>기술 지원팀 관리자<li>메시지 센터 읽기 권한자<li>보고서 구독자<li>사용자 계정 관리자|<p>삭제 및 복원</p><p>사용 안 함 및 사용</p><p>새로 고침 토큰 무효화</p><p>사용자 계정 이름을 포함한 모든 사용자 속성 관리</p><p>암호 재설정</p><p>(FIDO) 디바이스 키 업데이트</p>
  
  <b>중요</b>: 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 개인 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 암호를 변경할 수 있습니다. 사용자의 암호를 변경한다는 것은 사용자의 ID 및 권한을 가정할 수 있다는 것을 의미합니다. 예: 
  * 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 권한이 부여되었을 수 있으며, 다른 위치에서는 사용자 관리자에게 권한이 부여되지 않습니다. 이 경로를 통해 사용자 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
  * Azure 구독 소유자: Azure에서 중요한 개인 정보 또는 중요한 구성에 액세스할 수 있습니다.
  * 보안 그룹 및 Office 365 그룹 소유자: 그룹 멤버 자격을 관리할 수 있습니다. 해당 그룹은 중요한 개인 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
  * Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
  * 중요한 개인 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자

## <a name="role-permissions"></a>역할 권한
다음 표에서는 각 역할에 부여되는 Azure Active Directory의 특정 권한에 대해 설명합니다. 일부 역할에는 Azure Active Directory 외부의 Microsoft 서비스에 대한 추가 사용 권한이 있을 수 있습니다.

### <a name="application-administrator"></a>애플리케이션 관리자
앱 등록 및 엔터프라이즈 앱의 모든 측면을 만들고 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Azure Active Directory에서 applications.audience 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/authentication/update | Azure Active Directory에서 applications.authentication 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/basic/update | Azure Active Directory에서 애플리케이션의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/create | Azure Active Directory에서 애플리케이션을 만듭니다. |
| microsoft.aad.directory/applications/credentials/update | Azure Active Directory에서 applications.credentials 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/delete | Azure Active Directory에서 애플리케이션을 삭제합니다. |
| microsoft.aad.directory/applications/owners/update | Azure Active Directory에서 applications.owners 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/permissions/update | Azure Active Directory에서 applications.permissions 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory에서 appRoleAssignments를 만듭니다. |
| microsoft.aad.directory/appRoleAssignments/read | Azure Active Directory에서 appRoleAssignments를 읽습니다. |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory에서 appRoleAssignments를 업데이트합니다. |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory에서 appRoleAssignments를 삭제합니다. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Azure Active Directory에서 policies.applicationConfiguration 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Azure Active Directory에서 policies.applicationConfiguration 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory에서 servicePrincipals를 만듭니다. |
| microsoft.aad.directory/servicePrincipals/delete | Azure Active Directory에서 servicePrincipals를 삭제합니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.aad.reports/applicationAuditLogs/read | Azure AD 보고서에서 applicationAuditLogs를 읽습니다. |
| microsoft.aad.reports/applicationSignInReports/read | Azure AD 보고서에서 applicationSignInReports를 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="application-developer"></a>애플리케이션 개발자
‘사용자가 애플리케이션을 등록할 수 있음’ 설정에 관계없이 애플리케이션 등록을 만들 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Azure Active Directory에서 애플리케이션을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Azure Active Directory에서 appRoleAssignments를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Azure Active Directory에서 oAuth2PermissionGrants를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Azure Active Directory에서 servicePrincipals를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |

### <a name="billing-administrator"></a>대금 청구 관리자
결제 정보 업데이트와 같은 일반 결제 관련 작업을 수행할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Azure Active Directory에서 조직의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Azure Active Directory에서 organization.trustedCAsForPasswordlessAuth 속성을 업데이트합니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.commerce.billing/allEntities/allTasks | Office 365 청구의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="desktop-analytics-administrator"></a>데스크톱 분석 관리자
데스크톱 관리 도구 및 서비스(Intune 등)을 액세스하고 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 데스크톱 분석의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="cloud-application-administrator"></a>클라우드 애플리케이션 관리자
앱 프록시를 제외한 앱 등록 및 엔터프라이즈 앱의 모든 측면을 만들고 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Azure Active Directory에서 applications.audience 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/authentication/update | Azure Active Directory에서 applications.authentication 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/basic/update | Azure Active Directory에서 애플리케이션의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/create | Azure Active Directory에서 애플리케이션을 만듭니다. |
| microsoft.aad.directory/applications/credentials/update | Azure Active Directory에서 applications.credentials 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/delete | Azure Active Directory에서 애플리케이션을 삭제합니다. |
| microsoft.aad.directory/applications/owners/update | Azure Active Directory에서 applications.owners 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/permissions/update | Azure Active Directory에서 applications.permissions 속성을 업데이트합니다. |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory에서 appRoleAssignments를 만듭니다. |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory에서 appRoleAssignments를 업데이트합니다. |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory에서 appRoleAssignments를 삭제합니다. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Azure Active Directory에서 policies.applicationConfiguration 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Azure Active Directory에서 policies.applicationConfiguration 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory에서 servicePrincipals를 만듭니다. |
| microsoft.aad.directory/servicePrincipals/delete | Azure Active Directory에서 servicePrincipals를 삭제합니다. |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.aad.reports/applicationAuditLogs/read | Azure AD 보고서에서 applicationAuditLogs를 읽습니다. |
| microsoft.aad.reports/applicationSignInReports/read | Azure AD 보고서에서 applicationSignInReports를 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="cloud-device-administrator"></a>클라우드 디바이스 관리자
Azure AD에서 디바이스를 관리하기 위한 모든 권한입니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/devices/delete | Azure Active Directory에서 디바이스를 삭제합니다. |
| microsoft.aad.directory/devices/disable | Azure Active Directory에서 디바이스를 사용하지 않도록 설정합니다. |
| microsoft.aad.directory/devices/enable | Azure Active Directory에서 디바이스를 사용하도록 설정합니다. |
| microsoft.aad.reports/applicationAuditLogs/read | Azure AD 보고서에서 applicationAuditLogs를 읽습니다. |
| microsoft.aad.reports/applicationSignInReports/read | Azure AD 보고서에서 applicationSignInReports를 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="company-administrator"></a>회사 관리자
Azure AD 및 Azure AD ID를 사용하는 Microsoft 서비스의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Azure Active Directory에서 administrativeUnits를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/applications/allProperties/allTasks | Azure Active Directory에서 애플리케이션을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Azure Active Directory에서 appRoleAssignments를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Azure Active Directory에서 연락처를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Azure Active Directory에서 계약을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/devices/allProperties/allTasks | Azure Active Directory에서 디바이스를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Azure Active Directory에서 directoryRoles를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Azure Active Directory에서 directoryRoleTemplates를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/domains/allProperties/allTasks | Azure Active Directory에서 도메인을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/groups/allProperties/allTasks | Azure Active Directory에서 그룹을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Azure Active Directory에서 groupSettings를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Azure Active Directory에서 groupSettingTemplates를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Azure Active Directory에서 loginTenantBranding을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Azure Active Directory에서 oAuth2PermissionGrants를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/organization/allProperties/allTasks | Azure Active Directory에서 조직을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/policies/allProperties/allTasks | Azure Active Directory에서 정책을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Azure Active Directory에서 roleAssignments를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Azure Active Directory에서 roleDefinitions를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Azure Active Directory에서 scopedRoleMemberships를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/serviceAction/activateService | Azure Active Directory에서 Activateservice 서비스 작업을 수행할 수 있습니다. |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Azure Active Directory에서 Disabledirectoryfeature 서비스 작업을 수행할 수 있습니다. |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Azure Active Directory에서 Enabledirectoryfeature 서비스 작업을 수행할 수 있습니다. |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Azure Active Directory에서 Getavailableextentionproperties 서비스 작업을 수행할 수 있습니다. |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory에서 servicePrincipals를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Azure Active Directory에서 subscribedSkus를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/users/allProperties/allTasks | Azure Active Directory에서 사용자를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directorySync/allEntities/allTasks | Azure AD Connect에서 모든 작업을 수행합니다. |
| microsoft.aad.identityProtection/allEntities/allTasks | microsoft.aad.identityProtection에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 읽습니다. |
| microsoft.aad.reports/applicationAuditLogs/read | Azure AD 보고서에서 applicationAuditLogs를 읽습니다. |
| microsoft.aad.reports/applicationSignInReports/read | Azure AD 보고서에서 applicationSignInReports를 읽습니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.commerce.billing/allEntities/allTasks | Office 365 청구의 모든 측면을 관리합니다. |
| microsoft.intune/allEntities/allTasks | Intune의 모든 측면을 관리합니다. |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 준수 관리자의 모든 측면을 관리합니다. |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online의 모든 측면을 관리합니다. |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 고객 Lockbox의 모든 측면을 관리합니다. |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.messageCenter/securityMessages/read | microsoft.office365.messageCenter에서 securityMessages를 읽습니다. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI의 모든 측면을 관리합니다. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Office 365 보호 센터의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면을 관리합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365의 모든 측면을 관리합니다. |

### <a name="compliance-administrator"></a>규정 준수 관리자
Azure AD 및 Office 365에서 준수 구성 및 보고서를 읽고 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 준수 관리자의 모든 측면을 관리합니다. |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면을 관리합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="conditional-access-administrator"></a>조건부 액세스 관리자
조건부 액세스 기능을 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Azure Active Directory에서 policies.conditionalAccess 속성을 읽습니다. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Azure Active Directory에서 policies.conditionalAccess 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/conditionalAccess/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Azure Active Directory에서 policies.conditionalAccess 속성을 읽습니다. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Azure Active Directory에서 policies.conditionalAccess 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Azure Active Directory에서 policies.conditionalAccess 속성을 읽습니다. |

### <a name="crm-service-administrator"></a>CRM 서비스 관리자
Dynamics 365 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="customer-lockbox-access-approver"></a>고객 LockBox 액세스 승인자
고객 조직 데이터에 액세스하려는 Microsoft 지원 요청을 승인할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 고객 Lockbox의 모든 측면을 관리합니다. |

### <a name="device-administrators"></a>디바이스 관리자
이 역할의 구성원은 Azure AD에서 조인한 디바이스의 로컬 관리자 그룹에 추가됩니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Azure Active Directory에서 groupSettings의 기본 속성을 읽습니다. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Azure Active Directory에서 groupSettingTemplates의 기본 속성을 읽습니다. |

### <a name="directory-readers"></a>디렉터리 읽기 권한자
기본 디렉터리 정보를 읽을 수 있습니다. 애플리케이션에 대한 액세스 권한은 사용자를 위한 것이 아닙니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Azure Active Directory에서 administrativeUnits의 기본 속성을 읽습니다. |
| microsoft.aad.directory/administrativeUnits/members/read | Azure Active Directory에서 administrativeUnits.members 속성을 읽습니다. |
| microsoft.aad.directory/applications/audience/read | Azure Active Directory에서 applications.audience 속성을 읽습니다. |
| microsoft.aad.directory/applications/authentication/read | Azure Active Directory에서 applications.authentication 속성을 읽습니다. |
| microsoft.aad.directory/applications/basic/read | Azure Active Directory에서 애플리케이션의 기본 속성을 읽습니다. |
| microsoft.aad.directory/applications/credentials/read | Azure Active Directory에서 applications.credentials 속성을 읽습니다. |
| microsoft.aad.directory/applications/owners/read | Azure Active Directory에서 applications.owners 속성을 읽습니다. |
| microsoft.aad.directory/applications/permissions/read | Azure Active Directory에서 applications.permissions 속성을 읽습니다. |
| microsoft.aad.directory/applications/policies/read | Azure Active Directory에서 applications.policies 속성을 읽습니다. |
| microsoft.aad.directory/contacts/basic/read | Azure Active Directory에서 연락처의 표준 속성을 읽습니다. |
| microsoft.aad.directory/contacts/memberOf/read | Azure Active Directory에서 contacts.memberOf 속성을 읽습니다. |
| microsoft.aad.directory/contracts/basic/read | Azure Active Directory에서 계약의 기본 속성을 읽습니다. |
| microsoft.aad.directory/devices/basic/read | Azure Active Directory에서 디바이스의 기본 속성을 읽습니다. |
| microsoft.aad.directory/devices/memberOf/read | Azure Active Directory에서 devices.memberOf 속성을 읽습니다. |
| microsoft.aad.directory/devices/registeredOwners/read | Azure Active Directory에서 devices.registeredOwners 속성을 읽습니다. |
| microsoft.aad.directory/devices/registeredUsers/read | Azure Active Directory에서 devices.registeredUsers 속성을 읽습니다. |
| microsoft.aad.directory/directoryRoles/basic/read | Azure Active Directory에서 directoryRoles의 기본 속성을 읽습니다. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Azure Active Directory에서 directoryRoles.eligibleMembers 속성을 읽습니다. |
| microsoft.aad.directory/directoryRoles/members/read | Azure Active Directory에서 directoryRoles.members 속성을 읽습니다. |
| microsoft.aad.directory/domains/basic/read | Azure Active Directory에서 도메인의 기본 속성을 읽습니다. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Azure Active Directory에서 Groups.AppRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/groups/basic/read | Azure Active Directory에서 그룹의 기본 속성을 읽습니다. |
| microsoft.aad.directory/groups/memberOf/read | Azure Active Directory에서 groups.memberOf 속성을 읽습니다. |
| microsoft.aad.directory/groups/members/read | Azure Active Directory에서 groups.members 속성을 읽습니다. |
| microsoft.aad.directory/groups/owners/read | Azure Active Directory에서 groups.owners 속성을 읽습니다. |
| microsoft.aad.directory/groups/settings/read | Azure Active Directory에서 groups.settings 속성을 읽습니다. |
| microsoft.aad.directory/groupSettings/basic/read | Azure Active Directory에서 groupSettings의 기본 속성을 읽습니다. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Azure Active Directory에서 groupSettingTemplates의 기본 속성을 읽습니다. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 oAuth2PermissionGrants의 기본 속성을 읽습니다. |
| microsoft.aad.directory/organization/basic/read | Azure Active Directory에서 조직의 기본 속성을 읽습니다. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Azure Active Directory에서 organization.trustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/roleAssignments/basic/read | Azure Active Directory에서 roleAssignments의 기본 속성을 읽습니다. |
| microsoft.aad.directory/roleDefinitions/basic/read | Azure Active Directory에서 roleDefinitions의 기본 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/basic/read | Azure Active Directory에서 servicePrincipals의 기본 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Azure Active Directory에서 servicePrincipals.memberOf 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 servicePrincipals.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Azure Active Directory에서 servicePrincipals.ownedObjects 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/owners/read | Azure Active Directory에서 servicePrincipals.owners 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/policies/read | Azure Active Directory에서 servicePrincipals.policies 속성을 읽습니다. |
| microsoft.aad.directory/subscribedSkus/basic/read | Azure Active Directory에서 subscribedSkus의 기본 속성을 읽습니다. |
| microsoft.aad.directory/users/appRoleAssignments/read | Azure Active Directory에서 users.appRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/users/basic/read | Azure Active Directory에서 사용자의 기본 속성을 읽습니다. |
| microsoft.aad.directory/users/directReports/read | Azure Active Directory에서 users.directReports 속성을 읽습니다. |
| microsoft.aad.directory/users/invitedBy/read | Azure Active Directory에서 users.invitedBy 속성을 읽습니다. |
| microsoft.aad.directory/users/invitedUsers/read | Azure Active Directory에서 users.invitedUsers 속성을 읽습니다. |
| microsoft.aad.directory/users/manager/read | Azure Active Directory에서 users.manager 속성을 읽습니다. |
| microsoft.aad.directory/users/memberOf/read | Azure Active Directory에서 users.memberOf 속성을 읽습니다. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 users.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.aad.directory/users/ownedDevices/read | Azure Active Directory에서 users.ownedDevices 속성을 읽습니다. |
| microsoft.aad.directory/users/ownedObjects/read | Azure Active Directory에서 users.ownedObjects 속성을 읽습니다. |
| microsoft.aad.directory/users/registeredDevices/read | Azure Active Directory에서 users.registeredDevices 속성을 읽습니다. |

### <a name="directory-synchronization-accounts"></a>디렉터리 동기화 계정
Azure AD Connect에서만 사용됩니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Azure Active Directory에서 organization.dirSync 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/policies/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/policies/basic/read | Azure Active Directory에서 정책의 기본 속성을 읽습니다. |
| microsoft.aad.directory/policies/basic/update | Azure Active Directory에서 정책의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/owners/read | Azure Active Directory에서 policies.owners 속성을 읽습니다. |
| microsoft.aad.directory/policies/owners/update | Azure Active Directory에서 policies.owners 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Azure Active Directory에서 policies.policiesAppliedTo 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/basic/read | Azure Active Directory에서 servicePrincipals의 기본 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory에서 servicePrincipals를 만듭니다. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Azure Active Directory에서 servicePrincipals.memberOf 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 servicePrincipals.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/owners/read | Azure Active Directory에서 servicePrincipals.owners 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Azure Active Directory에서 servicePrincipals.ownedObjects 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/policies/read | Azure Active Directory에서 servicePrincipals.policies 속성을 읽습니다. |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.aad.directorySync/allEntities/allTasks | Azure AD Connect에서 모든 작업을 수행합니다. |

### <a name="directory-writers"></a>디렉터리 작성자
기본 디렉터리 정보를 읽고 쓸 수 있습니다. 애플리케이션에 대한 액세스 권한은 사용자를 위한 것이 아닙니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory에서 groups.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |
| microsoft.aad.directory/groupSettings/basic/update | Azure Active Directory에서 groupSettings의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/groupSettings/create | Azure Active Directory에서 groupSettings를 만듭니다. |
| microsoft.aad.directory/groupSettings/delete | Azure Active Directory에서 groupSettings를 삭제합니다. |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory에서 users.userPrincipalName 속성을 업데이트합니다. |

### <a name="exchange-service-administrator"></a>Exchange 서비스 관리자
Exchange 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Azure Active Directory에서 groups.unified 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/basic/update | Office 365 그룹의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/create | Office 365 그룹을 만듭니다. |
| microsoft.aad.directory/groups/unified/delete | Office 365 그룹을 삭제합니다. |
| microsoft.aad.directory/groups/unified/members/update | Office 365 그룹의 멤버 자격을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/owners/update | Office 365 그룹의 소유권을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="guest-inviter"></a>게스트 초대자
‘멤버가 게스트를 초대할 수 있음’ 설정에 관계없이 게스트 사용자를 초대할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Azure Active Directory에서 users.appRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/users/basic/read | Azure Active Directory에서 사용자의 기본 속성을 읽습니다. |
| microsoft.aad.directory/users/directReports/read | Azure Active Directory에서 users.directReports 속성을 읽습니다. |
| microsoft.aad.directory/users/invitedBy/read | Azure Active Directory에서 users.invitedBy 속성을 읽습니다. |
| microsoft.aad.directory/users/inviteGuest | Azure Active Directory에서 게스트 사용자를 초대합니다. |
| microsoft.aad.directory/users/invitedUsers/read | Azure Active Directory에서 users.invitedUsers 속성을 읽습니다. |
| microsoft.aad.directory/users/manager/read | Azure Active Directory에서 users.manager 속성을 읽습니다. |
| microsoft.aad.directory/users/memberOf/read | Azure Active Directory에서 users.memberOf 속성을 읽습니다. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 users.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.aad.directory/users/ownedDevices/read | Azure Active Directory에서 users.ownedDevices 속성을 읽습니다. |
| microsoft.aad.directory/users/ownedObjects/read | Azure Active Directory에서 users.ownedObjects 속성을 읽습니다. |
| microsoft.aad.directory/users/registeredDevices/read | Azure Active Directory에서 users.registeredDevices 속성을 읽습니다. |

### <a name="helpdesk-administrator"></a>기술 지원팀 관리자
관리자가 아닌 사용자 및 기술 지원팀 관리자의 암호를 재설정할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="information-protection-administrator"></a>Information Protection 관리자
Azure Information Protection 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="intune-service-administrator"></a>Intune 서비스 관리자
Intune 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Azure Active Directory에서 연락처의 기본 속성을 읽습니다. |
| microsoft.aad.directory/contacts/create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.aad.directory/contacts/delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.aad.directory/devices/basic/update | Azure Active Directory에서 디바이스의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/devices/create | Azure Active Directory에서 디바이스를 만듭니다. |
| microsoft.aad.directory/devices/delete | Azure Active Directory에서 디바이스를 삭제합니다. |
| microsoft.aad.directory/devices/registeredOwners/update | Azure Active Directory에서 devices.registeredOwners 속성을 업데이트합니다. |
| microsoft.aad.directory/devices/registeredUsers/update | Azure Active Directory에서 devices.registeredUsers 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory에서 groups.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.aad.directory/groups/hiddenMembers/read | Azure Active Directory에서 groups.hiddenMembers 속성을 읽습니다. |
| microsoft.aad.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.intune/allEntities/allTasks | Intune의 모든 측면을 관리합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="license-administrator"></a>라이선스 관리자
사용자 및 그룹의 제품 라이선스를 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/users/usageLocation/update | Azure Active Directory에서 users.usageLocation 속성을 업데이트합니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="lync-service-administrator"></a>Lync 서비스 관리자
비즈니스용 Skype 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면을 관리합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="message-center-reader"></a>메시지 센터 읽기 권한자
Office 365 메시지 센터에서만 조직의 메시지 및 업데이트를 읽을 수 있습니다. 

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |

### <a name="partner-tier1-support"></a>파트너 계층1 지원
사용하지 마세요. 일반적인 용도로는 적합하지 않습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Azure Active Directory에서 연락처의 기본 속성을 읽습니다. |
| microsoft.aad.directory/contacts/create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.aad.directory/contacts/delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.aad.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/users/delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.aad.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.aad.directory/users/restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory에서 users.userPrincipalName 속성을 업데이트합니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="partner-tier2-support"></a>파트너 계층2 지원
사용하지 마세요. 일반적인 용도로는 적합하지 않습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Azure Active Directory에서 연락처의 기본 속성을 읽습니다. |
| microsoft.aad.directory/contacts/create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.aad.directory/contacts/delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.aad.directory/domains/allTasks | Azure Active Directory에서 도메인을 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.aad.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.aad.directory/organization/basic/update | Azure Active Directory에서 조직의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Azure Active Directory에서 organization.trustedCAsForPasswordlessAuth 속성을 업데이트합니다. |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/users/delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.aad.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.aad.directory/users/restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory에서 users.userPrincipalName 속성을 업데이트합니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="power-bi-service-administrator"></a>Power BI 서비스 관리자
Power BI 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="privileged-role-administrator"></a>권한 있는 역할 관리자
Azure AD의 역할 할당 및 Privileged Identity Management의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Azure Active Directory에서 directoryRoles를 업데이트합니다. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |

### <a name="reports-reader"></a>보고서 구독자
로그인 및 감사 보고서를 읽을 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.reports/applicationAuditLogs/read | Azure AD 보고서에서 applicationAuditLogs를 읽습니다. |
| microsoft.aad.reports/applicationSignInReports/read | Azure AD 보고서에서 applicationSignInReports를 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.usageReports/allEntities/read | Office 365 사용 보고서를 읽습니다. |

### <a name="security-administrator"></a>보안 관리자
Azure AD 및 Office 365에서 보안 정보 및 보고서를 읽고 구성을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/basic/update | Azure Active Directory에서 정책의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/policies/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/policies/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/policies/owners/update | Azure Active Directory에서 policies.owners 속성을 업데이트합니다. |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection에서 모든 리소스를 읽습니다. |
| microsoft.aad.identityProtection/allEntities/update | microsoft.aad.identityProtection에서 모든 리소스를 업데이트합니다. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 읽습니다. |
| microsoft.aad.reports/applicationAuditLogs/read | Azure AD 보고서에서 applicationAuditLogs를 읽습니다. |
| microsoft.aad.reports/applicationSignInReports/read | Azure AD 보고서에서 applicationSignInReports를 읽습니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 보호 센터의 모든 측면을 읽습니다. |
| microsoft.office365.protectionCenter/allEntities/update | microsoft.office365.protectionCenter에서 모든 리소스를 업데이트합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="security-reader"></a>보안 판독기
Azure AD 및 Office 365에서 보안 정보 및 보고서를 읽을 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection에서 모든 리소스를 읽습니다. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 읽습니다. |
| microsoft.aad.reports/applicationAuditLogs/read | Azure AD 보고서에서 applicationAuditLogs를 읽습니다. |
| microsoft.aad.reports/applicationSignInReports/read | Azure AD 보고서에서 applicationSignInReports를 읽습니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 보호 센터의 모든 측면을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="service-support-administrator"></a>서비스 지원 관리자
서비스 상태 정보를 읽고, 지원 티켓을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="sharepoint-service-administrator"></a>SharePoint 서비스 관리자
SharePoint 서비스의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Azure Active Directory에서 groups.unified 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/basic/update | Office 365 그룹의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/create | Office 365 그룹을 만듭니다. |
| microsoft.aad.directory/groups/unified/delete | Office 365 그룹을 삭제합니다. |
| microsoft.aad.directory/groups/unified/members/update | Office 365 그룹의 멤버 자격을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/owners/update | Office 365 그룹의 소유권을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="teams-communications-administrator"></a>Teams 통신 관리자
Microsoft Teams 서비스 내에서 호출 및 회의 기능을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Azure Active Directory에서 정책의 기본 속성을 읽습니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.usageReports/allEntities/read | Office 365 사용 보고서를 읽습니다. |

### <a name="teams-communications-support-engineer"></a>Teams 통신 지원 엔지니어
고급 도구를 사용하여 Teams 내에서 통신 문제를 해결할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Azure Active Directory에서 정책의 기본 속성을 읽습니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="teams-communications-support-specialist"></a>Teams 통신 지원 전문가
기본 도구를 사용하여 Teams 내에서 통신 문제를 해결할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Azure Active Directory에서 정책의 기본 속성을 읽습니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="teams-service-administrator"></a>Teams 서비스 관리자
Microsoft Teams 서비스를 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Azure Active Directory에서 groups.hiddenMembers 속성을 읽습니다. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Azure Active Directory에서 groups.unified 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/basic/update | Office 365 그룹의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/create | Office 365 그룹을 만듭니다. |
| microsoft.aad.directory/groups/unified/delete | Office 365 그룹을 삭제합니다. |
| microsoft.aad.directory/groups/unified/members/update | Office 365 그룹의 멤버 자격을 업데이트합니다. |
| microsoft.aad.directory/groups/unified/owners/update | Office 365 그룹의 소유권을 업데이트합니다. |
| microsoft.aad.directory/policies/basic/read | Azure Active Directory에서 정책의 기본 속성을 읽습니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.usageReports/allEntities/read | Office 365 사용 보고서를 읽습니다. |

### <a name="user-account-administrator"></a>사용자 계정 관리자
제한된 관리자의 암호 재설정을 비롯하여 사용자 및 그룹의 모든 측면을 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory에서 appRoleAssignments를 만듭니다. |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory에서 appRoleAssignments를 삭제합니다. |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory에서 appRoleAssignments를 업데이트합니다. |
| microsoft.aad.directory/contacts/basic/update | Azure Active Directory에서 연락처의 기본 속성을 읽습니다. |
| microsoft.aad.directory/contacts/create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.aad.directory/contacts/delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory에서 groups.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.aad.directory/groups/hiddenMembers/read | Azure Active Directory에서 groups.hiddenMembers 속성을 읽습니다. |
| microsoft.aad.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.aad.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.aad.directory/users/create | Azure Active Directory에서 사용자를 만듭니다. |
| microsoft.aad.directory/users/delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.aad.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.aad.directory/users/restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory에서 users.userPrincipalName 속성을 업데이트합니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

## <a name="deprecated-roles"></a>사용되지 않는 역할

다음 역할은 사용할 수 없습니다. 해당 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다.

* 임시 라이선스 관리자
* 디바이스 연결
* 디바이스 관리
* 디바이스 사용자
* 전자 메일 확인 사용자 생성자
* 사서함 관리자
* 작업 공간 디바이스 연결

## <a name="next-steps"></a>다음 단계

* Azure 구독의 관리자로서 사용자를 할당하는 방법에 대해 자세히 알아보려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
* Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
