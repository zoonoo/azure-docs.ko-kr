---
title: Azure Active Directory B2C로 Saviynt 구성에 대 한 자습서
titleSuffix: Azure AD B2C
description: 응용 프로그램 간 통합을 위한 Saviynt를 사용 하 여 IT 현대화을 간소화 하 고 보안, 거 버 넌 스 및 규정 준수를 강화 하는 Azure Active Directory B2C 자습서입니다. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d80a1ba515aa137eba57051f080b4a2b4f311072
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708770"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 Saviynt 구성에 대 한 자습서

이 샘플 자습서에서는 AD (Azure Active Directory) B2C를 [Saviynt](https://saviynt.com/)와 통합 하는 방법에 대 한 지침을 제공 합니다. Saviynt의 Security Manager 플랫폼은 단일 통합 플랫폼에서 오늘날의 비즈니스 요구에 대 한 가시성, 보안 및 거 버 넌 스를 제공 합니다. Saviynt는 응용 프로그램 위험과 거 버 넌 스, 인프라 관리, 권한 있는 계정 관리 및 고객 위험 분석을 통합 합니다.

이 샘플 자습서에서는 Saviynt를 설정 하 여 Azure AD B2C 사용자에 게 위임 된 관리에 대 한 세분화 된 액세스 제어를 제공 합니다. Saviynt 사용자가 Azure AD B2C를 관리할 수 있는 권한이 있는지 확인 하려면 다음을 확인 합니다.

- 사용자가 특정 작업을 수행할 수 있는지 확인 하는 기능 수준 보안입니다. 예를 들어 사용자를 만들고, 사용자를 업데이트 하 고, 사용자 암호를 다시 설정 하는 등의 방법을 사용 합니다.

- 사용자 관리 작업을 수행 하는 동안 사용자가 다른 사용자의 특정 특성을 읽고 쓸 수 있는지 여부를 결정 하는 필드 수준 보안입니다. 예를 들어 지원 센터 에이전트는 전화 번호를 업데이트 하 고 다른 모든 특성은 읽기 전용으로 설정할 수 있습니다.

- 사용자가 특정 사용자에 대해 특정 작업을 수행할 수 있는지 여부를 확인 하는 데이터 수준 보안 예를 들어 영국 지역의 지원 센터 관리자는 영국 사용자만 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)입니다. 테 넌 트는 Azure 구독에 연결 됩니다.

- Saviynt [구독](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>시나리오 설명

Saviynt 통합에는 다음 구성 요소가 포함 됩니다.

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) – 고객이 등록 하 고 로그인 하 여 프로필을 관리 하는 방법에 대 한 사용자 지정 제어를 지 원하는 b2b (기업 간 id)입니다.

- [Saviynt](https://saviynt.com/) – 사용자 수명 주기 관리에 대 한 세분화 된 위임 된 관리 및 Azure AD B2C 사용자의 액세스 관리를 제공 하는 id 거 버 넌 스 플랫폼입니다.  

- [MICROSOFT GRAPH api](https://docs.microsoft.com/graph/use-the-api) –이 api는 Azure AD B2C에서 Azure AD B2C 사용자 및 해당 액세스를 관리 하기 위해 Saviynt에 대 한 인터페이스를 제공 합니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![Saviynt 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-saviynt/saviynt-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 위임 된 관리자는 Saviynt를 통해 Azure AD B2C 사용자 관리 작업을 시작 합니다.
| 2. | Saviynt는 위임 된 관리자가 특정 작업을 수행할 수 있는 경우 해당 권한 부여 엔진을 사용 하 여 확인 합니다.
| 3. | Saviynt의 권한 부여 엔진은 권한 부여 성공/실패 응답을 보냅니다.
| 4. | Saviynt를 사용 하면 위임 된 관리자가 필요한 작업을 수행할 수 있습니다.
| 5. | Saviynt 사용자 특성과 함께 Microsoft Graph API를 호출 하 여에서 사용자를 관리 Azure AD B2C
| 6. | Microsoft Graph API는 Azure AD B2C에서 사용자를 생성/업데이트/삭제 합니다.
| 7. | Azure AD B2C는 성공/실패 응답을 보냅니다.
| 8. | 그러면 Microsoft Graph API가 Saviynt에 대 한 응답을 반환 합니다.

## <a name="onboard-with-saviynt"></a>Saviynt으로 등록

1. Saviynt 계정을 만들려면 [Saviynt](https://saviynt.com/contact-us/) 에 문의 하세요.

2. 위임 된 관리 정책을 만들고 사용자를 다양 한 역할을 가진 [위임 된 관리자로](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-concept-delegation) 할당 합니다.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Saviynt를 사용 하 여 Azure AD B2C 구성

### <a name="creating-an-azure-ad-application-for-saviynt"></a>Saviynt에 대 한 Azure AD 응용 프로그램 만들기

1. [Azure Portal](https://portal.azure.com/#home)에 로그인합니다.

2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

3. Azure Portal에서 **Azure AD B2C**를 검색 하 고 선택 합니다.

4. **앱 등록**  >  **새 등록**을 선택 합니다.

5. 응용 프로그램의 이름을 입력 합니다. 예를 들어 Saviynt를 선택 하 고 **만들기**를 선택 합니다.

6. **API 권한** 으로 이동 하 고 **+ 권한 추가를 선택 합니다.**

7. API 사용 권한 요청 페이지가 나타납니다. **Microsoft api** 탭을 선택 하 고 자주 사용 하는 microsoft api로 **Microsoft Graph** 을 선택 합니다.

8. 다음 페이지로 이동 하 여 **응용 프로그램 사용 권한**을 선택 합니다.

9. **디렉터리**를 선택 하 고 **디렉터리** 를 선택 합니다. All 및 **디렉터리. ReadWrite. 모든** 확인란을 선택 합니다.

10. **권한 추가**를 선택 합니다. 추가 된 사용 권한을 검토 합니다.

11. **기본 디렉터리 저장에 대 한 관리자 동의 부여를**선택  >  **Save**합니다.

12. **인증서 및 비밀** 으로 이동 하 고 **+ 클라이언트 암호 추가**를 선택 합니다. 클라이언트 암호 설명을 입력 하 고 만료 옵션을 선택한 다음 **추가**를 선택 합니다.

13. 비밀 키가 생성 되 고 클라이언트 암호 섹션에 표시 됩니다.

    >[!NOTE]
    > 클라이언트 암호는 나중에 필요 합니다.

14. **개요** 로 이동 하 여 **클라이언트 Id** 및 **테 넌 트 id**를 가져옵니다.

15. Saviynt에서 설정을 완료 하려면 테 넌 트 ID, 클라이언트 ID 및 클라이언트 암호가 필요 합니다.

### <a name="enabling-saviynt-to-delete-users"></a>Saviynt에서 사용자를 삭제 하도록 설정

아래 단계에서는 Saviynt를 사용 하 여 Azure AD B2C에서 사용자 삭제 작업을 수행 하는 방법을 설명 합니다.

>[!NOTE]
>[관리자 역할에 서비스 주체에 대 한 액세스 권한을 부여 하기 전에 위험을 평가 합니다.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

1. Windows 워크스테이션/서버에 최신 버전의 MSOnline PowerShell 모듈을 설치 합니다.

2. AzureAD PowerShell 모듈에 연결 하 고 다음 명령을 실행 합니다.

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>솔루션 테스트

Saviynt 응용 프로그램 테 넌 트로 이동 하 여 사용자 수명 주기 관리 및 액세스 관리 사용 사례를 테스트 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

- [Web API 응용 프로그램 만들기](https://docs.microsoft.com/azure/active-directory-b2c/add-web-api-application)
