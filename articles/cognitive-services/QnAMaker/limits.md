---
title: 한도 및 경계 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker에는 기술 자료 및 서비스 부분에 대해 제한이 있습니다. 테스트 및 게시하기 위해서는 기술 자료를 그러한 제한 내로 유지하는 것이 중요합니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/22/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: ce6c5f3059041d8dbb097470cf4a415e73d9156b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237263"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 기술 자료 제한 및 경계
QnA Maker에 대한 포괄적인 제한 목록입니다.

## <a name="knowledge-bases"></a>기술 자료

* [Azure Search 계층 제한](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)을 기반으로 하는 최대 기술 자료 수

|**Azure Search 계층** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|게시할 수 있는 기술 자료의 최대 수|2|14|49|199|199|2,999|

 예를 들어 계층에 허용되는 인덱스가 15개 있으면 기술 자료 14개를 게시할 수 있습니다(게시되는 기술 자료당 인덱스 1개). 15번째 인덱스(`testkb`)는 모든 기술 자료에서 작성 및 테스트용으로 사용됩니다. 

## <a name="extraction-limits"></a>추출 제한
* 추출할 수 있는 최대 파일 수 및 최대 파일 크기: [QnAMaker 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/) 참조
* FAQ HTML 페이지에서 QnA 추출을 위해 크롤링할 수 있는 최대 딥 링크 수: 20

## <a name="metadata-limits"></a>메타데이터 제한
* [Azure Search 계층 제한](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)을 기반으로 하는 기술 자료별 최대 메타데이터 필드 수

|**Azure Search 계층** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|QnA Maker 서비스별 최대 메타데이터 필드 수(모든 기술 자료에서)|1,000|100*|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>기술 자료 콘텐츠 제한
기술 자료에서 전반적인 콘텐츠 제한 사항은 아래와 같습니다.
* 답변 텍스트 길이: 25,000
* 질문 텍스트 길이: 1,000
* 메타데이터 키/값 텍스트 길이: 100
* 메타데이터 이름에 지원되는 문자: 알파벳, 숫자 및 _  
* 메타데이터 값에 지원되는 문자: : 및 | 외 모든 문자 
* 파일 이름 길이: 200
* 지원되는 파일 형식: “.tsv”, “.pdf”, “.txt”, “.docx”, “.xlsx”
* 최대 대체 질문 수: 300
* 질문-응답 쌍의 최대 수: 종속 된 [Azure Search 계층](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) 선택 합니다. 질문 및 답변 쌍을 Azure Search 인덱스에서 문서에 매핑됩니다. 

## <a name="create-knowledge-base-call-limits"></a>기술 자료 호출 제한 만들기
이는 각 기술 자료 만들기 작업(‘기술 자료 만들기’ 클릭 또는 CreateKnowledgeBase API 호출)에 대한 제한을 나타냅니다. 
* 답변별 최대 대체 질문 수: 300
* 최대 URL 수: 10
* 최대 파일 수: 10

## <a name="update-knowledge-base-call-limits"></a>기술 자료 호출 제한 업데이트
이는 각 업데이트 작업(‘저장 및 학습’ 클릭 또는 UpdateKnowledgeBase API 호출)에 대한 제한을 나타냅니다. 
* 각 원본 이름의 길이: 300
* 추가 또는 삭제된 최대 대체 질문 수: 300
* 추가 또는 삭제된 최대 메타데이터 필드 수: 10
* 새로 고칠 수 있는 최대 URL 수: 5

## <a name="next-steps"></a>다음 단계

서비스 계층을 변경해야 하는 시기와 변경 방법을 알아봅니다.

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): 때 소스 파일이 더 있는 해야 하거나 더 큰 문서에 현재 계층을 벗어난 여 기술 자료의 가격 책정 계층 QnA Maker 서비스를 업그레이드 합니다.
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): 기술 자료에서 클라이언트 앱에서 더 많은 요청을 처리해야 하는 경우 앱 서비스 가격 책정 계층을 업그레이드하세요.
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): 많은 기술 자료를 포함할 계획인 경우 Azure Search 서비스 가격 책정 계층을 업그레이드하세요.
