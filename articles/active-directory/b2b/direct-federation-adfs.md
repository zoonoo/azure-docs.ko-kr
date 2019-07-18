---
title: B2B-Azure Active Directory에 대 한 AD FS와의 직접 페더레이션을 설정 | Microsoft Docs
description: 게스트가 Azure AD 앱에 로그인 할 수 있도록 직접 페더레이션에 대 한 id 공급자로 AD FS를 설정 하는 방법 알아보기
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
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544323"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>예제: 와 직접 페더레이션을 Active Directory Federation Services (AD FS) (미리 보기)
|     |
| --- |
| 직접 페더레이션에는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

이 문서에서는 설정 방법 설명 [직접 페더레이션](direct-federation.md) Active Directory Federation Services (AD FS)를 사용 하 여 SAML 2.0 또는 Ws-fed id 공급자로 합니다. 직접 페더레이션을 지원 하려면 특정 특성 및 클레임 id 공급자에 구성 되어야 합니다. 직접 페더레이션에 대 한 id 공급자를 구성 하는 방법을 설명 하기 위해, Active Directory Federation Services (AD FS)를 예로 사용 하겠습니다. AD FS SAML id 공급자 및 Ws-fed와 id 공급자를 모두 설정 하는 방법을 살펴봅니다.

> [!NOTE]
> 이 문서에서는 이해를 돕기 위해 SAML 및 Ws-fed와 둘 다에 대 한 AD FS를 설정 하는 방법을 설명 합니다. Id 공급자 인 AD FS의 직접 페더레이션 통합, Ws-fed 프로토콜을 사용 하는 것이 좋습니다. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>SAML 2.0 직접 페더레이션 위한 AD FS를 구성 합니다.
아래에 나열 된 특정 요구를 사용 하 여 SAML 프로토콜을 사용 하는 id 공급자와 페더레이션 하도록 azure AD B2B은 구성할 수 있습니다. SAML 구성 단계를 보여 주기 위해이 섹션에는 SAML 2.0에 대 한 AD FS를 설정 하는 방법을 보여 줍니다. 

직접 페더레이션을 설정 하려면 다음 특성을 id 공급자 SAML 2.0 응답에 수신 되어야 합니다. 온라인 보안 토큰 서비스 XML 파일에 연결 하거나 수동으로 입력 하 여 이러한 특성을 구성할 수 있습니다. 12 단계에서 [AD FS 테스트 인스턴스 만들기](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) AD FS 끝점을 찾는 방법 또는 예를 들어 메타 데이터 URL을 생성 하는 방법에 설명 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`합니다. 

|특성  |값  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|대상     |`urn:federation:MicrosoftOnline`         |
|발급자     |예를 들어 파트너 IdP의 URI 발급자 `http://www.example.com/exk10l6w90DHM0yi...`         |

다음 클레임을 id 공급자에서 발급 한 SAML 2.0 토큰에서 구성 해야 합니다.


|특성  |값  |
|---------|---------|
|NameID 형식     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


다음 섹션에서는 필수 특성 및 AD FS를 사용 하 여 SAML 2.0 id 공급자의 예로 클레임을 구성 하는 방법을 보여 줍니다.

### <a name="before-you-begin"></a>시작하기 전에

AD FS 서버를 설정 되어 있어야이 절차를 시작 하기 전에 작동 합니다. AD FS 서버 설정 사용 하 여 도움말을 참조 하세요 [Azure 가상 머신에서 테스트 AD FS 3.0 인스턴스 만들기](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)합니다.

### <a name="add-the-claim-description"></a>클레임 설명 추가

1. AD FS 서버에서 선택 **도구가** > **AD FS 관리**합니다.
2. 탐색 창에서 선택 **서비스** > **클레임 설명**합니다.
3. 아래 **동작**를 선택 **클레임 설명 추가**합니다.
4. 에 **클레임 설명 추가** 창에서 다음 값을 지정 합니다.

   - **표시 이름**: 영구 식별자
   - **클레임 식별자**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - 에 대 한 확인란을 선택 **이 페더레이션 서비스에서 수락할 수 있는 클레임 유형으로 페더레이션 메타 데이터에 클레임 설명이 게시**합니다.
   - 에 대 한 확인란을 선택 **이 페더레이션 서비스에 보낼 수 있는 클레임 유형으로이 클레임 설명은 페더레이션 메타 데이터에 게시**합니다.

5. **Ok**를 클릭합니다.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>신뢰 당사자 트러스트를 추가 하 고 클레임 규칙

1. AD FS 서버에서로 이동 **도구가** > **AD FS 관리**합니다.
2. 탐색 창에서 선택 **트러스트 관계** > **신뢰 당사자 트러스트**합니다.
3. 아래 **동작**를 선택 **신뢰 당사자 트러스트 추가**합니다. 
4. 에 대 한 추가 신뢰 당사자 트러스트 마법사에서 **데이터 원본 선택**, 옵션을 사용 하 여 **온라인 이나 로컬 네트워크에 게시 한 신뢰 당사자 관련 데이터 가져오기**합니다. 이 페더레이션 메타 데이터 URL-지정할 https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml 합니다. 다른 기본 선택 사항을 그대로 둡니다. **닫기**를 선택합니다.
5. 합니다 **클레임 규칙 편집** 마법사가 열립니다.
6. 에 **클레임 규칙 편집** 마법사 **규칙 추가**합니다. **규칙 유형 선택**를 선택 **LDAP 특성을 클레임으로 보내기**합니다. **다음**을 선택합니다.
7. **클레임 규칙 구성**를 다음 값을 지정 합니다. 

   - **클레임 규칙 이름**: 전자 메일 클레임 규칙 
   - **특성 저장소**: Active Directory 
   - **LDAP 특성**: 전자 메일 주소 
   - **나가는 클레임 유형이**: 전자 메일 주소

8. **마침**을 선택합니다.
9. 합니다 **클레임 규칙 편집** 창에 새 규칙이 표시 됩니다. **적용**을 클릭합니다. 
10. **Ok**를 클릭합니다.  

### <a name="create-an-email-transform-rule"></a>전자 메일 변환 규칙 만들기
1. 로 이동 **클레임 규칙 편집** 누릅니다 **규칙 추가**합니다. **규칙 유형 선택**를 선택 **들어오는 클레임 변환** 누릅니다 **다음**합니다. 
2. **클레임 규칙 구성**를 다음 값을 지정 합니다. 

   - **클레임 규칙 이름**: 전자 메일 변환 규칙 
   - **들어오는 클레임 유형이**: 전자 메일 주소 
   - **나가는 클레임 유형이**: 이름 ID 
   - **보내는 이름 ID 형식**: 영구 식별자 
   - **모든 클레임 값 통과**를 선택합니다.

3. **Finish**를 클릭합니다. 
4. 합니다 **클레임 규칙 편집** 창에 새 규칙이 표시 됩니다. **적용**을 클릭합니다. 
5. **확인**을 클릭합니다. 이제 AD FS 서버 SAML 2.0 프로토콜을 사용 하 여 직접 페더레이션에 대 한 구성 됩니다.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Ws-fed 직접 페더레이션 위한 AD FS를 구성 합니다. 
아래에 나열 된 특정 요구 사항을 Ws-fed 프로토콜을 사용 하는 id 공급자와 페더레이션 하도록 azure AD B2B은 구성할 수 있습니다. 현재 AD FS 및 Shibboleth를 포함 하는 Azure AD와의 호환성에 대 한 두 Ws-fed 공급자 테스트 되었습니다. 여기에서 Active Directory Federation Services (AD FS) Ws-fed id 공급자의 예로 사용 하겠습니다. Azure AD 사용 하 여 Ws-fed 규격 공급자 간에 신뢰 당사자 트러스트를 설정 하는 방법에 대 한 자세한 내용은 Azure AD Id 공급자 호환성 문서를 다운로드 합니다.

직접 페더레이션을 설정 하려면 다음 특성을 id 공급자의 Ws-fed 메시지에서 수신 되어야 합니다. 온라인 보안 토큰 서비스 XML 파일에 연결 하거나 수동으로 입력 하 여 이러한 특성을 구성할 수 있습니다. 12 단계에서 [AD FS 테스트 인스턴스 만들기](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) AD FS 끝점을 찾는 방법 또는 예를 들어 메타 데이터 URL을 생성 하는 방법에 설명 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`합니다.
 
|특성  |값  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|대상     |`urn:federation:MicrosoftOnline`         |
|발급자     |예를 들어 파트너 IdP의 URI 발급자 `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP에서 발급 한 Ws-fed 토큰에 대 한 필수 클레임:

|특성  |값  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

다음 섹션에서는 필수 특성을 구성 하는 방법을 보여 줍니다 및 Ws-fed와 id 공급자의 예로 AD FS를 사용 하 여 클레임입니다.

### <a name="before-you-begin"></a>시작하기 전에
AD FS 서버를 설정 되어 있어야이 절차를 시작 하기 전에 작동 합니다. AD FS 서버 설정 사용 하 여 도움말을 참조 하세요 [Azure 가상 머신에서 테스트 AD FS 3.0 인스턴스 만들기](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)합니다.


### <a name="add-the-relying-party-trust-and-claim-rules"></a>신뢰 당사자 트러스트를 추가 하 고 클레임 규칙 
1. AD FS 서버에서로 이동 **도구가** > **AD FS 관리**합니다. 
1. 탐색 창에서 선택 **트러스트 관계** > **신뢰 당사자 트러스트**합니다. 
1. 아래 **동작**를 선택 **신뢰 당사자 트러스트 추가**합니다.  
1. 추가 당사자 트러스트 마법사에 대 한 신뢰 **데이터 원본 선택**, 옵션을 사용 하 여 **온라인 이나 로컬 네트워크에 게시 한 신뢰 당사자 관련 데이터 가져오기**합니다. 이 페더레이션 메타 데이터 URL 지정: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`합니다.  다른 기본 선택 사항을 그대로 둡니다. **닫기**를 선택합니다.
1. 합니다 **클레임 규칙 편집** 마법사가 열립니다. 
1. 에 **클레임 규칙 편집** 마법사 **규칙 추가**합니다. **규칙 유형 선택**를 선택 **사용자 지정 규칙을 사용 하 여 클레임 보내기**합니다. *다음*을 선택합니다. 
1. **클레임 규칙 구성**를 다음 값을 지정 합니다.

   - **클레임 규칙 이름**: 변경이 불가능 한 문제 Id  
   - **사용자 지정 규칙**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. **마침**을 선택합니다. 
1. 합니다 **클레임 규칙 편집** 창에 새 규칙이 표시 됩니다. **적용**을 클릭합니다.  
1. 동일한 **클레임 규칙 편집** 마법사 **규칙 추가**합니다. **Cohose 규칙 유형**를 선택 **LDAP 특성을 클레임으로 보내기**합니다. **다음**을 선택합니다.
1. **클레임 규칙 구성**를 다음 값을 지정 합니다. 

   - **클레임 규칙 이름**: 전자 메일 클레임 규칙  
   - **특성 저장소**: Active Directory  
   - **LDAP 특성**: 전자 메일 주소  
   - **나가는 클레임 유형이**: 전자 메일 주소 

1.  **마침**을 선택합니다. 
1.  합니다 **클레임 규칙 편집** 창에 새 규칙이 표시 됩니다. **적용**을 클릭합니다.  
1.  **확인**을 클릭합니다. 이제 AD FS 서버에 직접 페더레이션 Ws-fed를 사용 하 여 구성 됩니다.

## <a name="next-steps"></a>다음 단계
다음으로 살펴볼 [Azure AD에서 직접 페더레이션을 구성](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) Azure AD 포털 또는 PowerShell을 사용 하 여 합니다. 
