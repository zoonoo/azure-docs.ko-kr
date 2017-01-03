---
title: "Azure Active Directory 로그인 활동 보고서 API 참조 | Microsoft Docs"
description: "Azure Active Directory 로그인 활동 보고서 API에 대한 참조"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: dce65678f9fc96d5802a7b705689cc63e6532c84


---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Azure Active Directory 로그인 활동 보고서 API 참조
이 항목은 Azure Active Directory Reporting API에 대한 항목 컬렉션의 일부입니다.  
Azure AD Reporting은 코드 또는 관련된 도구를 사용하여 로그인 활동 보고서 데이터에 액세스할 수 있는 API를 제공합니다.
이 항목은 **로그인 활동 보고서 API**에 대한 참조 정보를 제공합니다.

다음을 참조하세요.

* [로그인 활동](active-directory-reporting-azure-portal.md#sign-in-activities) 을 참조하세요.
* [Azure Active Directory Reporting API 시작](active-directory-reporting-api-getting-started.md) 을 참조하세요.

질문, 문제 또는 피드백은 [AAD Reporting 도움말](mailto:aadreportinghelp@microsoft.com)에 문의하세요.

## <a name="who-can-access-the-api-data"></a>API 데이터에 액세스할 수 있는 사용자는 누구인가요?
* 보안 관리 또는 보안 판독기 역할의 사용자
* 전역 관리자
* API에 액세스하는 인증이 있는 모든 앱(전역 관리자의 사용 권한에 따라 앱 권한 부여를 설정할 수 있음).

## <a name="prerequisites"></a>필수 조건
Reporting API를 통해 이 보고서에 액세스하려면 다음이 있어야 합니다.

* [Azure Active Directory Premium P1 또는 P2 Edition](active-directory-editions.md)
* [Azure AD Reporting API에 액세스하기 위한 필수 구성 요소](active-directory-reporting-api-prerequisites.md)를 완료합니다. 

## <a name="accessing-the-api"></a>API에 액세스
예를 들어 [Graph Explorer](https://graphexplorer2.cloudapp.net) 를 통해 또는 프로그래밍 방식으로 PowerShell을 사용하여 이 API에 액세스할 수 있습니다. PowerShell이 AAD Graph REST 호출에 사용된 OData 필터 구문을 올바르게 해석하기 위해 '(억음 부호) 기호를 사용하여 $ 기호를 "이스케이프"해야 합니다. ' 기호는 [PowerShell의 이스케이프 문자](https://technet.microsoft.com/library/hh847755.aspx)로 사용되어 PowerShell에서 $ 기호의 리터럴 해석을 수행하고 PowerShell 변수 이름(예: $filter)으로 혼동하지 않도록 할 수 있습니다.

이 항목은 Graph Explorer에 집중합니다. PowerShell 예제는 이 [PowerShell 스크립트](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script)를 참조하세요.

## <a name="api-endpoint"></a>API 끝점
다음과 같은 기본 URI를 사용하여 이 API에 액세스할 수 있습니다.  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



데이터의 볼륨 때문에 이 API는 백만 개의 반환된 레코드로 제한됩니다. 

이 호출은 배치에서 데이터를 반환합니다. 각 배치에는 최대 1000개 레코드가 있습니다.  
레코드의 다음 배치를 가져오려면 [다음] 링크를 사용합니다. 첫 번째 반환되는 레코드의 집합에서 [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) 정보를 가져옵니다. 건너뛰기 토큰은 결과 집합의 끝에 있습니다.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>지원되는 필터
필터의 형태로 API 호출에서 반환되는 레코드의 수를 좁힐 수 있습니다.  
로그인 API 관련 데이터의 경우 다음 필터를 지원합니다.

* **$top=\<반환될 레코드의 수\>** - 반환된 레코드의 수를 제한합니다. 이 작업은 비용이 많이 드는 작업입니다. 수천 개의 개체를 반환하려는 경우 이 필터를 사용하지 않아야 합니다.  
* **$filter=\<필터 문\>** - 지원되는 필터 필드를 기반으로 원하는 레코드 종류를 지정합니다.

## <a name="supported-filter-fields-and-operators"></a>지원되는 필터 필드 및 연산자
원하는 레코드의 종류를 지정하려면 다음 필터 필드 중 하나 또는 조합을 포함할 수 있는 필터 문을 빌드할 수 있습니다.

* [signinDateTime](#signindatetime) - 날짜 또는 날짜 범위를 정의합니다.
* [userId](#userid) - 특정 사용자 기반 사용자의 ID를 정의합니다.
* [userPrincipalName](#userprincipalname) - 특정 사용자 기반 사용자의 사용자 계정 이름(UPN)을 정의합니다.
* [appId](#appid) - 특정 앱 기반 응용 프로그램의 ID를 정의합니다.
* [appDisplayName](#appdisplayname) - 특정 앱 기반 앱의 표시 이름을 정의합니다.
* [loginStatus](#loginStatus) - 로그인의 상태를 정의합니다(성공/실패).

> [!NOTE]
> Graph Explorer를 사용하는 경우 필터 필드인 각 문자에 대한 정확한 대/소문자를 사용해야 합니다.
> 
> 

반환된 데이터의 범위를 좁히기 위해 지원되는 필터 및 필터 필드의 조합을 빌드할 수 있습니다. 예를 들어, 다음 문은 2016년 7월 1일과 2016년 7월 6일 사이에 상위 10개의 레코드를 반환합니다.

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**지원되는 연산자**: eq, ge, le, gt, lt

**예제**:

특정 날짜 사용

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



날짜 범위 사용    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**참고**:

날짜/시간 매개 변수는 UTC 형식이어야 합니다. 

- - -
### <a name="userid"></a>userId
**지원되는 연산자**: eq

**예제**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**참고**:

userId의 값은 문자열 값입니다.

- - -
### <a name="userprincipalname"></a>userPrincipalName
**지원되는 연산자**: eq

**예제**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**참고**:

userPrincipalName의 값은 문자열 값입니다.

- - -
### <a name="appid"></a>appId
**지원되는 연산자**: eq

**예제**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**참고**:

appId의 값은 문자열 값입니다.

- - -
### <a name="appdisplayname"></a>appDisplayName
**지원되는 연산자**: eq

**예제**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**참고**:

appDisplayName의 값은 문자열 값입니다.

- - -
### <a name="loginstatus"></a>loginStatus
**지원되는 연산자**: eq

**예제**:

    $filter=loginStatus+eq+'1'  


**참고**:

loginStatus에는 0 - 성공, 1 - 오류라는 두 개의 옵션이 있습니다.

- - -
## <a name="next-steps"></a>다음 단계
* 필터링된 로그인 활동에 대한 예제를 참조하시겠습니까? [Azure Active Directory 로그인 활동 보고서 API 샘플](active-directory-reporting-api-sign-in-activity-samples.md)을 확인하세요.
* Azure AD Reporting API에 대해 자세히 살펴보시겠습니까? [Azure Active Directory Reporting API 시작](active-directory-reporting-api-getting-started.md)을 참조하세요.




<!--HONumber=Dec16_HO5-->


