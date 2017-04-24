---
title: "Power BI Embedded에서 인증 및 권한 부여"
description: "Power BI Embedded에서 인증 및 권한 부여"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 93027f0f5467e0b21c47bbcbc84c67cdd50b26b8
ms.lasthandoff: 03/14/2017


---
# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Power BI Embedded에서 인증 및 권한 부여

Power BI Embedded 서비스는 명시적인 최종 사용자 인증 대신 인증 및 권한 부여에 대한 **키** 및 **앱 토큰**을 사용합니다. 이 모델에서 응용 프로그램이 최종 사용자에 대한 인증 및 권한 부여를 관리합니다. 필요한 경우 앱이 서비스에 요청된 보고서를 렌더링하라고 지시하는 앱 토큰을 만들어 보냅니다. 이 디자인에서는 앱이 사용자 인증 및 권한 부여에 Azure Active Directory를 사용할 수는 있지만 그럴 필요가 없습니다.

## <a name="two-ways-to-authenticate"></a>인증에 대한 두 가지 방법

**키** - 모든 Power BI Embedded REST API 호출에 키를 사용할 수 있습니다. 키는 **Azure 포털**에서 **모든 설정**, **액세스 키**를 차례로 선택하여 찾을 수 있습니다. 키는 항상 암호처럼 처리해야 합니다. 특정 작업 영역 컬렉션에서 모든 REST API 호출을 수행하는 권한이 이러한 키에 있습니다.

REST 호출에서 키를 사용하려면 다음 권한 부여 헤더를 추가합니다.            

    Authorization: AppKey {your key}

**앱 토큰** - 앱 토큰은 모든 포함 요청에 사용됩니다. 클라이언트 쪽에서 실행되도록 설계되었으므로 단일 보고서로 제한되며 만료 시간을 설정하는 것이 가장 좋습니다.

앱 토큰은 사용자 키 중 하나로 서명된 JWT(JSON Web Token)입니다.

앱 토큰은 다음 클레임을 포함할 수 있습니다.

| 클레임 | 설명 |
| --- | --- |
| **ver** |앱 토큰의 버전으로 현재 버전은 0.2.0입니다. |
| **aud** |토큰의 의도한 수신자입니다. Power BI Embedded의 경우 "https://analysis.windows.net/powerbi/api"를 사용합니다. |
| **iss** |토큰을 발급한 응용 프로그램을 나타내는 문자열입니다. |
| **type** |생성되는 앱 토큰의 유형입니다. 현재 지원되는 유일한 유형은 **embed**입니다. |
| **wcn** |토큰이 발급되는 대상 작업 영역 컬렉션 이름입니다. |
| **wid** |토큰이 발급되는 대상 작업 영역 ID입니다. |
| **rid** |토큰이 발급되는 보고서 ID입니다. |
| **username** (선택 사항) |RLS에 사용되며 RLS 규칙을 적용할 때 사용자를 식별하는 데 사용할 수 있는 문자열입니다. |
| **roles** (선택 사항) |행 수준 보안 규칙을 적용할 때 선택할 역할이 들어 있는 문자열입니다. 둘 이상의 역할을 전달하는 경우 문자열 배열로 전달해야 합니다. |
| **scp**(선택 사항) |사용 권한 범위를 포함하는 문자열입니다. 둘 이상의 역할을 전달하는 경우 문자열 배열로 전달해야 합니다. |
| **exp** (선택 사항) |토큰이 만료될 시간을 나타냅니다. Unix 타임스탬프로 전달되어야 합니다. |
| **nbf** (선택 사항) |토큰이 유효해지는 시작 시간을 나타냅니다. Unix 타임스탬프로 전달되어야 합니다. |

샘플 앱 토큰은 다음과 같이 표시됩니다.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

디코딩되면 결과는 다음과 같습니다.

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

앱 토큰을 쉽게 생성하는 SDK 내에서 사용할 수 있는 방법이 있습니다. 예를 들어, .NET의 경우 [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) 클래스 및 [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) 메서드를 살펴볼 수 있습니다.

.NET SDK의 경우 [범위](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes)를 참조할 수 있습니다.

## <a name="scopes"></a>범위

Embed 토큰으로 사용하는 경우 액세스 권한을 제공하는 리소스 사용을 제한하려고 할 수 있습니다. 이러한 이유로 범위가 지정된 사용 권한으로 토큰을 생성할 수 있습니다.

Power BI Embedded에 사용 가능한 범위는 다음과 같습니다.

|범위|설명|
|---|---|
|Dataset.Read|지정된 데이터 집합을 읽을 수 있는 사용 권한을 제공합니다.|
|Dataset.Write|지정된 데이터 집합을 쓸 수 있는 사용 권한을 제공합니다.|
|Dataset.ReadWrite|지정된 데이터 집합을 읽고 쓸 수 있는 사용 권한을 제공합니다.|
|Report.Read|지정된 보고서를 볼 수 있는 사용 권한을 제공합니다.|
|Report.ReadWrite|지정된 보고서를 보고 편집할 수 있는 사용 권한을 제공합니다.|
|Workspace.Report.Create|지정된 작업 영역 내에서 새 보고서를 만들 수 있는 사용 권한을 제공합니다.|
|Workspace.Report.Copy|지정된 작업 영역 내에서 기존 보고서를 복제할 수 있는 사용 권한을 제공합니다.|

다음과 같은 범위 간의 공백을 사용하여 여러 범위를 제공할 수 있습니다.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**필요한 클레임 - 범위**

scp: {scopesClaim} scopesClaim은 문자열 또는 문자열의 배열로서 작업 영역 리소스(예: 보고서, 데이터 집합 등)에 허용되는 사용 권한을 확인할 수 있습니다

정의된 범위를 사용하여 디코딩된 토큰은 다음과 비슷합니다.

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>작업 및 범위

|작업|대상 리소스|토큰 사용 권한|
|---|---|---|
|데이터 집합을 기반으로 새 메모리 내 보고서를 만듭니다.|데이터 집합|Dataset.Read|
|데이터 집합을 기반으로 새 메모리 내 보고서를 만들고 보고서를 저장합니다.|데이터 집합|*Dataset.Read<br>* Workspace.Report.Create|
|기존 메모리 내 보고서를 보고 탐색/편집합니다. Report.Read는 Dataset.Read를 의미합니다. 그러면 편집 내용을 저장하는 사용 권한을 허용하지 않습니다.|보고서|Report.Read|
|기존 보고서를 편집하고 저장합니다.|보고서|Report.ReadWrite|
|보고서(다른 이름으로 저장)의 복사본을 저장합니다.|보고서|*Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>다음은 흐름 방식입니다.
1. 응용 프로그램에 API 키 복사 **Azure Portal**에서 키를 가져올 수 있습니다.
   
    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
2. 토큰이 클레임을 어설션하며 만료 시간이 있습니다.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-2.png)
3. 토큰이 API 액세스 키로 서명됩니다.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-3.png)
4. 사용자가 보고서 보기를 요청합니다.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-4.png)
5. API 액세스 키로 토큰의 유효성이 검사됩니다.
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-5.png)
6. Power BI Embedded에서 사용자에게 보고서를 보냅니다.
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-6.png)

**Power BI Embedded** 에서 사용자에게 보고서를 보낸 후 사용자는 사용자 지정 앱에서 보고서를 볼 수 있습니다. 예를 들어 [판매 데이터 PBIX 분석 샘플](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)을 가져온 경우 샘플 웹앱이 다음과 같이 보입니다.

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="see-also"></a>참고 항목

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Microsoft Power BI Embedded 샘플 시작](power-bi-embedded-get-started-sample.md)  
[일반적인 Microsoft Power BI Embedded 시나리오](power-bi-embedded-scenarios.md)  
[Microsoft Power BI Embedded 시작](power-bi-embedded-get-started.md)  
[PowerBI-CSharp Git 리포지토리](https://github.com/Microsoft/PowerBI-CSharp)  
궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](http://community.powerbi.com/)


