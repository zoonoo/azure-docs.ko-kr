---
title: B2B - Azure AD에 대한 AD FS와 직접 페더레이션 설정
description: 게스트가 Azure AD 앱에 로그인할 수 있도록 직접 페더레이션을 위한 ID 공급자로 AD FS를 설정하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272838"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>예: Active Directory 페더레이션 서비스(AD FS)와의 직접 페더레이션(미리 보기)
|     |
| --- |
| 직접 페더레이션은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.|
|     |

이 문서에서는 SamL 2.0 또는 WS-Fed ID 공급자로 Active Directory 페더레이션 서비스(AD FS)를 사용하여 [직접 페더레이션을](direct-federation.md) 설정하는 방법을 설명합니다. 직접 페더레이션을 지원하려면 특정 특성 및 클레임을 ID 공급자에서 구성해야 합니다. 직접 페더레이션에 대한 ID 공급자를 구성하는 방법을 설명하기 위해 AD FS(Active Directory Federation 서비스)를 예로 들어 보겠습니다. AD FS를 SAML ID 공급자와 WS-Fed ID 공급자로 설정하는 방법을 살펴보겠습니다.

> [!NOTE]
> 이 문서에서는 SAML 및 WS-Fed모두에 대해 AD FS를 그림으로 설정하는 방법에 대해 설명합니다. ID 공급자가 AD FS인 직접 페더레이션 통합의 경우 WS-Fed를 프로토콜로 사용하는 것이 좋습니다. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>SAML 2.0 직접 페더레이션을 위한 AD FS 구성
Azure AD B2B는 아래에 나열된 특정 요구 사항과 함께 SAML 프로토콜을 사용하는 ID 공급자와 페더레이트하도록 구성할 수 있습니다. SAML 구성 단계를 설명하기 위해 이 섹션에서는 SAML 2.0에 대한 AD FS를 설정하는 방법을 보여 줍니다. 

직접 페더레이션을 설정하려면 ID 공급자의 SAML 2.0 응답에서 다음 특성을 수신해야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결하거나 수동으로 입력하여 구성할 수 있습니다. [테스트 AD FS 인스턴스 만들기의](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) 12단계는 AD FS 끝점을 찾는 방법 또는 메타데이터 URL을 생성하는 방법을 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`설명합니다. 

|특성  |값  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|사용자     |`urn:federation:MicrosoftOnline`         |
|발급자     |예를 들어 파트너 IdP의 발급자 URI`http://www.example.com/exk10l6w90DHM0yi...`         |

ID 공급자가 발급한 SAML 2.0 토큰에서 다음 클레임을 구성해야 합니다.


|특성  |값  |
|---------|---------|
|이름 ID 형식     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


다음 섹션에서는 SAML 2.0 ID 공급자의 예로 AD FS를 사용하여 필요한 특성 및 클레임을 구성하는 방법을 보여 줍니다.

### <a name="before-you-begin"></a>시작하기 전에

이 절차를 시작하기 전에 AD FS 서버를 이미 설정하고 작동해야 합니다. AD FS 서버 설정에 대한 도움말은 [Azure 가상 컴퓨터에서 테스트 AD FS 3.0 인스턴스 만들기를](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)참조하십시오.

### <a name="add-the-claim-description"></a>클레임 설명 추가

1. AD FS 서버에서 **도구** > **AD FS 관리를**선택합니다.
2. 탐색 창에서 **서비스** > **클레임 설명을 선택합니다.**
3. **작업**에서 **클레임 설명 추가를**선택합니다.
4. 클레임 **설명 추가** 창에서 다음 값을 지정합니다.

   - **이름 표시**: 영구 식별자
   - **클레임 식별자**:`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - **페더레이션 메타데이터에서 이 클레임 설명 게시에**대한 확인란을 이 페더레이션 서비스가 허용할 수 있는 클레임 유형으로 선택합니다.
   - **페더레이션 메타데이터에서 이 클레임 설명 게시에**대한 확인란을 이 페더레이션 서비스에서 보낼 수 있는 클레임 유형으로 선택합니다.

5. **확인**을 클릭합니다.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>신뢰 당사자 신뢰 및 클레임 규칙 추가

1. AD FS 서버에서 **도구** > **AD FS 관리로**이동합니다.
2. 탐색 창에서 신뢰 **관계** > **신뢰 신뢰 를 선택합니다.**
3. **작업**에서 **신뢰 자 신뢰 추가를**선택합니다. 
4. **데이터 원본 선택에**대한 신뢰 당사자 신뢰 마법사 추가에서 온라인 또는 로컬 네트워크에 게시된 신뢰 **당사자에 대한 데이터 가져오기**옵션을 사용합니다. 이 페더레이션 https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml메타데이터 URL을 지정합니다. 다른 기본 선택 항목을 그대로 둡니다. **닫기**를 선택합니다.
5. **클레임 규칙 편집** 마법사가 열립니다.
6. 클레임 **규칙 편집** 마법사에서 **규칙 추가를**선택합니다. **규칙 유형 선택에서** **LDAP 특성 보내기를 클레임으로**선택합니다. **다음**을 선택합니다.
7. **클레임 구성 규칙에서**다음 값을 지정합니다. 

   - **클레임 규칙 이름**: 이메일 클레임 규칙 
   - **특성 저장소**: 활성 디렉터리 
   - **LDAP 특성**: 전자 메일 주소 
   - **나가는 클레임 유형**: 전자 메일 주소

8. **마침**을 선택합니다.
9. **클레임 규칙 편집** 창에는 새 규칙이 표시됩니다. **적용**을 클릭합니다. 
10. **확인**을 클릭합니다.  

### <a name="create-an-email-transform-rule"></a>전자 메일 변환 규칙 만들기
1. 클레임 **규칙 편집으로** 이동하여 **규칙 추가를**클릭합니다. **규칙 유형 선택에서** **들어오는 클레임 변환을** 선택하고 **다음**을 클릭합니다. 
2. **클레임 구성 규칙에서**다음 값을 지정합니다. 

   - **클레임 규칙 이름**: 전자 메일 변환 규칙 
   - **수신 클레임 유형**: 전자 메일 주소 
   - **나가는 클레임 유형**: 이름 ID 
   - **나가는 이름 ID 형식**: 영구 식별자 
   - **모든 클레임 값을 통과를**선택합니다.

3. **마침**을 클릭합니다. 
4. **클레임 규칙 편집** 창에는 새 규칙이 표시됩니다. **적용**을 클릭합니다. 
5. **확인**을 클릭합니다. 이제 AD FS 서버는 SAML 2.0 프로토콜을 사용하여 직접 페더레이션을 위해 구성됩니다.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>WS-Fed 직접 페더레이션을 위한 AD FS 구성 
Azure AD B2B는 아래에 나열된 특정 요구 사항과 함께 WS-Fed 프로토콜을 사용하는 ID 공급자와 페더레이트하도록 구성할 수 있습니다. 현재 두 WS-Fed 공급자는 Azure AD와의 호환성테스트를 거쳤으며 AD FS 및 Shibboleth를 포함합니다. 여기서는 WS-Fed ID 공급자의 예로 AD FS(Active Directory Federation Services)를 사용합니다. Azure AD를 사용하여 WS-Fed 호환 공급자 간에 신뢰 당사자 신뢰를 설정하는 자세한 내용은 Azure AD ID 공급자 호환성 문서를 다운로드하십시오.

직접 페더레이션을 설정하려면 ID 공급자의 WS-Fed 메시지에서 다음 특성을 수신해야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결하거나 수동으로 입력하여 구성할 수 있습니다. [테스트 AD FS 인스턴스 만들기의](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) 12단계는 AD FS 끝점을 찾는 방법 또는 메타데이터 URL을 생성하는 방법을 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`설명합니다.
 
|특성  |값  |
|---------|---------|
|수동 요청자 엔드포인트     |`https://login.microsoftonline.com/login.srf`         |
|사용자     |`urn:federation:MicrosoftOnline`         |
|발급자     |예를 들어 파트너 IdP의 발급자 URI`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP에서 발행한 WS-Fed 토큰에 대한 필수 클레임:

|특성  |값  |
|---------|---------|
|불변ID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

다음 섹션에서는 WS-Fed ID 공급자의 예로 AD FS를 사용하여 필요한 특성 및 클레임을 구성하는 방법을 보여 줍니다.

### <a name="before-you-begin"></a>시작하기 전에
이 절차를 시작하기 전에 AD FS 서버를 이미 설정하고 작동해야 합니다. AD FS 서버 설정에 대한 도움말은 [Azure 가상 컴퓨터에서 테스트 AD FS 3.0 인스턴스 만들기를](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)참조하십시오.


### <a name="add-the-relying-party-trust-and-claim-rules"></a>신뢰 당사자 신뢰 및 클레임 규칙 추가 
1. AD FS 서버에서 **도구** > **AD FS 관리로**이동합니다. 
1. 탐색 창에서 신뢰 **관계** > **신뢰 신뢰 를 선택합니다.** 
1. **작업**에서 **신뢰 자 신뢰 추가를**선택합니다.  
1. **데이터 원본 선택에**대한 신뢰 당사자 신뢰 마법사 추가에서 온라인 또는 로컬 네트워크에 게시된 신뢰 **당사자에 대한 데이터 가져오기**옵션을 사용합니다. 이 페더레이션 `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`메타데이터 URL을 지정합니다.  다른 기본 선택 항목을 그대로 둡니다. **닫기**를 선택합니다.
1. **클레임 규칙 편집** 마법사가 열립니다. 
1. 클레임 **규칙 편집** 마법사에서 **규칙 추가를**선택합니다. **규칙 유형 선택에서** **사용자 지정 규칙을 사용하여 클레임 보내기를**선택합니다. *다음*을 선택합니다. 
1. **클레임 구성 규칙에서**다음 값을 지정합니다.

   - **클레임 규칙 이름**: 불변 ID 발급  
   - **사용자 지정 규칙**:`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. **마침**을 선택합니다. 
1. **클레임 규칙 편집** 창에는 새 규칙이 표시됩니다. **적용**을 클릭합니다.  
1. 동일한 **클레임 규칙 편집** 마법사에서 규칙 **추가를**선택합니다. **코호스 규칙 유형에서** **LDAP 특성 보내기를 클레임으로 선택합니다.** **다음**을 선택합니다.
1. **클레임 구성 규칙에서**다음 값을 지정합니다. 

   - **클레임 규칙 이름**: 이메일 클레임 규칙  
   - **특성 저장소**: 활성 디렉터리  
   - **LDAP 특성**: 전자 메일 주소  
   - **나가는 클레임 유형**: 전자 메일 주소 

1.  **마침**을 선택합니다. 
1.  **클레임 규칙 편집** 창에는 새 규칙이 표시됩니다. **적용**을 클릭합니다.  
1.  **확인**을 클릭합니다. AD FS 서버는 이제 WS-Fed를 사용하여 직접 페더레이션을 위해 구성됩니다.

## <a name="next-steps"></a>다음 단계
다음으로 Azure AD 포털에서 또는 PowerShell을 사용하여 [Azure AD에서 직접 페더레이션을 구성합니다.](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) 
