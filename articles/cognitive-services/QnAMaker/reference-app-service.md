---
title: 서비스 구성-QnA Maker
description: 리소스를 구성 하는 방법 및 위치를 이해 합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: d2095919d6f6be482390250fd8d0ee20e9015237
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444863"
---
# <a name="service-configuration"></a>서비스 구성

각 버전의 QnA Maker는 서로 다른 Azure 리소스 (서비스) 집합을 사용 합니다. 이 문서에서는 이러한 서비스에 대해 지원 되는 사용자 지정을 설명 합니다. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

QnA Maker는 App Service를 사용 하 여 [Generateanswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer)에서 사용 하는 쿼리 런타임을 제공 합니다.

이러한 설정은 App Service에 대 한 Azure Portal에서 사용할 수 있습니다. 설정은 **설정** , **구성** 을 차례로 선택 하 여 사용할 수 있습니다.

개별 설정은 응용 프로그램 설정 목록을 통해 설정 하거나 **고급 편집** 을 선택 하 여 여러 설정을 수정할 수 있습니다.

|리소스|설정|
|--|--|
|AzureSearchAdminKey|Cognitive Search-QnA pair 저장소 및 Ranker #1에 사용 됩니다.|
|AzureSearchName|Cognitive Search-QnA pair 저장소 및 Ranker #1에 사용 됩니다.|
|DefaultAnswer|일치 항목을 찾을 수 없는 경우의 대답 텍스트|
|UserAppInsightsAppId|채팅 로그 및 원격 분석|
|UserAppInsightsKey|채팅 로그 및 원격 분석|
|UserAppInsightsName|채팅 로그 및 원격 분석|

변경을 완료 한 후에는 Azure Portal의 **개요** 페이지에서 서비스를 **다시 시작** 해야 합니다.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

App Service 사용자 지정은 QnA Maker 관리 (미리 보기)에 적용 되지 않습니다.

---

## <a name="qna-maker-service"></a>QnA Maker Service

QnA Maker 서비스는 단일 QnA Maker 서비스와 모든 해당 기술 자료에서 공동 작업할 수 있도록 다음 사용자에 대 한 구성을 제공 합니다.

서비스에 [협력자를 추가 하는 방법을](./how-to/collaborate-knowledge-base.md) 알아봅니다.

## <a name="change-azure-cognitive-search"></a>Azure Cognitive Search 변경

QnA Maker 서비스에 연결 된 [Cognitive Search 서비스를 변경 하는 방법](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) 에 대해 알아봅니다.

## <a name="change-default-answer"></a>기본 답변 변경

[기본 답변의 텍스트를 변경 하는 방법](How-To/change-default-answer.md)에 대해 알아봅니다. 

## <a name="telemetry"></a>원격 분석

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

Application Insights는 QnA Maker GA를 사용 하 여 원격 분석을 모니터링 하는 데 사용 됩니다. QnA Maker와 관련 된 구성 설정은 없습니다.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

[QnA Maker 관리 (미리 보기) 서비스에 원격 분석을 추가 하는 방법](How-To/get-analytics-knowledge-base.md)에 대해 알아봅니다. 

---

## <a name="app-service-plan"></a>App Service 플랜

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (안정적인 릴리스)](#tab/v1)

App Service 계획에는 QnA Maker 관련 된 구성 설정이 없습니다.

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker 관리 (미리 보기 릴리스)](#tab/v2)

App Service 계획은 QnA Maker 관리 (미리 보기)와 함께 사용 되지 않습니다.

---

## <a name="next-steps"></a>다음 단계

기술 자료로 가져올 문서와 Url에 대 한 [형식](reference-document-format-guidelines.md) 에 대해 자세히 알아보세요.
