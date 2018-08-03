---
title: LUIS 응용 프로그램에 대한 액세스 이해 - Azure | Microsoft Docs
description: LUIS 작성에 액세스하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: fe0ee7d575e69c883ad7f980477e86fbd004778e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397246"
---
# <a name="authoring-and-endpoint-user-access"></a>작성 및 끝점 사용자 액세스
작성 액세스는 소유자 및 협력자가 사용할 수 있습니다. 개인 앱의 경우 소유자 및 협력자는 끝점 액세스를 사용할 수 있습니다. 공용 앱의 경우 LUIS 계정을 소유하고 공용 앱 ID를 가진 모든 사용자가 끝점 액세스를 사용할 수 있습니다. 

## <a name="access-to-authoring"></a>작성에 대한 액세스
[LUIS](luis-reference-regions.md#luis-website) 웹 사이트 또는 [작성 API](https://aka.ms/luis-authoring-apis)에서 앱에 액세스하는 것은 앱 소유자가 제어합니다. 

소유자 및 모든 협력자가 앱을 작성하기 위해 액세스할 수 있습니다. 

|작성 액세스에 포함된 작업|메모|
|--|--|
|끝점 키 추가 또는 제거||
|버전 내보내기||
|끝점 로그 내보내기||
|버전 가져오기||
|앱 공개|앱이 공용이면 작성 또는 끝점 키가 있는 모든 사용자가 앱을 쿼리할 수 있습니다.|
|모델 수정|
|게시|
|[활성 학습](luis-how-to-review-endoint-utt.md)의 끝점 발화 검토|
|학습|

## <a name="access-to-endpoint"></a>끝점에 대한 액세스
LUIS를 쿼리하기 위한 끝점에 대한 액세스는 **설정** 페이지에서 앱의 **공용** 설정을 통해 제어합니다. 개인 앱의 끝점 쿼리는 나머지 할당량 적중을 사용하여 권한이 있는 키를 확인합니다. 공용 앱의 끝점 쿼리는 나머지 할당량 적중도 확인하는 끝점 키를 제공해야 합니다(누구든지 쿼리를 만드는 사용자의 끝점 키). 

끝점 키는 GET 요청의 쿼리 문자열 또는 POST 요청의 헤더로 전달됩니다.

![앱을 공용으로 설정](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>개인 앱 끝점 보안
개인 앱의 끝점은 다음에만 제공됩니다.

|키 및 사용자|설명|
|--|--|--|
|소유자의 작성 키| 최대 1000회의 끝점 적중|
|협력자의 작성 키| 최대 1000회의 끝점 적중|
|**[게시](luis-how-to-publish-app.md)** 페이지에서 추가된 끝점 키|소유자 및 협력자가 끝점 키를 추가할 수 있음|

기타 작성 또는 끝점 키에는 액세스 권한이 **없습니다**.

### <a name="public-app-endpoint-access"></a>공용 앱 끝점 액세스
앱의 **설정** 페이지에서 앱을 **공용**으로 구성합니다. 앱이 공용으로 구성되면 키에 전체 끝점 할당량이 사용되지 않는 한 ‘모든’ 유효한 LUIS 작성 키 또는 LUIS 끝점 키가 앱을 쿼리할 수 있습니다.

소유자 또는 협력자가 아닌 사용자는 앱 ID가 제공된 경우에만 공용 앱에 액세스할 수 있습니다. LUIS에는 공용 앱을 검색할 수 있는 공개 ‘시장’이나 다른 방법이 없습니다.  

## <a name="microsoft-user-accounts"></a>Microsoft 사용자 계정
작성자 및 협력자는 게시 페이지에서 LUIS에 키를 추가할 수 있습니다. Azure Portal에서 LUIS 키를 만드는 Microsoft 사용자 계정은 앱 소유자 또는 앱 협력자여야 합니다. 

Active Directory 사용자 계정에 자세히 알아보려면 [Azure Active Directory 테넌트 사용자](luis-how-to-collaborate.md#azure-active-directory-tenant-user)를 참조하세요. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>끝점 보안 
서버 간 환경에서 LUIS 끝점 키를 호출하여 볼 수 있는 사용자를 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우, 봇과 LUIS 간의 연결은 이미 안전합니다. LUIS 끝점을 직접 호출하는 경우에는 제어된 액세스(예: [AAD](https://azure.microsoft.com/services/active-directory/))를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API가 호출되고 인증 및 권한 부여가 확인되면 LUIS에 해당 호출을 전달합니다. 이 전략은 가로채기 공격을 방지하지 않지만 사용자의 끝점을 난독 처리하고, 액세스를 추적하도록 허용하고, 끝점 응답 로깅(예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가하도록 허용합니다.  

## <a name="security-compliance"></a>보안 준수
LUIS는 ISO 27001:2013 및 ISO 27018:2014 감사를 성공적으로 완료했고 감사 보고서에 비규격 없음(결과)이 포함되었습니다. 또한 LUIS는 성숙도 평가의 가능한 최고 금상과 함께 CSA STAR 인증을 획득했습니다. Azure는 이 인증을 얻은 유일한 주요 공용 클라우드 서비스 공급자입니다. 자세한 내용은 [보안 센터](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO 페이지에 참조되는 Azure의 기본 [준수 개요](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 문서에서 업데이트된 범위 설명에 포함된 LUIS를 찾을 수 있습니다.  

## <a name="next-steps"></a>다음 단계

최고의 예측을 위해 의도 및 엔터티를 사용하는 방법을 알아보려면 [모범 사례](luis-concept-best-practices.md)를 참조하세요.