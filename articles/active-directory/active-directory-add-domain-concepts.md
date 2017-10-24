---
title: "Azure Active Directory에서 사용자 지정 도메인 이름의 개념적 개요 | Microsoft Docs"
description: "Single Sign-On을 위한 페더레이션을 포함하여 Azure Active Directory에서 사용자 지정 도메인 이름 사용에 대한 개념적 프레임워크를 설명합니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fd0c5def-0da2-43af-81bc-76f4cfe86afd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: b0ba411dd40bbd063a328d61be899c1e70a96eda
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Azure Active Directory에서 사용자 지정 도메인 이름의 개념적 개요
도메인 이름은 다음의 일부로 많은 디렉터리 리소스에 대한 중요한 식별자일 수 있습니다.

* 사용자 이름 또는 사용자에 대한 전자 메일 주소
* 그룹에 대한 주소
* 응용 프로그램에 대한 앱 ID URI

Azure AD(Azure Active Directory)의 리소스는 리소스를 포함하는 디렉터리가 소유하는 이미 확인된 도메인 이름을 포함할 수 있습니다. 전역 관리자만 Azure AD에서 도메인 관리 작업을 수행할 수 있습니다.

> [!TIP]
> [Azure AD Admin Center](https://aad.portal.azure.com)에서 도메인 이름을 관리하는 방법은 [Azure Active Directory에서 사용자 지정 도메인 이름 관리](active-directory-domains-manage-azure-portal.md)를 참조하세요.

Azure AD의 도메인 이름은 전역적으로 고유합니다. 사용자 지정 도메인 이름은 한 번에 하나의 Azure AD 테넌트에서 사용할 수 있습니다. 한 Azure AD 디렉터리에서 도메인 이름을 확인했다면 다른 Azure AD 디렉터리에서 동일한 도메인 이름을 확인하거나 사용할 수 없습니다.

## <a name="initial-and-custom-domain-names"></a>초기 및 사용자 지정 도메인 이름
Azure AD에서 모든 도메인 이름은 초기 도메인 이름이거나 사용자 지정 도메인 이름입니다.

모든 Azure AD는 contoso.onmicrosoft.com 형태의 초기 도메인 이름과 함께 제공됩니다. 이 예에서는 일반적으로 디렉터리를 생성한 관리자에 의해 디렉터리가 생성될 때 세 번째 수준의 도메인 이름 "contoso.onmicrosoft.com"이 설정되었습니다. 디렉터리에 대한 초기 도메인 이름은 변경 또는 삭제할 수 없습니다. 초기 도메인 이름은 사용자 지정 도메인 이름이 확인될 때까지, 완전히 작동하는 동안 주로 부트스트래핑 메커니즘으로 사용하도록 제공됩니다.

대부분의 프로덕션 환경에서 디렉터리에는 "contoso.com"처럼 하나 이상의 확인된 사용자 지정 도메인이 있으며 이 사용자 지정 도메인은 최종 사용자에게 표시됩니다. 사용자 지정 도메인 이름은 해당 조직이 소유하고 사용하는 도메인 이름입니다. 예를 들면, “contoso.com”은 해당사의 웹 사이트 호스팅 같은 용도로 사용됩니다. 이 도메인 이름은 전자 메일을 보내고 받기 위해 회사 네트워크에 로그인하는 데 사용하는 사용자 이름의 일부이므로 직원에게 친숙합니다.

Azure AD에서 사용할 수 있으려면 그 전에, 사용자 지정 도메인 이름을 사용자의 디렉터리에 추가하고 확인해야 합니다.

## <a name="verified-and-unverified-domain-names"></a>확인 및 확인되지 않은 도메인 이름
디렉터리에 대한 초기 도메인 이름은 Azure AD에서 확인된 것으로 암시적으로 평가됩니다. 관리자가 사용자 지정 도메인 이름을 Azure AD에 추가하면 초기에는 확인되지 않은 상태입니다. Azure AD는 어떠한 디렉터리 리소스도 확인되지 않은 도메인 이름을 사용하도록 허용하지 않습니다. 이렇게 하면 하나의 디렉터리만 특정 도메인 이름을 사용할 수 있고, 도메인 이름을 사용하는 조직이 실제로 해당 도메인 이름을 소유한다는 것을 확인할 수 있습니다.

Azure AD는 도메인에 대한 DNS(도메인 이름 서비스) 영역 파일에서 특정 항목을 조회하여 도메인 이름의 소유권을 확인합니다. 도메인 이름의 소유권을 확인하기 위해, 관리자는 Azure AD로부터 DNS 항목을(Azure AD가 찾게 될) 확보하고 해당 항목을 도메인 이름에 대한 DNS 영역 파일에 추가합니다. DNS 영역 파일은 해당 도메인의 도메인 이름 등록 기관에 의해 유지 관리됩니다. 도메인을 확인하는 단계는 [Azure AD 디렉터리에 사용자 지정 도메인 추가](active-directory-add-domain.md)문서에 나와 있습니다.

DNS 항목을 도메인 이름의 영역 파일에 추가하면 다른 도메인 서비스(예: 전자 메일, 웹 호스팅)에 영향을 주지 않습니다.

## <a name="federated-and-managed-domain-names"></a>페더레이션 및 관리되는 도메인 이름
사용자에게 온-프레미스 Active Directory 및 Azure AD 간의 페더레이션된 로그인 환경을 제공하기 위해 Azure AD에서 사용자 지정 도메인 이름을 구성할 수 있습니다. 페더레이션을 위한 도메인을 구성하려면 Azure AD에서 권한 있는 리소스 및 Windows Server Active Directory에 대한 업데이트도 필요합니다. Azure AD Connect에서 또는 PowerShell을 사용하여 페더레이션된 도메인 구성을 완료해야 합니다. Azure 클래식 포털에서 사용자 지정 도메인의 페더레이션을 시작할 수 없습니다. 경우에 따라 페더레이션되지 않은 도메인을 관리되는 도메인이라고 합니다. Azure AD 디렉터리에 대한 초기 도메인은 암시적으로 관리되는 도메인으로 평가됩니다.

## <a name="primary-domain-names"></a>주 도메인 이름
디렉터리의 주 도메인 이름은 관리자가 [Azure Portal](https://portal.azure.com/) 또는 Office 365 관리 포털 또는 Microsoft Intune 포털과 같은 다른 포털에서 새 사용자를 만들 때 사용자 이름의 '도메인' 부분에 기본값으로 미리 선택된 도메인 이름입니다. 디렉터리에는 하나의 주 도메인 이름만 있을 수 있습니다. 관리자는 페더레이션되지 않은 모든 확인된 사용자 지정 도메인 또는 초기 도메인이 되도록 주 도메인 이름을 변경할 수 있습니다.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Azure AD 및 기타 Microsoft Online Services에서 도메인 이름
Exchange Online, SharePoint Online 및 Intune과 같은 또 다른 Microsoft Online Services에서 사용할 수 있도록 먼저 Azure AD에서 도메인 이름을 확인해야 합니다. 이러한 다른 서비스에는 일반적으로 관리자가 서비스에 특정한 하나 이상의 DNS 항목을 추가해야 합니다.

Azure 웹앱은 도메인의 소유권을 확인하는 고유의 메커니즘을 사용합니다. Azure AD에 의존하는 구독의 Azure 웹앱에서 사용하기 위해 이전에 확인한 경우에도 Azure AD에서 사용하기 위해 도메인을 확인해야 합니다. Azure 웹앱은 웹앱을 보호하는 디렉터리와 다른 디렉터리에서 확인된 도메인 이름을 사용할 수 있습니다.

## <a name="managing-domain-names"></a>도메인 이름 관리
Azure 클래식 포털 및 PowerShell에서 도메인 관리 작업을 완료할 수 있습니다. Azure AD Graph API를 사용하여 다양한 작업을 완료할 수 있습니다.

* [사용자 지정 도메인 이름 추가 및 확인](active-directory-add-domain.md)
* [Azure 클래식 포털에서 도메인 관리](active-directory-add-manage-domain-names.md)
* [PowerShell을 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Azure AD Graph API를 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

