---
title: Azure Active Directory에서 관리자 역할 할당 | Microsoft Docs
description: 관리자 역할은 사용자를 추가하고, 관리자 역할을 할당하며, 사용자 암호를 다시 설정하고, 사용자 라이선스 또는 도메인을 관리하는 데 사용할 수 있습니다. 관리자 역할이 할당된 사용자는 조직에서 구독한 모든 클라우드 서비스에서 동일한 권한을 갖습니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/27/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 9b56f540af2b8d35258a4db79502c9edf83cdb45
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128469"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할 할당

Azure AD(Azure Active Directory)를 사용하여 다른 기능을 담당하도록 별도의 관리자를 지정할 수 있습니다. 관리자는 Azure AD 포털에서 사용자 추가 또는 변경, 관리 역할 할당, 사용자 암호 재설정, 사용자 라이선스 관리 및 도메인 이름 관리와 같은 작업을 수행하도록 지정할 수 있습니다.

## <a name="details-about-the-global-administrator-role"></a>전역 관리자 역할에 대한 세부 정보

전역 관리자는 모든 관리 기능에 액세스할 수 있습니다. 기본적으로 Azure 구독에 등록하는 사람에게는 디렉터리에 대한 전역 관리자 역할이 할당됩니다. 전역 관리자만 다른 관리자 역할을 할당할 수 있습니다.

## <a name="assign-or-remove-administrator-roles"></a>관리자 역할 할당 또는 제거

Azure Active Directory에서 사용자에게 관리 역할을 할당하는 방법에 대한 내용은 [Azure Active Directory에서 관리자 역할에 사용자 할당](../fundamentals/active-directory-users-assign-role-azure-portal.md)을 참조하세요.

## <a name="available-roles"></a>사용 가능한 역할

다음과 같은 관리자 역할을 사용할 수 있습니다.

* **[응용 프로그램 관리자](#application-administrator)**: 이 역할의 사용자는 엔터프라이즈 응용 프로그램, 응용 프로그램 등록 및 응용 프로그램 프록시 설정의 모든 측면을 만들고 관리할 수 있습니다. 또한 이 역할은 위임된 권한 및 Microsoft Graph와 Azure AD Graph를 제외한 응용 프로그램 사용 권한에 동의하는 기능을 부여합니다. 이 역할의 구성원은 새 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램을 만들 때 소유자로 추가되지 않습니다.

* **[응용 프로그램 개발자](#application-developer)**: 이 역할의 사용자는 “사용자가 응용 프로그램을 등록할 수 있음” 설정이 아니오로 설정된 경우 응용 프로그램 등록을 만들 수 있습니다. 또한 이 역할은 “사용자가 앱이 사용자 대신 회사 데이터에 액세스하는 것에 동의할 수 있음” 설정이 아니오로 설정된 경우 구성원이 직접 동의하도록 허용합니다. 이 역할의 구성원은 새 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램을 만들 때 소유자로 추가됩니다.

* **[대금 청구 관리자](#billing-administrator)**: 구입하고, 구독을 관리하고, 지원 티켓을 관리하고, 서비스 상태를 모니터링합니다.

* **[클라우드 응용 프로그램 관리자](#cloud-application-administrator)**: 이 역할의 사용자는 응용 프로그램 관리자 역할과 동일한 권한을 가집니다. 다만 응용 프로그램 프록시를 관리하는 권한은 없습니다. 이 역할은 엔터프라이즈 응용 프로그램 및 응용 프로그램 등록의 모든 측면을 만들고 관리하는 기능을 부여합니다. 또한 이 역할은 위임된 권한 및 Microsoft Graph와 Azure AD Graph를 제외한 응용 프로그램 사용 권한에 동의하는 기능을 부여합니다. 이 역할의 구성원은 새 응용 프로그램 등록 또는 엔터프라이즈 응용 프로그램을 만들 때 소유자로 추가되지 않습니다.

* **[준수 관리자](#compliance-administrator)**: 이 역할의 사용자는 Office 365 보안 및 준수 센터와 Exchange 관리 센터 내 관리 권한을 갖습니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **[조건부 액세스 관리자](#conditional-access-administrator)**: 이 역할의 사용자는 Azure Active Directory 조건부 액세스 설정을 관리할 수 있습니다.
  > [!NOTE]
  > Azure에서 Exchange ActiveSync 조건부 액세스 정책을 배포하려면 사용자도 글로벌 관리자여야 합니다.
  
* **[장치 관리자](#device-administrators)**: 이 역할은 [장치 설정](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)에서 추가 로컬 관리자로만 할당할 수 있습니다. 이 역할을 가진 사용자가 Azure Active Directory에 가입된 모든 Windows 10 장치에서 로컬 컴퓨터 관리자가 됩니다. Azure Active Directory의 장치 개체를 관리하는 기능이 없습니다. 

* **[디렉터리 읽기 권한자](#directory-readers)**: [동의 프레임워크](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)를 지원하지 않는 응용 프로그램에 할당될 레거시 역할입니다. 이 역할은 어느 사용자에게나 할당되면 안 됩니다.

* **[디렉터리 동기화 계정](#directory-synchronization-accounts)**: 사용하지 않도록 합니다. 이 역할은 Azure AD Connect 서비스에 자동으로 할당되고 다른 사용에 적합하거나 지원되지 않습니다.

* **[디렉터리 작성자](#directory-writers)**: [동의 프레임워크](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)를 지원하지 않는 응용 프로그램에 할당될 레거시 역할입니다. 이 역할은 어느 사용자에게나 할당되면 안 됩니다.

* **[Dynamics 365 서비스 관리자/CRM 서비스 관리자](#dynamics-365-service-administrator)**: 서비스가 있는 경우 이 역할의 사용자는 Microsoft Dynamics 365 Online 내에서 글로벌 권한을 가지며, 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [서비스 관리 역할을 사용하여 테넌트 관리](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)를 참조하세요.

* **[Exchange 서비스 관리자](#exchange-service-administrator)**: 이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft Exchange Online 내에서 전역 사용 권한을 가집니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **[전역 관리자 / 회사 관리자](#company-administrator)**: 이 역할의 사용자는 Azure Active Directory의 모든 관리 기능 및 Exchange Online, SharePoint Online 및 비즈니스용 Skype Online과 같은 Azure Active Directory에 페더레이션하는 서비스에 대한 액세스를 가집니다. Azure Active Directory 테넌트에 등록하는 사람이 전역 관리자가 됩니다. 전역 관리자만 다른 관리자 역할을 할당할 수 있습니다. 회사에 여러 전역 관리자가 있을 수 있습니다. 전역 관리자는 모든 사용자 및 모든 다른 관리자의 암호를 다시 설정할 수 있습니다.

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "회사 관리자"로 식별됩니다. [Azure portal](https://portal.azure.com)에서는 "전역 관리자"입니다.
  >
  >

* **[게스트 초대자](#guest-inviter)**: 이 역할의 사용자는 **구성원이 초대할 수 있음** 사용자 설정이 '아니요'로 설정된 경우 Azure Active Directory B2B 게스트 사용자 초대를 관리할 수 있습니다. B2B 공동 작업에 대한 자세한 내용은 [Azure AD B2B 공동 작업 정보](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 참조하세요. 다른 권한은 포함되지 않습니다.

* **[Information Protection 관리자](#information-protection-administrator)**: 이 역할의 사용자는 Azure Information Protection 서비스에 대한 모든 권한을 가집니다. 이 역할은 Azure Information Protection 정책에 대한 레이블을 구성하고, 보호 템플릿을 관리하고, 보호를 활성화하는 권한을 갖습니다. 이 역할은 ID 보호 센터, Privileged Identity Management, Office 365 Service Health 또는 Office 365 보안 및 준수 센터에서 어느 권한도 부여하지 않습니다.

* **[Intune 서비스 관리자](#intune-service-administrator)**: 이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft Intune Online 내에서 전역 사용 권한을 가집니다. 또한 이 역할은 정책을 연결하고 그룹을 만들고 관리하기 위해 사용자와 장치를 관리하는 기능을 포함합니다. 자세한 내용은 [Microsoft Intune에서 RBAC(역할 기반 관리 제어)](https://docs.microsoft.com/intune/role-based-access-control)를 참조하세요

* **[라이선스 관리자](#license-administrator)**: 이 역할의 사용자는 사용자 및 그룹의(그룹 기반 라이선스를 사용하여) 라이선스 할당을 추가, 제거 및 업데이트하고, 사용자의 사용 위치를 관리합니다. 이 역할은 사용 위치를 벗어나서 구독을 구매 또는 관리하고, 그룹을 만들거나 관리하고, 사용자를 만들거나 관리하는 기능을 부여하지 않습니다.

* **[메시지 센터 읽기 권한자](#message-center-reader)**: 이 역할의 사용자는 Exchange, Intune, Microsoft Teams 등 구성된 서비스의 조직에 대한 [Office 365 메시지 센터](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)에서 알림 및 자문 상태 업데이트를 모니터링할 수 있습니다. 메시지 센터 읽기 권한자는 게시물 및 업데이트 이메일 다이제스트를 매주 수신하며, 메시지 센터 게시물을 Office 365에서 공유할 수 있습니다. Azure AD에서 이 역할에 할당된 사용자는 Azure AD 서비스에서 사용자 및 그룹처럼 읽기 전용 권한만 있습니다. 

* **[파트너 계층 1 지원](#partner-tier1-support)**: 사용하지 마세요. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

* **[파트너 계층 2 지원](#partner-tier2-support)**: 사용하지 마세요. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

* **[암호 관리자/기술 지원팀 관리자](#helpdesk-administrator)**: 이 역할의 사용자는 암호를 변경하고, 서비스 요청을 관리하고, 서비스 상태를 모니터링할 수 있습니다. 기술 지원팀 관리자는 사용자 및 다른 기술 지원팀 관리자에 대해서만 암호를 다시 설정할 수 있습니다. 

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "기술 지원팀 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com/)에서 "암호 관리자"입니다.
  >
  >
  
* **[Power BI 서비스 관리자](#power-bi-service-administrator)**: 이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft Power BI 내에서 전역 사용 권한을 가지며 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [Power BI 관리자 역할 이해](https://docs.microsoft.com/power-bi/service-admin-role)를 참조하세요.

* **[권한 있는 역할 관리자](#privileged-role-administrator)**: 이 역할의 사용자는 Azure Active Directory 및 Azure AD Privileged Identity Management 내에서 역할 할당을 관리할 수 있습니다. 또한 이 역할을 통해 Privileged Identity Management의 모든 측면을 관리할 수 있습니다.

* **[보고서 읽기 권한자](#reports-reader)**: 이 역할의 사용자는 Office 365 관리 센터에서 사용량 보고 데이터 및 보고서 대시보드를, PowerBI에서 채택 컨텍스트 팩을 볼 수 있습니다. 또한 역할은 Azure AD의 로그온 보고서 및 활동과 함께 Microsoft Graph Reporting API에서 반환되는 데이터에 대한 액세스를 제공합니다. 보고서 구독자 역할에 할당된 사용자는 관련된 사용량 및 채택 메트릭에만 액세스할 수 있습니다. 설정을 구성하거나 Exchange와 같은 제품 특정 관리 센터에 액세스할 수 있는 관리자 권한은 없습니다. 

* **[보안 관리자](#security-administrator)**: 이 역할의 사용자는 보안 읽기 역할의 모든 읽기 전용 권한 및 보안 관련 서비스(Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management 및 Office 365 보안 및 준수 센터)에 대한 구성을 관리할 수 있습니다. Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에서 제공됩니다.
  
  | 그런 다음 | 가능한 작업 |
  | --- | --- |
  | ID 보호 센터 |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>또한 암호 재설정을 제외한 모든 IPC 작업을 수행할 능력. |
  | Privileged Identity Management |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>**없습니다** . |
  | <p>Office 365 서비스 상태 모니터링</p><p>Office 365 보안 및 규정 준수 센터 |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>고급 위협 방지 기능(맬웨어 및 바이러스 보호, 악성 URL 구성, URL 추적 등)에서 모든 설정을 구성할 수 있습니다. |
  
* **[보안 읽기 권한자](#security-reader)**: 이 역할의 사용자는 Azure Active Directory, Identity Protection, Privileged Identity Management의 모든 정보를 포함하여 전역 읽기 전용 액세스를 갖고, Azure Active Directory 로그인 보고서 및 감사 로그를 읽을 수 있습니다. 또한 역할은 Office 365 보안 및 규정 준수 센터의 읽기 전용 권한을 부여합니다. Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에서 제공됩니다.

  | 그런 다음 | 가능한 작업 |
  | --- | --- |
  | ID 보호 센터 |모든 보안 보고서 및 보안 기능에 대한 설정 정보를 읽습니다.<ul><li>스팸 방지<li>암호화<li>데이터 손실 방지<li>맬웨어 방지<li>Advanced Threat Protection<li>피싱 방지<li>메일 흐름 규칙 |
  | Privileged Identity Management |<p>Azure AD PIM에 표시되는 다음과 같은 모든 정보에 대한 읽기 전용 액세스 권한을 갖습니다. Azure AD 역할 할당에 대한 정책 및 보고서, 보안 검토, Azure AD 역할 할당 외의 시나리오에 대한 정책 데이터 및 보고서에 대한 향후 읽기 액세스 권한.<p>Azure AD PIM에 로그인을 하거나 어떠한 변경도 할 수 **없습니다**. PIM 포털이나 PowerShell을 통해, 이 역할을 가진 담당자는 사용자가 후보자일 경우 추가 역할(예: 전역 관리자 또는 권한 있는 역할 관리자)을 활성화할 수 있습니다. |
  | <p>Office 365 서비스 상태 모니터링</p><p>Office 365 보안 및 규정 준수 센터</p> |<ul><li>경고 읽기 및 관리<li>보안 정책 읽기<li>위협 인텔리전스, 클라우드 앱 검색 및 검색 및 조사의 격리 읽기<li>모든 보고서 읽기 |

* **[서비스 지원 관리자](#service-support-administrator)**: 이 역할의 사용자는 Azure 및 Office 365 서비스에 대한 Microsoft로의 지원 요청 및 Azure Portal 및 Office 365 관리자 포털에서 서비스 대시보드 및 메시지 센터에 대한 뷰를 열 수 있습니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **[SharePoint 서비스 관리자](#sharepoint-service-administrator)**: 이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft SharePoint Online 내에서 전역 사용 권한을 가지며 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **[비즈니스용 Skype/Lync Service 관리자](#lync-service-administrator)**: 이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft 비즈니스용 Skype 내에서 전역 사용 권한을 가지며 Azure Active Directory에서 Skype 관련 사용자 특성을 관리합니다. 또한 이 역할은 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능을 부여합니다. 자세한 내용은 [비즈니스용 Skype 관리자 역할 정보](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)를 참조하세요.

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "Lync Service 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com/)에서 "비즈니스용 Skype 서비스 관리자"입니다.
  >
  >

* **[사용자 계정 관리자](#user-account-administrator)**: 이 역할의 사용자는 사용자 및 그룹의 모든 측면을 만들고 관리할 수 있습니다. 또한 이 역할은 지원 티켓을 관리하고 서비스 상태를 모니터링하는 권한을 포함합니다. 몇 가지 제한 사항이 적용됩니다. 예를 들어 이 역할은 전역 관리자를 삭제할 수 없습니다. 사용자 계정 관리자는 사용자, 기술 지원팀 관리자 및 다른 사용자 계정 관리자에 대한 암호만 변경할 수 있습니다.

| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>사용자, 기술 지원팀 관리자 및 다른 사용자 계정 관리자에 대한 암호만 변경</p><p>사용자 보기 만들기 및 관리</p><p>제한 사항과 함께 사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리. 전역 관리자를 삭제하거나 다른 관리자를 만들 수 없습니다.</p> |<p>Office 제품의 대금 청구 및 구매 작업 수행</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>다단계 인증 사용/사용 안 함</p><p>감사 로그 보기</p> |

## <a name="deprecated-roles"></a>사용되지 않는 역할

다음 역할은 사용할 수 없습니다. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다.

* 임시 라이선스 관리자
* 장치 연결
* 장치 관리
* 장치 사용자
* 전자 메일 확인 사용자 생성자
* 사서함 관리자
* 작업 공간 장치 연결


### <a name="to-add-a-colleague-as-a-global-administrator"></a>동료를 전역 관리자로 추가하려면

1. 테넌트 디렉터리에 대한 전역 관리자 또는 권한 있는 역할 관리자 계정으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

   ![Azure AD 관리 센터 열기](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. **사용자**를 선택합니다.

3. 전역 관리자로 지정하려는 사용자를 찾아 해당 사용자에 대한 블레이드를 엽니다.

4. 사용자 블레이드에서 **디렉터리 역할**을 선택합니다.
 
5. 디렉터리 역할 블레이드에서 **전역 관리자** 역할을 선택하고 저장합니다.

## <a name="detailed-azure-active-directory-permissions"></a>Azure Active Directory 권한 세부 정보
다음 표에서는 각 역할에 부여되는 Azure Active Directory의 특정 권한에 대해 설명합니다. 전역 관리자와 같은 일부 역할에는 Azure Active Directory 외부의 Microsoft 서비스에 대한 추가 권한이 있을 수 있습니다.


### <a name="application-administrator"></a>응용 프로그램 관리자
앱 등록 및 엔터프라이즈 앱의 모든 측면을 만들고 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Azure Active Directory에서 응용 프로그램을 만듭니다. |
| microsoft.aad.directory/Application/Delete | Azure Active Directory에서 응용 프로그램을 삭제합니다. |
| microsoft.aad.directory/Application/Update | Azure Active Directory에서 응용 프로그램의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Azure Active Directory에서 Applications.DefaultPolicy 속성을 업데이트합니다. |
| microsoft.aad.directory/Application/Update/Owners | Azure Active Directory에서 Applications.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/AppRoleAssignment/Create | Azure Active Directory에서 AppRoleAssignments를 만듭니다. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Azure Active Directory에서 AppRoleAssignments를 삭제합니다. |
| microsoft.aad.directory/AppRoleAssignment/Update | Azure Active Directory에서 AppRoleAssignments의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Policy/Create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/Policy/Delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/Policy/Update | Azure Active Directory에서 정책의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Policy/Update/Owners | Azure Active Directory에서 Policies.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Azure Active Directory(Azure AD Graph & Microsoft Graph)를 제외한 모든 리소스에 대해 모든 사용자를 대신하여 동의할 수 있습니다. |
| microsoft.aad.directory/ServicePrincipal/Create | Azure Active Directory에서 ServicePrincipals를 만듭니다. |
| microsoft.aad.directory/ServicePrincipal/Delete | Azure Active Directory에서 ServicePrincipals를 삭제합니다. |
| microsoft.aad.directory/ServicePrincipal/Update | Azure Active Directory에서 ServicePrincipals의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Azure Active Directory에서 ServicePrincipals.AppRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Azure Active Directory에서 ServicePrincipals.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Azure Active Directory에서 ServicePrincipals.DefaultPolicy 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Azure Active Directory에서 ServicePrincipals.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.reports/AllEntities/Read | Azure AD 보고서를 읽습니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="application-developer"></a>응용 프로그램 개발자
**사용자가 응용 프로그램을 등록할 수 있음** 설정에 관계없이 응용 프로그램 등록을 만들 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Azure Active Directory에서 응용 프로그램을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Azure Active Directory에서 AppRoleAssignments를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Azure Active Directory에서 OAuth2PermissionGrants를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Azure Active Directory에서 ServicePrincipals를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |

### <a name="billing-administrator"></a>대금 청구 관리자
결제 정보 업데이트와 같은 일반 결제 관련 작업을 수행할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/Organization/Update | Azure Active Directory에서 조직의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 업데이트합니다. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.billing/AllEntities/AllActions | Office 365 청구의 모든 측면을 관리합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="cloud-application-administrator"></a>클라우드 응용 프로그램 관리자
앱 프록시를 제외한 앱 등록 및 엔터프라이즈 앱의 모든 측면을 만들고 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Azure Active Directory에서 응용 프로그램을 만듭니다. |
| microsoft.aad.directory/Application/Delete | Azure Active Directory에서 응용 프로그램을 삭제합니다. |
| microsoft.aad.directory/Application/Update | Azure Active Directory에서 응용 프로그램의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Azure Active Directory에서 Applications.DefaultPolicy 속성을 업데이트합니다. |
| microsoft.aad.directory/Application/Update/Owners | Azure Active Directory에서 Applications.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/AppRoleAssignment/Create | Azure Active Directory에서 AppRoleAssignments를 만듭니다. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Azure Active Directory에서 AppRoleAssignments를 삭제합니다. |
| microsoft.aad.directory/AppRoleAssignment/Update | Azure Active Directory에서 AppRoleAssignments의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Policy/Create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/Policy/Delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/Policy/Update | Azure Active Directory에서 정책의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Policy/Update/Owners | Azure Active Directory에서 Policies.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Azure Active Directory(Azure AD Graph & Microsoft Graph)를 제외한 모든 리소스에 대해 모든 사용자를 대신하여 동의할 수 있습니다. |
| microsoft.aad.directory/ServicePrincipal/Create | Azure Active Directory에서 ServicePrincipals를 만듭니다. |
| microsoft.aad.directory/ServicePrincipal/Delete | Azure Active Directory에서 ServicePrincipals를 삭제합니다. |
| microsoft.aad.directory/ServicePrincipal/Update | Azure Active Directory에서 ServicePrincipals의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Azure Active Directory에서 ServicePrincipals.AppRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Azure Active Directory에서 ServicePrincipals.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Azure Active Directory에서 ServicePrincipals.DefaultPolicy 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Azure Active Directory에서 ServicePrincipals.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.reports/AllEntities/Read | Azure AD 보고서를 읽습니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="company-administrator"></a>회사 관리자
Azure AD 및 Azure AD ID를 사용하는 Microsoft 서비스의 모든 측면을 관리할 수 있습니다. Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "회사 관리자"로 식별됩니다. [Azure portal](https://portal.azure.com)에서는 "전역 관리자"입니다.

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Azure Active Directory에서 AdministrativeUnits를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Azure Active Directory에서 응용 프로그램을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Azure Active Directory에서 AppRoleAssignments를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Azure Active Directory에서 CollaborationSpaces를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Azure Active Directory에서 연락처를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Azure Active Directory에서 장치를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Azure Active Directory에서 DirectoryRoles를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Azure Active Directory에서 DirectoryRoleTemplates를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Azure Active Directory에서 DirectorySettings를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Azure Active Directory에서 DirectorySettingTemplates를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Azure Active Directory에서 도메인을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Azure Active Directory에서 그룹을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Azure Active Directory에서 LoginTenantBrandings를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Azure Active Directory에서 OAuth2PermissionGrants를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Azure Active Directory에서 정책을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Azure Active Directory(Azure AD Graph & Microsoft Graph)를 포함한 모든 리소스에 대해 모든 사용자를 대신하여 동의할 수 있습니다. |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Azure Active Directory에서 ServicePrincipals를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Azure Active Directory에서 응용 프로그램을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/User/AllActions/AllProperties | Azure Active Directory에서 사용자를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.aad.aadconnect/AllEntities/AllActions | microsoft.aad.aadconnect에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.billing/AllEntities/AllActions | Office 365 청구의 모든 측면을 관리합니다. |
| microsoft.aad.compliance/AllEntities/AllActions | 준수 센터에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.directorysync/AllEntities/AllActions | Azure AD Connect에서 모든 작업을 수행합니다. |
| microsoft.aad.lockbox/AllEntities/AllActions | Lockbox(인증 키 저장소) 서비스의 모든 측면을 관리합니다. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | 권한 있는 역할 관리 서비스의 모든 측면을 관리합니다. |
| microsoft.aad.reports/AllEntities/AllActions | Azure AD 보고서를 읽고 구성합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.crm/AllEntities/AllActions | Dynamics 365의 모든 측면을 관리합니다. |
| microsoft.exchange/AllEntities/AllActions | Exchange Online의 모든 측면을 관리합니다. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Information Protection의 모든 측면을 관리합니다. |
| microsoft.intune/AllEntities/AllActions | Intune의 모든 측면을 관리합니다. |
| microsoft.powerbi/AllEntities/AllActions | Power BI의 모든 측면을 관리합니다. |
| microsoft.protectioncenter/AllEntities/AllActions | Office 365 보호 센터를 관리합니다. |
| microsoft.sharepoint/AllEntities/AllActions | SharePoint Online을 관리합니다. |
| microsoft.skypeforbusiness/AllEntities/AllActions | 비즈니스용 Skype Online을 관리합니다. |

### <a name="compliance-administrator"></a>규정 준수 관리자
Azure AD 및 Office 365에서 준수 구성 및 보고서를 읽고 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.compliance/AllEntities/AllActions | 준수 센터에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.exchange/Compliance/AllActions | Exchange Online에서 준수를 관리합니다. |
| microsoft.sharepoint/Compliance/AllActions | SharePoint Online에서 준수를 관리합니다. |
| microsoft.skypeforbusiness/Compliance/AllActions | 비즈니스용 Skype Online에서 준수를 관리합니다. |

### <a name="conditional-access-administrator"></a>조건부 액세스 관리자
조건부 액세스 기능을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Azure Active Directory에서 ConditionalAccessPolicys를 만듭니다. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Azure Active Directory에서 ConditionalAccessPolicys를 삭제합니다. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Azure Active Directory에서 ConditionalAccessPolicys의 표준 속성을 읽습니다. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Azure Active Directory에서 ConditionalAccessPolicys.Owners 속성을 읽습니다. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Azure Active Directory에서 ConditionalAccessPolicys.PolicyAppliedTo 속성을 읽습니다. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Azure Active Directory에서 ConditionalAccessPolicys의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Azure Active Directory에서 ConditionalAccessPolicys.Owners 속성을 업데이트합니다. |

### <a name="device-administrators"></a>장치 관리자

이 역할을 가진 사용자가 Azure Active Directory에 가입된 모든 Windows 10 장치에서 로컬 컴퓨터 관리자가 됩니다. 그러나 Azure Active Directory의 장치 개체를 관리할 수 있는 권한은 없습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

### <a name="directory-readers"></a>디렉터리 읽기 권한자
기본 디렉터리 정보를 읽을 수 있습니다. 응용 프로그램에 대한 액세스 권한을 부여하는 작업은 다음과 같습니다.

| **Actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Azure Active Directory에서 AdministrativeUnits의 표준 속성을 읽습니다. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Azure Active Directory에서 AdministrativeUnits.Members 속성을 읽습니다. |
| microsoft.aad.directory/Application/Read | Azure Active Directory에서 응용 프로그램의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Application/Read/Owners | Azure Active Directory에서 Applications.Owners 속성을 읽습니다. |
| microsoft.aad.directory/CollaborationSpace/Read | Azure Active Directory에서 CollaborationSpaces의 표준 속성을 읽습니다. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Azure Active Directory에서 CollaborationSpaces.Owners 속성을 읽습니다. |
| microsoft.aad.directory/Contact/Read | Azure Active Directory에서 Contacts의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Contact/Read/MemberOf | Azure Active Directory에서 Contacts.MemberOf 속성을 읽습니다. |
| microsoft.aad.directory/Device/Read | Azure Active Directory에서 장치의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Device/Read/MemberOf | Azure Active Directory에서 Devices.MemberOf 속성을 읽습니다. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Azure Active Directory에서 Devices.RegisteredOwners 속성을 읽습니다. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Azure Active Directory에서 Devices.RegisteredUsers 속성을 읽습니다. |
| microsoft.aad.directory/DirectoryRole/Read | Azure Active Directory에서 DirectoryRoles의 표준 속성을 읽습니다. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Azure Active Directory에서 DirectoryRoles.EligibleMembers 속성을 읽습니다. |
| microsoft.aad.directory/DirectoryRole/Read/Members | Azure Active Directory에서 DirectoryRoles.Members 속성을 읽습니다. |
| microsoft.aad.directory/DirectorySetting/Read | Azure Active Directory에서 DirectorySettings의 표준 속성을 읽습니다. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Azure Active Directory에서 DirectorySettingTemplates의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Domain/Read | Azure Active Directory에서 도메인의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Group/Read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Azure Active Directory에서 Groups.AppRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/Group/Read/MemberOf | Azure Active Directory에서 Groups.MemberOf 속성을 읽습니다. |
| microsoft.aad.directory/Group/Read/Members | Azure Active Directory에서 Groups.Members 속성을 읽습니다. |
| microsoft.aad.directory/Group/Read/Owners | Azure Active Directory에서 Groups.Owners 속성을 읽습니다. |
| microsoft.aad.directory/Group/Read/Settings | Azure Active Directory에서 Groups.Settings 속성을 읽습니다. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Azure Active Directory에서 OAuth2PermissionGrants의 표준 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read | Azure Active Directory에서 ServicePrincipals의 표준 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Azure Active Directory에서 ServicePrincipals.AppRoleAssignedTo 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Azure Active Directory에서 ServicePrincipals.AppRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Azure Active Directory에서 ServicePrincipals.DefaultPolicy 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Azure Active Directory에서 ServicePrincipals.MemberOf 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Azure Active Directory에서 ServicePrincipals.OAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Azure Active Directory에서 ServicePrincipals.Owners 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Azure Active Directory에서 ServicePrincipals.OwnedObjects 속성을 읽습니다. |
| microsoft.aad.directory/Organization/Read | Azure Active Directory에서 조직의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/User/Read | Azure Active Directory에서 사용자의 표준 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Azure Active Directory에서 Users.AppRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/DirectReports | Azure Active Directory에서 Users.DirectReports 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/InvitedBy | Azure Active Directory에서 Users.InvitedBy 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/InvitedUsers | Azure Active Directory에서 Users.InvitedUsers 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/Manager | Azure Active Directory에서 Users.Manager 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/MemberOf | Azure Active Directory에서 Users.MemberOf 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Azure Active Directory에서 Users.OAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/OwnedDevices | Azure Active Directory에서 Users.OwnedDevices 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/OwnedObjects | Azure Active Directory에서 Users.OwnedObjects 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Azure Active Directory에서 Users.RegisteredDevices 속성을 읽습니다. |

### <a name="directory-synchronization-accounts"></a>디렉터리 동기화 계정
Azure AD Connect에서만 사용됩니다.

| **Actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/Policy/Delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/Policy/Read | Azure Active Directory에서 정책의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Policy/Read/Owners | Azure Active Directory에서 Policies.Owners 속성을 읽습니다. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Azure Active Directory에서 Policies.PolicyAppliedTo 속성을 읽습니다. |
| microsoft.aad.directory/Policy/Update | Azure Active Directory에서 정책의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Policy/Update/Owners | Azure Active Directory에서 Policies.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Create | Azure Active Directory에서 ServicePrincipals를 만듭니다. |
| microsoft.aad.directory/ServicePrincipal/Read | Azure Active Directory에서 ServicePrincipals의 표준 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Azure Active Directory에서 ServicePrincipals.AppRoleAssignedTo 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Azure Active Directory에서 ServicePrincipals.AppRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Azure Active Directory에서 ServicePrincipals.DefaultPolicy 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Azure Active Directory에서 ServicePrincipals.MemberOf 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Azure Active Directory에서 ServicePrincipals.OAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Azure Active Directory에서 ServicePrincipals.Owners 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Azure Active Directory에서 ServicePrincipals.OwnedObjects 속성을 읽습니다. |
| microsoft.aad.directory/ServicePrincipal/Update | Azure Active Directory에서 ServicePrincipals의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Azure Active Directory에서 ServicePrincipals.AppRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Azure Active Directory에서 ServicePrincipals.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Azure Active Directory에서 ServicePrincipals.DefaultPolicy 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Azure Active Directory에서 ServicePrincipals.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Update/DirSync | Azure Active Directory에서 Organizations.DirSync 속성을 업데이트합니다. |
| microsoft.aad.directorysync/AllEntities/AllActions | Azure AD Connect에서 모든 작업을 수행합니다. |

### <a name="directory-writer"></a>디렉터리 작성자
기본 디렉터리 정보를 읽고 쓸 수 있습니다. 응용 프로그램에 대한 액세스 권한을 부여하는 작업은 다음과 같습니다.

| **Actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Azure Active Directory에서 DirectorySettings를 만듭니다. |
| microsoft.aad.directory/DirectorySetting/Delete | Azure Active Directory에서 DirectorySettings를 삭제합니다. |
| microsoft.aad.directory/DirectorySetting/Update | Azure Active Directory에서 DirectorySettings의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/Group/CreateAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/Group/Read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Group/Update | Azure Active Directory에서 그룹의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Azure Active Directory에서 Groups.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory에서 Groups.Members 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Owners | Azure Active Directory에서 Groups.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Settings | Azure Active Directory에서 Groups.Settings 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/User/Update | Azure Active Directory에서 사용자의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory에서 Users.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory에서 Users.Manager 속성을 업데이트합니다. |

### <a name="dynamics-365-service-administrator"></a>Dynamics 365 서비스 관리자
Dynamics 365 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.crm/AllEntities/AllActions | Dynamics 365의 모든 측면을 관리합니다. |

### <a name="exchange-service-administrator"></a>Exchange 서비스 관리자
Exchange 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.exchange/AllEntities/AllActions | Exchange Online의 모든 측면을 관리합니다. |

### <a name="guest-inviter"></a>게스트 초대자
**구성원이 게스트를 초대할 수 있음** 설정에 관계없이 게스트 사용자를 초대할 수 있습니다.

  > [!NOTE]
  > 이 역할은 게스트 역할에서 추가 권한을 상속합니다.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Azure Active Directory에서 게스트 사용자를 초대합니다. |
| microsoft.aad.directory/User/Read | Azure Active Directory에서 사용자의 표준 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Azure Active Directory에서 Users.AppRoleAssignments 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/DirectReports | Azure Active Directory에서 Users.DirectReports 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/InvitedBy | Azure Active Directory에서 Users.InvitedBy 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/InvitedUsers | Azure Active Directory에서 Users.InvitedUsers 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/Manager | Azure Active Directory에서 Users.Manager 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/MemberOf | Azure Active Directory에서 Users.MemberOf 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Azure Active Directory에서 Users.OAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/OwnedDevices | Azure Active Directory에서 Users.OwnedDevices 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/OwnedObjects | Azure Active Directory에서 Users.OwnedObjects 속성을 읽습니다. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Azure Active Directory에서 Users.RegisteredDevices 속성을 읽습니다. |

### <a name="helpdesk-administrator"></a>기술 지원팀 관리자
관리자가 아닌 사용자 및 기술 지원팀 관리자의 암호를 재설정할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Azure Active Directory에서 제한된 관리자 및 다른 기술 지원팀 관리자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |

### <a name="information-protection-administrator"></a>Information Protection 관리자
Azure Information Protection 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Information Protection의 모든 측면을 관리합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="intune-service-administrator"></a>Intune 서비스 관리자
Intune 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.aad.directory/Contact/Delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.aad.directory/Contact/Update | Azure Active Directory에서 연락처의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Device/Create | Azure Active Directory에서 장치를 만듭니다. |
| microsoft.aad.directory/Device/Delete | Azure Active Directory에서 장치를 삭제합니다. |
| microsoft.aad.directory/Device/Update | Azure Active Directory에서 장치의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Azure Active Directory에서 Devices.RegisteredOwners 속성을 업데이트합니다. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Azure Active Directory에서 Devices.RegisteredUsers 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/Group/CreateAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/Group/Delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.aad.directory/Group/Read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Azure Active Directory에서 Groups.HiddenMembers 속성을 읽습니다. |
| microsoft.aad.directory/Group/Restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.aad.directory/Group/Update | Azure Active Directory에서 그룹의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Azure Active Directory에서 Groups.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory에서 Groups.Members 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Owners | Azure Active Directory에서 Groups.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Settings | Azure Active Directory에서 Groups.Settings 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/User/Update | Azure Active Directory에서 사용자의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory에서 Users.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory에서 Users.Manager 속성을 업데이트합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.intune/AllEntities/AllActions | Intune의 모든 측면을 관리합니다. |


### <a name="license-administrator"></a>라이선스 관리자
사용자 및 그룹의 제품 라이선스를 관리할 수 있습니다.
 
  > [!NOTE]
  > 이 역할은 디렉터리 읽기 권한자 역할에서 추가 권한을 상속합니다.
  >
  >
 
| **Actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/users/usageLocation/update | Azure Active Directory에서 users.usageLocation 속성을 업데이트합니다. |
| microsoft.azure.accessService/allEntities/allTasks | Azure 액세스 서비스의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="lync-service-administrator"></a>Lync 서비스 관리자
비즈니스용 Skype 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.skypeforbusiness/AllEntities/AllActions | 비즈니스용 Skype Online을 관리합니다. |

### <a name="message-center-reader"></a>메시지 센터 읽기 권한자
Office 365 메시지 센터에서만 조직의 메시지 및 업데이트를 읽을 수 있습니다. 

  > [!NOTE]
  > 이 역할은 디렉터리 읽기 권한자 역할에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | 메시지 센터에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |

### <a name="partner-tier1-support"></a>파트너 계층1 지원
사용하지 마세요. 일반적인 용도로는 적합하지 않습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Contact/Create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.aad.directory/Contact/Delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.aad.directory/Contact/Update | Azure Active Directory에서 연락처의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/Group/CreateAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/Group/Read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory에서 Groups.Members 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Owners | Azure Active Directory에서 Groups.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/User/Delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/User/Restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.aad.directory/User/Update | Azure Active Directory에서 사용자의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory에서 Users.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory에서 Users.Manager 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Azure Active Directory에서 관리자가 아닌 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="partner-tier2-support"></a>파트너 계층2 지원
사용하지 마세요. 일반적인 용도로는 적합하지 않습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Contact/Create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.aad.directory/Contact/Delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.aad.directory/Contact/Update | Azure Active Directory에서 연락처의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Domain/AllActions | Azure Active Directory에서 도메인을 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.directory/Group/Create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/Group/Delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.aad.directory/Group/Read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Group/Restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory에서 Groups.Members 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/Organization/Update | Azure Active Directory에서 조직의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 업데이트합니다. |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/User/Delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/User/Restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.aad.directory/User/Update | Azure Active Directory에서 사용자의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory에서 Users.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory에서 Users.Manager 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/Password | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="power-bi-service-administrator"></a>Power BI 서비스 관리자
Power BI 제품의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.powerbi/AllEntities/AllActions | Power BI의 모든 측면을 관리합니다. |

### <a name="privileged-role-administrator"></a>권한 있는 역할 관리자
Azure AD에서 역할 할당을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Azure Active Directory에서 DirectoryRoles의 표준 속성을 업데이트합니다. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | 권한 있는 역할 관리 서비스의 모든 측면을 관리합니다. |

### <a name="reports-reader"></a>보고서 구독자
로그인 및 감사 보고서를 읽을 수 있습니다.

  > [!NOTE]
  > 이 역할은 디렉터리 읽기 권한자 역할에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | Azure AD 보고서를 읽습니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.usagereports/AllEntities/Read | Office 365 사용 보고서를 읽습니다. |

### <a name="security-administrator"></a>보안 관리자
보안 정보 및 보고서를 읽을 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Azure Active Directory에서 Applications.DefaultPolicy 속성을 업데이트합니다. |
| microsoft.aad.directory/Policy/Create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.aad.directory/Policy/Delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.aad.directory/Policy/Update | Azure Active Directory에서 정책의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Policy/Update/Owners | Azure Active Directory에서 Policies.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Azure Active Directory에서 ServicePrincipals.DefaultPolicy 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Privileged Identity Management의 모든 측면을 읽습니다. |
| microsoft.protectioncenter/AllEntities/Read | Office 365 보호 센터의 모든 측면을 읽습니다. |
| microsoft.protectioncenter/AllEntities/Update | Office 365 보호 센터를 관리합니다. |

### <a name="security-reader"></a>보안 읽기 권한자
Azure AD 및 Office 365에서 보안 정보 및 보고서를 읽을 수 있습니다.

  > [!NOTE]
  > 이 역할은 디렉터리 읽기 권한자 역할에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Privileged Identity Management의 모든 측면을 읽습니다. |
| microsoft.protectioncenter/AllEntities/Read | Office 365 보호 센터의 모든 측면을 읽습니다. |

### <a name="service-support-administrator"></a>서비스 지원 관리자
서비스 상태 정보를 읽고, 지원 티켓을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |

### <a name="sharepoint-service-administrator"></a>SharePoint 서비스 관리자
SharePoint 서비스의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

  > [!NOTE]
  > 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에서 설명한 역할을 참조하세요.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.sharepoint/AllEntities/AllActions | SharePoint Online을 관리합니다. |

### <a name="user-account-administrator"></a>사용자 계정 관리자
사용자 및 그룹의 모든 측면을 관리할 수 있습니다.

  > [!NOTE]
  > 이 역할은 [사용자 역할](https://docs.microsoft.com/azure/active-directory/users-default-permissions)에서 추가 권한을 상속합니다.
  >
  >

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Azure Active Directory에서 AppRoleAssignments를 만듭니다. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Azure Active Directory에서 AppRoleAssignments를 삭제합니다. |
| microsoft.aad.directory/AppRoleAssignment/Update | Azure Active Directory에서 AppRoleAssignments의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Contact/Create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.aad.directory/Contact/Delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.aad.directory/Contact/Update | Azure Active Directory에서 연락처의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.aad.directory/Group/CreateAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.aad.directory/Group/Delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.aad.directory/Group/Read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Azure Active Directory에서 Groups.HiddenMembers 속성을 읽습니다. |
| microsoft.aad.directory/Group/Restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.aad.directory/Group/Update | Azure Active Directory에서 그룹의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Azure Active Directory에서 Groups.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory에서 Groups.Members 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Owners | Azure Active Directory에서 Groups.Owners 속성을 업데이트합니다. |
| microsoft.aad.directory/Group/Update/Settings | Azure Active Directory에서 Groups.Settings 속성을 업데이트합니다. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory에서 Organizations.TrustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.aad.directory/User/Create | Azure Active Directory에서 사용자를 만듭니다. |
| microsoft.aad.directory/User/Delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.aad.directory/User/Restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.aad.directory/User/Update | Azure Active Directory에서 사용자의 표준 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory에서 Users.AppRoleAssignments 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory에서 Users.Manager 속성을 업데이트합니다. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Azure Active Directory에서 제한된 관리자, 기술 지원팀 관리자 및 다른 사용자 계정 관리자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health를 읽고 구성합니다. |

## <a name="next-steps"></a>다음 단계

* Azure 구독에 대한 관리자를 변경하는 방법에 대해 자세히 알아보려면 [Azure 관리자 역할을 추가 또는 변경하는 방법](../../billing/billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
