---
title: Azure Active Directory B2C Arkose Labs
titleSuffix: Azure AD B2C
description: Arkose Labs를 사용 하 여 Azure AD B2C 인증을 통합 하 여 봇 공격, 계정 인수 공격 및 사기성 계정 입구를 방지 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4d8174cd0bfdb2297099b403fb836210c5529ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86170227"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 Arkose Labs를 구성 하기 위한 자습서

이 자습서에서는 Arkose Labs를 사용 하 여 Azure AD B2C 인증을 통합 하는 방법에 대해 알아봅니다. Arkose Labs는 봇 공격, 계정 인수 공격 및 사기성 계정 입구에 대해 조직을 지원 합니다.  

## <a name="prerequisites"></a>사전 요구 사항

시작 하려면 다음이 필요 합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](tutorial-create-tenant.md) 입니다.

## <a name="scenario-description"></a>시나리오 설명

다음 다이어그램에서는 Arkose Labs를 Azure AD B2C와 통합 하는 방법을 설명 합니다.

![Arkose Labs 아키텍처 다이어그램](media/partner-arkose-labs/arkose-architecture-diagram.png)

| 단계  | 설명 |
|---|---|
|1     | 사용자가 이전에 만든 계정으로 로그인 합니다. 사용자가 제출을 선택 하면 Arkose Labs 적용 챌린지가 표시 됩니다. 사용자가 챌린지를 완료 한 후 상태는 Arkose Labs로 전송 되어 토큰을 생성 합니다.        |
|2     |  Arkose Labs는 Azure AD B2C로 토큰을 다시 보냅니다.       |
|3     |  로그인 양식이 제출 되기 전에 토큰은 확인을 위해 Arkose Labs에 전송 됩니다.       |
|4     |  Arkose은 챌린지에서 성공 또는 실패 결과를 다시 보냅니다.       |
|5     |  챌린지가 성공적으로 완료 되 면 로그인 양식이 Azure AD B2C 전송 되 고 Azure AD B2C 인증을 완료 합니다.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Arkose Labs를 사용 하 여 등록

1. 먼저 [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) 에 문의 하 고 계정을 만듭니다.

2. 계정을 만들었으면로 이동 https://dashboard.arkoselabs.com/login 합니다.

3. 대시보드 내에서 사이트 설정으로 이동 하 여 공개 키와 개인 키를 찾습니다. 이 정보는 나중에 Azure AD B2C를 구성 하는 데 필요 합니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>1 부-사용자 지정 HTML을 저장할 blob 저장소 만들기

저장소 계정을 만들려면 다음 단계를 수행 합니다.  

1. Azure Portal에 로그인 합니다.

2. Azure 구독을 포함 하는 디렉터리를 사용 해야 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 구독을 포함 하는 디렉터리를 선택 합니다. 이 디렉터리는 Azure B2C 테넌트가 포함된 디렉터리와 다릅니다.

3. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스** 를 선택한 다음  **저장소 계정**을 검색 하 고 선택 합니다.

4.  **추가**를 선택합니다.

5.  **리소스 그룹**에서 **새로 만들기**를 선택 하 고 새 리소스 그룹의 이름을 입력 한 다음 **확인**을 선택 합니다.

6. 스토리지 계정의 이름을 입력합니다. 선택하는 이름은 Azure 전체에서 고유해야 하고, 3~24자 사이여야 하고, 숫자 및 소문자만 포함할 수 있습니다.

7. 스토리지 계정의 위치를 선택하거나 기본 위치를 적용합니다.

8. 다른 모든 기본값을 적용 하 고  **검토 &** 만들기를 선택  >  **Create**합니다.

9. 스토리지 계정을 만든 후  **리소스로 이동**을 선택합니다.

#### <a name="create-a-container"></a>컨테이너 만들기

1. 저장소 계정의 개요 페이지에서  **blob**을 선택 합니다.

2.   **컨테이너**를 선택 하 고 컨테이너에 대 한 이름을 입력 한 다음  **blob** (blob에 대 한 익명 읽기 액세스 전용)을 선택 하 고 **확인**을 선택 합니다.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>CORS (원본 간 리소스 공유) 사용

브라우저의 Azure AD B2C 코드는 현대적이고 표준적인 방법을 사용하여 사용자 흐름에서 지정하는 URL의 사용자 지정 콘텐츠를 로드합니다. CORS를 사용 하면 다른 도메인에서 웹 페이지의 제한 된 리소스를 요청할 수 있습니다.

1. 메뉴에서  **CORS**를 선택 합니다.

2.   **허용 되는 원본**에 대해을 입력  `https://your-tenant-name.b2clogin.com` 합니다. -테 넌 트 이름을 Azure AD B2C 테 넌 트의 이름으로 바꿉니다. 예를 들면  `https://fabrikam.b2clogin.com`입니다. 모든 소문자를 사용 하 여 테 넌 트 이름을 입력 합니다.

3.  **허용 되는 방법**에 대해 **가져오기**, **배치**및 **옵션**을 선택 합니다.

4. **허용된 헤더**에 별표(*)를 입력합니다.

5.  **노출 된 헤더**에 별표 (*)를 입력 합니다.

6. **최대 기간**에 200을 입력합니다.

   ![Arkose Labs 등록 및 로그인](media/partner-arkose-labs/signup-signin-arkose.png)

7. **저장**을 선택합니다.

### <a name="part-2--set-up-a-back-end-server"></a>2 부-백 엔드 서버 설정

Git Bash를 다운로드 하 고 다음 단계를 수행 합니다.

1. 지시에 따라 [웹 앱을 만듭니다](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-php).App Service "표시에 첫 번째 PHP 앱을 배포 했습니다.

2. 로컬 폴더를 열고 **verify-token** **파일의 이름을 바꿉니다.**

3. 새로 이름이 바뀐 파일인 verify-token 파일을 열고 다음을 수행 합니다.

   a. 콘텐츠를 [GitHub 리포지토리에](https://github.com/ArkoseLabs/Azure-AD-B2C)있는 verify-token 파일의 콘텐츠로 바꿉니다.

   b. Arkose Labs 대시보드에서 가져온 개인 키를 사용 하 여 3 줄의 <private_key>를 바꿉니다.

4. 로컬 터미널 창에서 Git의 변경 내용을 커밋한 후 Git Bash에 다음을 입력 하 여 코드 변경 내용을 Azure에 푸시합니다.

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>3 부-최종 설치

#### <a name="store-the-custom-html"></a>사용자 지정 HTML 저장

1. [GitHub 리포지토리에](https://github.com/ArkoseLabs/Azure-AD-B2C)저장 된 index.html 파일을 엽니다.

2. 의 모든 인스턴스 `<tenantname>` 를 b2C 테 넌 트 이름 (즉,)으로 바꿉니다 `<tenantname>.b2clogin.com` . 4 개의 인스턴스가 있어야 합니다.

3. 를 `<appname>` 2 부, 1 단계에서 만든 앱 이름으로 바꿉니다.

   ![Arkose Labs Azure CLI 보여 주는 스크린샷](media/partner-arkose-labs/arkose-azure-cli.png)

4. `<public_key>`64 줄의을 Arkose Labs 대시보드에서 가져온 공개 키로 바꿉니다.

5. index.html 파일을 위에서 만든 blob 저장소에 업로드 합니다.

6. **저장소**  >  **컨테이너**  >  **업로드**로 이동 합니다.

#### <a name="set-up-azure-ad-b2c"></a>Azure AD B2C 설정

> [!NOTE]
> Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만듭니다.

1. [여기](tutorial-create-user-flows.md)에서 정보에 따라 사용자 흐름을 만듭니다. **사용자 흐름 테스트**섹션에 도달 하면 중지 합니다.

2. [사용자 흐름](user-flow-javascript-overview.md)에서 JavaScript를 사용 하도록 설정 합니다.

3. 동일한 사용자 흐름 페이지에서 사용자 지정 페이지 url을 사용 하도록 설정 합니다. **사용자 흐름**  >  **페이지 레이아웃**  >  **사용 사용자 지정 페이지 내용 사용**  =  **예**  >  **사용자 지정 페이지 url 삽입**을 참조 하세요.
이 사용자 지정 페이지 URL은 blob 저장소 내의 index.html 파일 위치에서 가져옵니다.  

   ![Arkose Labs 저장소 url을 보여 주는 스크린샷](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 **정책**아래에서 **사용자 흐름**을 선택 합니다.

2. 이전에 만든 사용자 흐름을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 설정을 선택 합니다.

   a. **응용 프로그램** -등록 된 앱을 선택 합니다 (JWT는 샘플).

   b. **회신 url** -리디렉션 url을 선택 합니다.

   다. **사용자 흐름 실행**을 선택합니다.

4. 등록 흐름을 진행 하 고 계정을 만듭니다.

5. 로그아웃합니다.

6. 로그인 흐름을 진행 합니다.

7. **계속**을 선택한 후 Arkose Labs 퍼즐이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](custom-policy-overview.md)

- [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md?tabs=applications)
