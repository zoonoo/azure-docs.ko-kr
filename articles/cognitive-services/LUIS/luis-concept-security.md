---
title: 공동 작업 시 보안
titleSuffix: Language Understanding - Azure Cognitive Services
description: 작성 액세스는 소유자 및 협력자가 사용할 수 있습니다. 프라이빗 앱의 경우 소유자 및 협력자는 엔드포인트 액세스를 사용할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 499854bcf6774c3e4eee350c1dd4a2204885f3b1
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522486"
---
# <a name="authoring-and-endpoint-user-access"></a>작성 및 엔드포인트 사용자 액세스
작성 액세스는 소유자 및 협력자가 사용할 수 있습니다. 프라이빗 앱의 경우 소유자 및 협력자는 엔드포인트 액세스를 사용할 수 있습니다. 공용 앱의 경우 LUIS 계정을 소유하고 공용 앱 ID를 가진 모든 사용자가 엔드포인트 액세스를 사용할 수 있습니다. 

## <a name="access-to-authoring"></a>작성에 대한 액세스
[LUIS](luis-reference-regions.md#luis-website) 웹 사이트 또는 [작성 API](https://go.microsoft.com/fwlink/?linkid=2092087)에서 앱에 액세스하는 것은 앱 소유자가 제어합니다. 

소유자 및 모든 협력자가 앱을 작성하기 위해 액세스할 수 있습니다. 

|작성 액세스에 포함된 작업|메모|
|--|--|
|엔드포인트 키 추가 또는 제거||
|버전 내보내기||
|엔드포인트 로그 내보내기||
|버전 가져오기||
|앱 공개|앱이 공용이면 작성 또는 엔드포인트 키가 있는 모든 사용자가 앱을 쿼리할 수 있습니다.|
|모델 수정|
|게시|
|[활성 학습](luis-how-to-review-endpoint-utterances.md)의 엔드포인트 발화 검토|
|학습|

## <a name="access-to-endpoint"></a>엔드포인트에 대한 액세스

엔드포인트 쿼리에 대한 액세스는 **관리** 섹션, **애플리케이션 정보** 페이지의 설정으로 제어됩니다. 

![앱을 공용으로 설정](./media/luis-concept-security/set-application-as-public.png)

|[개인 엔드포인트](#private-app-endpoint-security)|[공용 엔드포인트](#public-app-endpoint-access)|
|:--|:--|
|소유자 및 협력자에게 제공|소유자, 협력자 및 앱 ID를 아는 그 밖의 모든 사람에게 제공|

### <a name="private-app-endpoint-security"></a>프라이빗 앱 엔드포인트 보안

프라이빗 앱의 엔드포인트는 다음에만 제공됩니다.

|키 및 사용자|설명|
|--|--|
|소유자의 작성 키| 최대 1000회의 엔드포인트 적중|
|협력자의 작성 키| 최대 1000회의 엔드포인트 적중|
|작성자 또는 협력자가 LUIS에 할당한 모든 키|키 사용량 계층 기반|

#### <a name="microsoft-user-accounts"></a>Microsoft 사용자 계정

작성자 및 협력자는 프라이빗 LUIS 앱에 키를 할당할 수 있습니다. Azure Portal에서 LUIS 키를 만드는 Microsoft 사용자 계정은 앱 소유자 또는 앱 협력자여야 합니다. 다른 Azure 계정에서 프라이빗 앱에 키를 할당할 수 없습니다.

Active Directory 사용자 계정에 자세히 알아보려면 [Azure Active Directory 테넌트 사용자](luis-how-to-collaborate.md#azure-active-directory-tenant-user)를 참조하세요. 

### <a name="public-app-endpoint-access"></a>공용 앱 엔드포인트 액세스

앱이 공용으로 구성되면 키에 전체 엔드포인트 할당량이 사용되지 않는 한 ‘모든’ 유효한 LUIS 작성 키 또는 LUIS 엔드포인트 키가 앱을 쿼리할 수 있습니다. 

소유자 또는 협력자가 아닌 사용자는 앱 ID가 제공된 경우에만 공용 앱에 액세스할 수 있습니다. LUIS에는 공용 앱을 검색할 수 있는 공개 ‘시장’이나 다른 방법이 없습니다.   

공용 앱은 모든 지역에 게시되므로 지역 기반 LUIS 리소스 키를 가진 사용자가 해당 리소스 키와 연결된 지역에서 앱에 액세스할 수 있습니다.

## <a name="microsoft-user-accounts"></a>Microsoft 사용자 계정

작성자 및 협력자는 게시 페이지에서 LUIS에 키를 추가할 수 있습니다. Azure Portal에서 LUIS 키를 만드는 Microsoft 사용자 계정은 앱 소유자 또는 앱 협력자여야 합니다. 

Active Directory 사용자 계정에 자세히 알아보려면 [Azure Active Directory 테넌트 사용자](luis-how-to-collaborate.md#azure-active-directory-tenant-user)를 참조하세요. 

## <a name="securing-the-endpoint"></a>엔드포인트 보안 

서버 간 환경에서 LUIS 엔드포인트 키를 호출하여 볼 수 있는 사용자를 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우, 봇과 LUIS 간의 연결은 이미 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [AAD](https://azure.microsoft.com/services/active-directory/))를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API가 호출되고 인증 및 권한 부여가 확인되면 LUIS에 해당 호출을 전달합니다. 이 전략은 가로채기 공격을 방지하지 않지만 사용자의 엔드포인트를 난독 처리하고, 액세스를 추적하도록 허용하고, 엔드포인트 응답 로깅(예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가하도록 허용합니다.  

## <a name="security-compliance"></a>보안 준수
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>다음 단계

최고의 예측을 위해 의도 및 엔터티를 사용하는 방법을 알아보려면 [모범 사례](luis-concept-best-practices.md)를 참조하세요.
