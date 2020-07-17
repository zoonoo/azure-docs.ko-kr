---
title: 서비스 구성-QnA Maker
description: 리소스를 구성 하는 방법 및 위치를 이해 합니다.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804263"
---
# <a name="service-configuration"></a>서비스 구성

QnA Maker는 Cognitive Search, App Service, App Service 계획 및 Application Insights를 비롯 한 여러 Azure 리소스 (서비스)를 사용 합니다.

QnA Maker에서 지 원하는 이러한 설정에 대 한 모든 사용자 지정은 아래에 나열 되어 있습니다.

## <a name="app-service"></a>App Service

QnA Maker는 App Service를 사용 하 여 [Generateanswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)에서 사용 하는 쿼리 런타임을 제공 합니다.


이러한 설정은 App Service에 대 한 Azure Portal에서 사용할 수 있습니다. 설정은 **설정**, **구성**을 차례로 선택 하 여 사용할 수 있습니다.

개별 설정은 응용 프로그램 설정 목록을 통해 설정 하거나 **고급 편집**을 선택 하 여 여러 설정을 수정할 수 있습니다.

|리소스|Setting|
|--|--|
|AzureSearchAdminKey|Cognitive Search-QnA pair 저장소 및 Ranker #1에 사용 됩니다.|
|AzureSearchName|Cognitive Search-QnA pair 저장소 및 Ranker #1에 사용 됩니다.|
|DefaultAnswer|일치 항목을 찾을 수 없는 경우의 대답 텍스트|
|UserAppInsightsAppId|채팅 로그 및 원격 분석|
|UserAppInsightsKey|채팅 로그 및 원격 분석|
|UserAppInsightsName|채팅 로그 및 원격 분석|

Cognitive Search 서비스를 서비스에 [추가 하는 방법](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) 에 대해 알아봅니다.

변경을 완료 한 후에는 Azure Portal의 **개요** 페이지에서 서비스를 **다시 시작** 해야 합니다.

## <a name="qna-maker-service"></a>QnA Maker Service

QnA Maker 서비스는 단일 QnA Maker 서비스와 모든 해당 기술 자료에서 공동 작업할 수 있도록 다음 사용자에 대 한 구성을 제공 합니다.

서비스에 [협력자를 추가 하는 방법을](./how-to/collaborate-knowledge-base.md) 알아봅니다.

## <a name="application-insights"></a>Application Insights

Application Insights에 QnA Maker 관련 된 구성 설정이 없습니다.

## <a name="app-service-plan"></a>App Service 계획

App Service 계획에는 QnA Maker 관련 된 구성 설정이 없습니다.

## <a name="next-steps"></a>다음 단계

기술 자료로 가져올 문서와 Url에 대 한 [형식](reference-document-format-guidelines.md) 에 대해 자세히 알아보세요.