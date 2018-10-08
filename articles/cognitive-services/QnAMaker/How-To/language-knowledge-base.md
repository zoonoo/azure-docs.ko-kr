---
title: 영어 이외의 기술 자료 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 여러 언어로 기술 자료 콘텐츠를 지원합니다. 그러나 각 QnA Maker 서비스는 단일 언어에 예약되어야 합니다. 특정 QnA Maker 서비스를 대상으로 지정한 첫 번째 기술 자료는 해당 서비스의 언어를 설정합니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: b983fb21e8e67a422b6757619d1d0dfe8b6b9dcc
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033909"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA 작성기에 대한 기술 콘텐츠의 언어 지원
QnA Maker는 여러 언어로 기술 자료 콘텐츠를 지원합니다. 그러나 각 QnA Maker 서비스는 단일 언어에 예약되어야 합니다. 특정 QnA Maker 서비스를 대상으로 지정한 첫 번째 기술 자료는 해당 서비스의 언어를 설정합니다. 지원되는 언어 전체 목록은 [여기](../Overview/languages-supported.md)를 참조하세요.

언어는 압축을 풀 데이터 원본의 콘텐츠에서 자동으로 인식됩니다. 해당 서비스에서 새 QnA Maker 서비스와 새 기술 자료를 만들면 언어가 올바르게 설정되어 있는지 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. **리소스 그룹**을 선택하고 QnA Maker 서비스를 배포할 리소스 그룹으로 이동하고 **Azure Search** 리소스를 선택합니다.

    ![Azure Search 리소스 선택](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. **testkb** 인덱스를 선택합니다. 이 Azure Search 인덱스는 항상 첫 번째로 만들어지고 해당 서비스에서 모든 기술 자료의 저장된 콘텐츠를 포함합니다. 

    ![테스트 KB 선택](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. testkb 세부 정보를 보여주는 **필드** 섹션을 선택합니다.

    ![필드 선택](../media/qnamaker-how-to-language-kb/selectfields.png)

5. **분석기**에 대한 확인란을 선택하고 언어 정보를 확인합니다.

    ![분석기 선택](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. 특정 언어로 분석기를 설정했음을 알아야 합니다. 이 언어는 기술 자료 생성 단계에서 자동으로 검색되었습니다. 리소스가 만들어지면 이 언어를 변경할 수 없습니다.

    ![선택한 분석기](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Bot Service로 QnA 봇 만들기](../Tutorials/create-qna-bot.md)
