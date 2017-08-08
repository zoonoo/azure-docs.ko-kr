---
title: "Azure Active Directory에 사용자 지정 도메인 이름 추가 및 페더레이션 로그인 설정 | Microsoft Docs"
description: "Azure Active Directory에 회사의 도메인 이름을 추가하여 Azure Active Directory와 온-프레미스 페더레이션 솔루션 간에 페더레이션 로그인을 설정하는 방법입니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 27126c7e-e6d6-4ef3-a4fb-f5f0706e749d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 7f257dff6fdd423b89d1c52a84b64951dcf5915f
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017

---
# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Azure Active Directory에 사용자 지정 도메인 이름 추가
contoso.com의 사용자가 페더레이션 Single Sign-On 환경을 회사 네트워크에서 유지할 수 있도록 'contoso.com'과 같은 사용자 지정 도메인 이름을 구성할 수 있습니다. AD FS(Active Directory Federation Services) 또는 회사 네트워크에서 실행 중인 다른 페더레이션 서버가 이미 있는 경우 Azure AD Connect 도구를 사용하여 사용자 지정 도메인 이름을 사용하도록 Azure AD를 구성할 수 있습니다. 또한 Azure AD Connect를 사용하여 새 AD FS 환경을 배포하고 이를 Azure AD에 대한 페더레이션 Single Sign-On에 구성할 수 있습니다.

AD FS 또는 다른 페더레이션 서버가 없거나 이를 배포하지 않으려는 경우 [Azure Active Directory에 사용자 지정 도메인 이름 추가](active-directory-add-domain.md)지침을 따릅니다.

## <a name="add-a-custom-domain-name-to-your-directory"></a>디렉터리에 사용자 지정 도메인 이름 추가
1. Azure AD 디렉터리의 전역 관리자인 사용자 계정으로 [Azure 클래식 포털](https://manage.windowsazure.com/) 에 로그인합니다.
2. **Active Directory**에서 디렉터리를 열고 **도메인** 탭을 선택합니다.
3. 명령 모음에서 **추가**를 선택한 다음 'contoso.com'과 같은 사용자 지정 도메인의 이름을 입력합니다. .com, .net 또는 그 외 최상위 확장명을 포함해야 합니다.
4. **내 로컬 Active Directory를 사용하여 이 도메인을 Single Sign-On용으로 구성할 계획입니다** 확인란을 선택합니다.
5. **추가**를 선택합니다.

Azure AD Connect 도구를 실행하여 Azure AD에서 도메인을 확인하는 데 사용할 DNS 항목을 가져옵니다. 마법사의 **Azure AD 도메인** 단계에서 DNS 항목을 확인합니다. 마법사의 어떤 단계가 [이 지침에서](connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)와 유사한지를 확인할 수 있습니다. Azure AD Connect 도구가 없는 경우 [여기서 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771)할 수 있습니다.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>도메인에 대한 도메인 이름 등록 기관에 DNS 항목을 추가합니다.
사용자 지정 도메인 이름을 Azure AD로 사용하는 다음 단계에서는 도메인에 대한 DNS 영역 파일을 업데이트합니다. 이를 통해 Azure AD에서는 조직이 사용자 지정 도메인 이름을 소유하는지를 확인합니다.

1. 도메인 이름에 대한 도메인 이름 등록 기관의 웹 사이트에 로그인합니다. 이 작업을 수행하기 위해 액세스할 수 없는 경우 엑세스 권한이 있는 조직의 사용자 또는 팀에게 2단계를 완료하고 완료될 때를 알려주도록 요청합니다.
2. Azure AD에서 제공되는 DNS 항목을 추가하여 도메인에 대한 DNS 영역 파일을 업데이트합니다. 이러한 DNS 항목을 통해 Azure AD에서 도메인에 대한 소유권을 확인할 수 있도록 합니다. DNS 항목은 메일 라우팅이나 웹 호스팅 같은 어떠한 동작도 변경하지 않습니다.

이 단계에 대한 도움말은 [널리 사용되는 DNS 등록 기관에서 DNS 항목을 추가하기 위한 지침](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Azure AD에서 도메인 이름 확인
DNS 항목을 추가하고 나면, Azure AD에서 도메인 이름을 확인할 준비가 되었습니다.

도메인을 확인하려면 Azure AD Connect 마법사의 **Azure AD 도메인** 단계에서 **다음**을 선택합니다. Azure AD에서는 도메인에 대한 DNS 영역 파일에서 DNS 항목을 찾습니다. DNS 레코드가 전파되면 Azure AD에서는 도메인 이름을 확인합니다. 전파는 보통 몇 초 밖에 걸리지 않지만 한 시간 이상이 걸릴 경우도 있습니다. 확인이 처음에 작동하지 않으면 나중에 다시 시도하세요.

그런 다음 Azure AD Connect 마법사의 나머지 단계를 진행합니다. 그러면 Windows Server AD에서 Azure AD에 사용자를 동기화합니다. 페더레이션에 대해 구성되는 도메인에서 동기화된 사용자는 회사 네트워크에서 Azure AD에 페더레이션 Single Sign-On 환경을 가져올 수 됩니다.

## <a name="troubleshooting"></a>문제 해결
사용자 지정 도메인 이름을 확인할 수 없는 경우 다음을 시도하세요. 가장 일반적인 것부터 시작하여, 가장 드문 것까지 알아보겠습니다.

1. **한 시간을 대기합니다**. Azure AD가 도메인을 확인할 수 있기 전에 DNS 레코드를 전파해야 합니다. 이 작업은 한 시간 이상 걸릴 수 있습니다.
2. **DNS 레코드를 입력하고 정확한지 확인합니다**. 도메인에 대한 도메인 이름 등록 기관의 웹 사이트에서 이 단계를 완료합니다. DNS 항목을 DNS 영역 파일에 나타나지 않는 경우 또는 Azure AD에서 제공한 DNS 항목과 정확하게 일치하지 않는 경우 Azure AD는 도메인 이름을 확인할 수 없습니다. 도메인 이름 등록 기관에서 도메인에 대한 DNS 레코드를 업데이트하기 위한 액세스 권한이 없는 경우 조직에서 이 액세스 권한이 있는 사람이나 팀과 DNS 항목을 공유하고 DNS 항목을 추가하도록 요청합니다.
3. **Azure AD에서 도메인 이름을 다른 디렉터리에서 삭제합니다**. 단일 디렉터리에서 도메인 이름을 확인할 수 있습니다. 다른 디렉터리에서 이전에 도메인 이름을 확인한 경우 새 디렉터리에서 확인할 수 있게 되기 전에 삭제해야 합니다. 도메인 이름을 삭제하는 방법에 대해 자세히 알아보려면 [사용자 지정 도메인 이름 관리](active-directory-add-manage-domain-names.md)를 참고합니다.

## <a name="add-more-custom-domain-names"></a>사용자 지정 도메인 이름 더 추가하기
조직에서 사용자 지정 도메인 이름을 여러 개 사용하는 경우(예: ‘contoso.com’ 및 ‘contosobank.com’), 최대 900개의 도메인에 해당 이름을 추가할 수 있습니다. 이 문서의 동일한 단계를 사용하여 각 도메인 이름을 추가합니다.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 도메인 이름 관리](active-directory-add-manage-domain-names.md)
* [Azure AD에서 도메인 관리 개념 알아보기](active-directory-add-domain-concepts.md)
* [사용자 로그인 시 회사의 브랜딩 표시](active-directory-add-company-branding.md)
* [PowerShell을 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)


