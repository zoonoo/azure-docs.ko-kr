---
title: LexisNexis를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 프로 파일링 및 id 유효성 검사 서비스인 LexisNexis를 사용 하 여 Azure AD B2C 인증을 통합 하는 방법을 알아보고 사용자의 장치에 따라 사용자 id를 확인 하 고 포괄적인 위험 평가를 제공 합니다.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c753e9a18f9869e1bf11aa437fb60484f2553e17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259257"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 LexisNexis 구성에 대 한 자습서

이 샘플 자습서에서는 [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)와 Azure AD B2C를 통합 하는 방법에 대 한 지침을 제공 합니다. LexisNexis는 다양 한 솔루션을 제공 하며 [여기](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)에서 찾을 수 있습니다. 이 샘플 자습서에서는 LexisNexis의 **ThreatMetrix** 솔루션을 다룹니다. ThreatMetrix는 프로 파일링 및 id 유효성 검사 서비스입니다. 사용자 id를 확인 하 고 사용자의 장치에 따라 포괄적인 위험 평가를 제공 하는 데 사용 됩니다.

이 통합은 등록 흐름 중에 사용자가 제공 하는 몇 가지 사용자 정보를 기반으로 프로 파일링을 수행 합니다. ThreatMetrix는 사용자가 로그인 할 수 있도록 허용할지 여부를 결정 합니다. 다음 특성은 ThreatMetrix의 위험 분석에서 고려 됩니다.

- Email
- 전화 번호
- 사용자의 컴퓨터에서 수집 된 프로 파일링 정보

## <a name="prerequisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 입니다.

## <a name="scenario-description"></a>시나리오 설명

ThreatMetrix 통합에는 다음 구성 요소가 포함 됩니다.

- Azure AD B2C – 사용자의 자격 증명을 확인 하 고 id 공급자 라고도 하는 권한 부여 서버입니다.

- ThreatMetrix – ThreatMetrix 서비스는 사용자가 제공 하는 입력을 가져와서 사용자의 컴퓨터에서 수집 된 프로 파일링 정보와 결합 하 여 사용자 상호 작용의 보안을 확인 합니다.

- 사용자 지정 Rest API –이 API는 Azure AD B2C와 ThreatMetrix 서비스 간의 통합을 구현 합니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![lexisnexis에 대 한 스크린샷-다이어그램](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|단계 | 설명 |
|:--------------|:-------------|
|1. | 사용자가 로그인 페이지에 도착 합니다. 사용자가 등록을 선택 하 여 새 계정을 만들고 페이지에 정보를 입력 합니다. Azure AD B2C 사용자 특성을 수집 합니다.
| 2. | Azure AD B2C 중간 계층 API를 호출 하 고 사용자 특성을 전달 합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집 하 고 LexisNexis API에서 사용할 수 있는 형식으로 변환 합니다. 그런 다음 LexisNexis로 보냅니다.  
| 4. | LexisNexis는 정보를 사용 하 고이를 처리 하 여 위험 분석을 기반으로 사용자 id의 유효성을 검사 합니다. 그런 다음 중간 계층 API에 결과를 반환 합니다.
| 5. | 중간 계층 API는 정보를 처리 하 고 Azure AD B2C에 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시 되 면 사용자에 게 오류 메시지가 표시 됩니다. 성공 응답이 표시 되 면 사용자가 인증 되 고 액세스 권한이 부여 됩니다.

## <a name="onboard-with-lexisnexis"></a>LexisNexis으로 등록

1. LexisNexis 계정을 만들려면 [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) 에 문의 하세요.

2. 요구 사항에 맞는 LexisNexis 정책을 만듭니다. [여기](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)에서 사용 가능한 설명서를 참조 하세요.

>[!NOTE]
> 정책 이름은 나중에 사용 됩니다.

계정이 만들어지면 API 구성에 필요한 정보를 받게 됩니다. 다음 섹션에서는이 프로세스에 대해 설명 합니다.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>LexisNexis를 사용 하 여 Azure AD B2C 구성

### <a name="part-1---deploy-the-api"></a>1 부-API 배포

제공 된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) 를 Azure 서비스에 배포 합니다. 이러한 [지침](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.

>[!NOTE]
>필요한 설정을 사용 하 여 Azure AD를 구성 하려면 배포 된 서비스의 URL이 필요 합니다.

### <a name="part-2---configure-the-api"></a>2 부-API 구성

응용 프로그램 설정은 [Azure의 App service에서 구성할](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)수 있습니다.  이 방법을 사용 하면 설정을 리포지토리로 체크 인하지 않고도 안전 하 게 구성할 수 있습니다. Rest API에 대 한 다음 설정을 제공 해야 합니다.

| 애플리케이션 설정 | 원본 | 참고 |
| :-------- | :------------| :-----------|
|ThreatMetrix: Url | ThreatMetrix 계정 구성 |     |
|ThreatMetrix: OrgId | ThreatMetrix 계정 구성 |     |
|ThreatMetrix: ApiKey |ThreatMetrix 계정 구성|  |
|ThreatMetrix: 정책 | ThreatMetrix에서 만든 정책 이름 | |
| BasicAuth: ApiUsername |API에 대 한 사용자 이름 정의| 사용자 이름은 Azure AD B2C 구성에 사용 됩니다.
| BasicAuth: ApiPassword | API에 대 한 암호를 정의 합니다. | Azure AD B2C 구성에서 암호가 사용 됩니다.

### <a name="part-3---deploy-the-ui"></a>3 부-UI 배포

이 솔루션은 Azure AD B2C에서 로드 하는 사용자 지정 UI 템플릿을 사용 합니다. 이러한 UI 템플릿은 ThreatMetrix 서비스로 직접 전송 되는 프로 파일링을 수행 합니다.

이러한 [지침](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#custom-page-content-walkthrough) 을 참조 하 여 포함 된 [UI 파일](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) 을 blob storage 계정에 배포 합니다. 이러한 지침에는 blob storage 계정 설정, CORS 구성 및 공용 액세스 사용이 포함 됩니다.

UI는 [대양 blue 템플릿을](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue)기반으로 합니다. 배포 된 위치를 참조 하도록 UI 내의 모든 링크를 업데이트 해야 합니다. UI 폴더에서을 찾아를 배포 된 https://yourblobstorage/blobcontainer 위치로 바꿉니다.

### <a name="part-4---create-api-policy-keys"></a>4 부-API 정책 키 만들기

이 [문서](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) 를 참조 하 여 두 개의 정책 키를 만듭니다. 하나는 API 사용자 이름, 하나는 위에서 정의한 api 암호에 대 한 키입니다.

이 샘플 정책은 다음 키 이름을 사용 합니다.

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>5 부-API URL 업데이트

제공 된 [trustframeworkextensions.xml 정책](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)에서 이라는 기술 프로필을 찾고 `Rest-LexisNexus-SessionQuery` `ServiceUrl` 위에 배포 된 API의 위치를 사용 하 여 메타 데이터 항목을 업데이트 합니다.

### <a name="part-6---update-ui-url"></a>6 부-UI URL 업데이트

제공 된 [trustframeworkextensions.xml 정책](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)에서 찾기 및 바꾸기를 수행 하 여 https://yourblobstorage/blobcontainer/ UI 파일이 배포 된 위치로를 검색 합니다.

>[!NOTE]
> 모범 사례로, 고객은 특성 컬렉션 페이지에서 동의 알림을 추가 하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보를 보내도록 사용자에 게 알립니다.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>7 부-Azure AD B2C 정책 구성

[로컬 계정 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) 을 다운로드 하 고 Azure AD B2C 테 넌 트에 대 한 [정책을](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) 구성 하려면이 [문서](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 를 참조 하세요.

>[!NOTE]
>제공 된 정책을 업데이트 하 여 특정 테 넌 트와 관련 된 정책을 업데이트 합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 정책 아래에서 **사용자 흐름**을 선택 합니다.

2. 이전에 만든 **사용자 흐름**을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 설정을 선택 합니다.

   a. **응용 프로그램**: 등록 된 앱을 선택 합니다 (JWT는 샘플).

   b. **회신 url**: **리디렉션 url** 을 선택 합니다.

   다. **사용자 흐름 실행**을 선택합니다.

4. 등록 흐름으로 이동 하 여 계정 만들기

5. 로그 아웃

6. 로그인 흐름을 통해 이동  

7. **Continue**를 입력 하면 ThreatMetrix 퍼즐이 팝업 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
