---
title: 서비스 구성 - QnA 메이커
description: 리소스를 구성하는 방법과 위치를 이해합니다.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804263"
---
# <a name="service-configuration"></a>서비스 구성

QnA Maker는 인지 검색, 앱 서비스, 앱 서비스 계획 및 응용 프로그램 통찰력을 비롯한 여러 Azure 리소스(서비스)를 사용합니다.

QnA 메이커가 지원하는 이러한 설정에 대한 모든 사용자 정의는 다음과 같습니다.

## <a name="app-service"></a>App Service

QnA Maker는 앱 서비스를 사용하여 [generateAnswer API에서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)사용하는 쿼리 런타임을 제공합니다.


이러한 설정은 앱 서비스에 대한 Azure 포털에서 사용할 수 있습니다. 설정 **,** **다음 구성을**선택하여 설정을 사용할 수 있습니다.

응용 프로그램 설정 목록을 통해 개별 설정을 설정하거나 고급 **편집을**선택하여 여러 설정을 수정할 수 있습니다.

|리소스|설정|
|--|--|
|AzureSearchAdminKey|인지 검색 - QnA 쌍 저장 및 랭커 #1 사용|
|Azure SearchName|인지 검색 - QnA 쌍 저장 및 랭커 #1 사용|
|기본 답변|일치하는 일치를 찾을 수 없는 경우 의 정답 텍스트|
|사용자 앱 인사이트앱Id|채팅 로그 및 원격 분석|
|사용자 앱 인사이트키|채팅 로그 및 원격 분석|
|사용자 앱 인사이트이름|채팅 로그 및 원격 분석|

[코그너티브 검색 서비스를 서비스에 변경하는 방법을](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) 알아보세요.

변경이 완료되면 Azure 포털의 **개요** 페이지에서 서비스를 **다시 시작해야** 합니다.

## <a name="qna-maker-service"></a>QnA Maker Service

QnA Maker 서비스는 다음 사용자가 단일 QnA Maker 서비스와 모든 기술 기반에서 공동 작업할 수 있도록 구성을 제공합니다.

서비스에 [공동 작업자를 추가하는 방법을](./how-to/collaborate-knowledge-base.md) 알아봅니다.

## <a name="application-insights"></a>Application Insights

응용 프로그램 인사이트는 QnA 메이커에 특정 구성 설정이 없습니다.

## <a name="app-service-plan"></a>App Service 계획

앱 서비스 계획에는 QnA Maker와 관련된 구성 설정이 없습니다.

## <a name="next-steps"></a>다음 단계

기술 자료로 가져올 문서 및 URL의 [형식에](reference-document-format-guidelines.md) 대해 자세히 알아보세요.