---
title: "Azure Active Directory에서 관리자 역할 할당 | Microsoft Docs"
description: "Azure Active Directory에서 사용할 수 있는 관리자 역할 및 이 역할을 할당하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: fdb4ed95b25519fb7ff319fe20a0dc5c54506506
ms.lasthandoff: 04/28/2017


---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할 할당
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-assign-admin-roles-azure-portal.md)
> * [Azure 클래식 포털](active-directory-assign-admin-roles.md)
>
>

Azure AD(Azure Active Directory)를 사용하여 다른 기능을 담당하도록 별도의 관리자를 지정할 수 있습니다. 이러한 관리자는 역할에 따라 Azure 포털 또는 Azure 클래식 포털의 다양한 기능에 액세스할 수 있게 되며 사용자 만들기나 편집, 다른 사람에게 관리 역할 할당, 사용자 암호 다시 설정, 사용자 라이선스 관리, 도메인 관리 등을 수행할 수 있습니다. 관리자 역할이 할당되는 사용자는 Office 365 포털 또는 Azure 클래식 포털이나 Windows PowerShell용 Azure AD 모듈을 사용하여 이 역할을 할당하는지 여부에 상관없이 조직이 구독하는 모든 클라우드 서비스에서 동일한 권한을 가집니다.

다음과 같은 관리자 역할을 사용할 수 있습니다.

* **대금 청구 관리자**: 구입하고, 구독을 관리하고, 지원 티켓을 관리하고, 서비스 상태를 모니터링합니다.
* **전역 관리자 / 회사 관리자**: 모든 관리 기능에 액세스할 수 있습니다. Azure 계정에 등록하는 사람이 전역 관리자가 됩니다. 전역 관리자만 다른 관리자 역할을 할당할 수 있습니다. 회사에 여러 전역 관리자가 있을 수 있습니다.

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "회사 관리자"로 식별됩니다. [Azure 포털](https://portal.azure.com)에서 "전역 관리자"입니다.
  >
  >

* **호환성 관리자**: 이 역할의 사용자는 Office 365 보안 및 준수 센터 및 Exchange 관리 센터 내 관리 권한이 있으며 Office 365 관리 센터에서 감사 로그를 읽을 수 있습니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

* **CRM 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft CRM Online 내에서 전역 사용 권한을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.
* **고객 LockBox 액세스 승인자**: LockBox 서비스가 활성화된 경우 이 역할의 사용자는 회사 정보에 액세스하도록 Microsoft 엔지니어에 대한 요청을 승인할 수 있습니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.
* **장치 관리자**: 이 역할의 사용자는 Azure Active Directory에 연결된 모든 Windows 10 장치의 관리자가 됩니다.
* **디렉터리 읽기 권한자**: [동의 프레임워크](active-directory-integrating-applications.md)를 지원하지 않는 응용 프로그램에 할당될 레거시 역할입니다. 모든 사용자에게 할당되면 안 됩니다.
* **디렉터리 동기화 계정**: 사용하지 마십시오. 이 역할은 Azure AD Connect 서비스에 자동으로 할당되고 다른 사용에 적합하거나 지원되지 않습니다.
* **디렉터리 작성자**: [동의 프레임워크](active-directory-integrating-applications.md)를 지원하지 않는 응용 프로그램에 할당될 레거시 역할입니다. 모든 사용자에게 할당되면 안 됩니다.
* **Exchange 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft Exchange Online 내에서 전역 사용 권한을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.
* **Intune 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft Intune Online 내에서 전역 사용 권한을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.
* **비즈니스용 Skype 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft 비즈니스용 Skype 내에서 전역 사용 권한을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요. 이 역할은 이전에 **Lync 서비스 관리자** 역할이라고 했습니다.
* **게스트 초대자**: 이 역할의 사용자는 게스트 초대를 관리할 수 있습니다. 다른 권한은 포함되지 않습니다.
* **사서함 관리자**: 이 역할은 RIM Blackberry 장치에 대한 Exchange Online 전자 메일 지원을 위해서만 사용됩니다. 조직이 RIM Blackberry 장치에서 Exchange Online 전자 메일을 사용하지 않는 경우 이 역할을 사용하지 마십시오.
* **파트너 계층 1 지원**: 사용하지 마십시오. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.
* **파트너 계층 2 지원**: 사용하지 마십시오. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.
* **암호 관리자/Helpdesk 관리자**: 암호를 재설정하고, 서비스 요청을 관리하고, 서비스 상태를 모니터링합니다. 암호 관리자는 사용자 및 다른 암호 관리자에 대해서만 암호를 다시 설정할 수 있습니다.

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell에서 이 역할은 "기술 지원팀 관리자"로 식별됩니다.
  >
  >

* **Power BI 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft Power BI 내에서 전역 사용 권한을 가집니다. 자세한 내용은 [Power BI를 보다 쉽게 관리하기](https://powerbi.microsoft.com/en-us/blog/making-it-easier-to-administer-power-bi/)를 참조합니다.
* **권한 있는 역할 관리자**: 이 역할의 사용자는 Azure AD [권한 있는 ID 관리자](active-directory-privileged-identity-management-configure.md)를 관리하고 다른 사용자에 대한 역할을 업데이트할 수 있습니다.
* **SharePoint 서비스 관리자**: 이 역할의 사용자는 서비스가 있는 경우 Microsoft SharePoint Online 내에서 전역 사용 권한을 가집니다. 자세한 내용은 [Office 365 관리 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.
* **서비스 관리자**: 서비스 요청을 관리하고 서비스 상태를 모니터링합니다.

  > [!NOTE]
  > 사용자에게 서비스 관리자 역할을 할당하려면 전역 관리자가 먼저 Exchange Online 등의 서비스에서 사용자에게 관리 권한을 할당한 다음 Azure 클래식 포털에서 해당 사용자에게 서비스 관리자 역할을 할당해야 합니다.
  >
  >
* **사용자 계정 관리자**: 암호를 다시 설정하고, 서비스 상태를 모니터링하고, 사용자 계정과 사용자 그룹 및 서비스 요청을 관리합니다. 사용자 관리 관리자의 권한에 몇 가지 제한이 적용됩니다. 예를 들어 전역 관리자를 삭제하거나 다른 관리자를 만들 수 없습니다. 또한 대금 청구 관리자, 전역 관리자, 서비스 관리자의 암호를 재설정할 수 없습니다.
* **보안 판독기**: ID 보안 센터, Privileged Identity Management, Office 365 서비스 상태 모니터링 및 Office 365 보안 및 규정 준수 센터의 여러 보안 기능에 읽기 전용 액세스합니다.
* **보안 관리자**: **보안 판독기** 역할의 모든 읽기 전용 권한에다 동일한 서비스에 대한 ID 보안 센터, Privileged Identity Management, Office 365 서비스 상태 모니터링 및 Office 365 보안 및 규정 준수 센터와 같은 여러 관리자 권한을 추가했습니다.

## <a name="administrator-permissions"></a>관리자 권한
### <a name="billing-administrator"></a>대금 청구 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>Office 제품의 대금 청구 및 구매 작업 수행</p> |<p>사용자 암호 다시 설정</p><p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>보고서 보기</p> |

### <a name="global-administrator"></a>전역 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>Office 제품의 대금 청구 및 구매 작업 수행</p> <p>사용자 암호 다시 설정</p><p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>다단계 인증 사용/사용 안 함</p><p>보고서 보기</p> |해당 없음 |

### <a name="password-administrator"></a>암호 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>사용자 암호 다시 설정</p> |<p>Office 제품의 대금 청구 및 구매 작업 수행</p><p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>보고서 보기</p> |

### <a name="service-administrator"></a>서비스 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p> |<p>사용자 암호 다시 설정</p><p>Office 제품의 대금 청구 및 구매 작업 수행</p><p>사용자 보기 만들기 및 관리</p><p>사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>보고서 보기</p> |

### <a name="user-administrator"></a>사용자 관리자
| 가능한 작업 | 불가능한 작업 |
| --- | --- |
| <p>회사 및 사용자 정보 보기</p><p>Office 지원 티켓 관리</p><p>제한 사항과 함께 사용자 암호 다시 설정. 대금 청구 관리자, 전역 관리자, 서비스 관리자에 대한 암호를 다시 설정할 수 없습니다.</p><p>사용자 보기 만들기 및 관리</p><p>제한 사항과 함께 사용자 및 그룹 만들기/편집/삭제, 사용자 라이선스 관리. 전역 관리자를 삭제하거나 다른 관리자를 만들 수 없습니다.</p> |<p>Office 제품의 대금 청구 및 구매 작업 수행</p><p>도메인 관리</p><p>회사 정보 관리</p><p>다른 사용자에게 관리 역할 위임</p><p>디렉터리 동기화 사용</p><p>다단계 인증 사용/사용 안 함</p><p>보고서 보기</p> |

### <a name="security-reader"></a>보안 판독기
| 내용 | 가능한 작업 |
| --- | --- |
| ID 보호 센터 |모든 보안 보고서 및 보안 기능에 대한 설정 정보를 읽습니다.<ul><li>스팸 방지<li>암호화<li>데이터 손실 방지<li>맬웨어 방지<li>Advanced Threat Protection<li>피싱 방지<li>메일 흐름 규칙 |
| Privileged Identity Management |<p>Azure AD PIM에 표시되는 다음과 같은 모든 정보에 대한 읽기 전용 액세스 권한을 갖습니다. Azure AD 역할 할당에 대한 정책 및 보고서, 보안 검토, Azure AD 역할 할당 외의 시나리오에 대한 정책 데이터 및 보고서에 대한 향후 읽기 액세스 권한.<p>Azure AD PIM에 로그인을 하거나 어떠한 변경도 할 수 **없습니다**. PIM 포털이나 PowerShell을 통해, 이 역할을 가진 담당자는 사용자가 후보자일 경우 추가 역할(예: 전역 관리자 또는 권한 있는 역할 관리자)을 활성화할 수 있습니다. |
| <p>Office 365 서비스 상태 모니터링</p><p>Office 365 보안 및 규정 준수 센터</p> |<ul><li>경고 읽기 및 관리<li>보안 정책 읽기<li>위협 인텔리전스, 클라우드 앱 검색 및 검색 및 조사의 격리 읽기<li>모든 보고서 읽기 |

### <a name="security-administrator"></a>보안 관리자
| 내용 | 가능한 작업 |
| --- | --- |
| ID 보호 센터 |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>또한 암호 재설정을 제외한 모든 IPC 작업을 수행할 능력. |
| Privileged Identity Management |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>**없습니다** . |
| <p>Office 365 서비스 상태 모니터링</p><p>Office 365 보안 및 규정 준수 센터 |<ul><li>보안 읽기 권한자 역할의 모든 권한.<li>고급 위협 방지 기능(맬웨어 및 바이러스 보호, 악성 URL 구성, URL 추적 등)에서 모든 설정을 구성할 수 있습니다. |

## <a name="details-about-the-global-administrator-role"></a>전역 관리자 역할에 대한 세부 정보
전역 관리자는 모든 관리 기능에 액세스할 수 있습니다. 기본적으로 Azure 구독에 등록하는 사람에게는 디렉터리에 대한 전역 관리자 역할이 할당됩니다. 전역 관리자만 다른 관리자 역할을 할당할 수 있습니다.

## <a name="assign-or-remove-administrator-roles"></a>관리자 역할 할당 또는 제거
1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory**를 클릭한 다음 조직의 디렉터리 이름을 클릭합니다.
2. **사용자** 페이지에서 편집하려는 사용자의 표시 이름을 클릭합니다.
3. 기존 관리자 역할을 제거하려면 **조직 내 역할** 목록에서 이 사용자에게 할당할 관리자 역할을 선택하거나 **사용자**를 선택합니다.
4. **암호 확인용 메일** 상자에 메일 주소를 입력합니다. 이 메일 주소는 암호 자체 재설정을 포함한 중요한 알림에 사용되므로, 사용자가 Azure에 액세스할 수 있는지 여부에 상관없이 이 메일 계정에 액세스할 수 있어야 합니다.
5. **허용** 또는 **차단**을 선택하여 사용자가 로그인하여 서비스에 액세스할 수 있게 할지 여부를 지정합니다.
6. **사용 위치** 드롭다운 목록에서 위치를 지정합니다.
7. 완료 되면 **저장**을 클릭합니다.

## <a name="deprecated-roles"></a>사용되지 않는 역할

다음 역할은 사용할 수 없습니다. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다.

* 임시 라이선스 관리자
* 전자 메일 확인 사용자 생성자
* 장치 연결
* 장치 관리
* 장치 사용자
* 작업 공간 장치 연결

## <a name="next-steps"></a>다음 단계
* Azure 구독에 대한 관리자를 변경하는 방법에 대해 자세히 알아보려면 [Azure 관리자 역할을 추가 또는 변경하는 방법](../billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure에서 리소스 액세스를 제어하는 방법에 대해 자세히 알아보려면 [Azure의 리소스 액세스 이해](active-directory-understanding-resource-access.md)
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](active-directory-how-subscriptions-associated-directory.md)
* [사용자 관리](active-directory-create-users.md)
* [암호 관리](active-directory-manage-passwords.md)
* [그룹 관리](active-directory-manage-groups.md)

