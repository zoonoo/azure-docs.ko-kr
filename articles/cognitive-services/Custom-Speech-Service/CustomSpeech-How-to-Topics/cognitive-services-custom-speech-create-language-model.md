---
title: Custom Speech Service를 사용한 언어 모델 만들기 자습서 - Microsoft Cognitive Services | Microsoft Docs
description: 이 자습서에서는 Microsoft Cognitive Services에서 Custom Speech Service를 사용하여 언어 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ms.openlocfilehash: 6af2da9ffc7678a58fcf1c647ba89c586066d2ad
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339099"
---
# <a name="tutorial-create-a-custom-language-model"></a>자습서: 사용자 지정 언어 모델 만들기

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

이 자습서에서는 텍스트 쿼리에 대한 사용자 지정 언어 모델 또는 사용자가 응용 프로그램에 입력 하거나 언급할 것으로 예상하는 발언을 만듭니다. 그런 다음, 응용 프로그램에 음성 상호 작용을 추가하려면 Microsoft에서 기존의 최신 음성 모델과 함께 이 사용자 지정 언어 모델을 사용할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 데이터 준비
> * 언어 데이터 집합 가져오기
> * 사용자 지정 언어 모델 만들기

Cognitive Services 계정이 아직 없는 경우 시작하기 전에 [무료 계정](https://cris.ai)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

[Cognitive Services 구독](https://cris.ai/Subscriptions) 페이지를 열어 Cognitive Services 계정이 구독에 연결되어 있는지 확인합니다.

구독이 목록에 없으면 **무료 구독 가져오기** 단추를 클릭하여 Cognitive Services가 사용자를 위한 계정을 만들게 할 수 있습니다. 또는 **기존 구독 연결** 단추를 클릭하여 Azure Portal에서 만든 사용자 지정 Search Service 구독에 연결할 수 있습니다.

Azure Portal에서 사용자 지정 Search Service 구독을 만드는 방법에 대한 정보는 [Azure Portal에서 Cognitive Services API 계정 만들기](../../cognitive-services-apis-create-account.md)를 참조합니다.

## <a name="prepare-the-data"></a>데이터 준비

응용 프로그램에 대한 사용자 지정 언어 모델을 만들려면 예를 들어 시스템에 발화 예제 목록을 제공해야 합니다.

*   "그는 지난 주 내내 두드러기가 났습니다."
*   "그 환자는 탈장 봉합 상처가 잘 아물었습니다."

문장은 완벽한 문장이거나 문법적으로 정확할 필요는 없으며 시스템이 배포 시 발생하리라 예상하는 음성 입력을 정확하게 반영해야 합니다. 이 예제에서는 사용자가 응용 프로그램으로 수행할 작업의 내용과 스타일 모두를 반영해야 합니다.

언어 모델 데이터는 로캘에 따라 US-ASCII 또는 UTF-8을 사용하여 일반 텍스트 파일로 작성되어야 합니다. en-US의 경우 인코딩 모두가 지원됩니다. zh-CN의 경우 UTF-8만 지원됩니다(BOM는 선택 사항). 텍스트 파일은 줄당 한 가지 예제(문장, 발언 또는 쿼리)를 포함해야 합니다.

일부 문장에 더 높은 가중치(중요도)가 있기를 바라는 경우 데이터에 여러 번 추가할 수 있습니다. 적당한 반복 횟수는 10회에서 100회 사이입니다. 100회를 정규화하는 경우 이와 관련된 문장에 쉽게 가중치를 줄 수 있습니다.

언어 데이터에 대한 기본 요구 사항이 다음 표에 요약되어 있습니다.

| 자산 | 값 |
|----------|-------|
| 텍스트 인코딩 | en-US: US-ACSII 또는 UTF-8 또는 zh-CN: UTF-8|
| 줄당 발언의 # | 1 |
| 최대 파일 크기 | 200MB |
| 설명 | 문자를 4번 이상, 예를 들어 'aaaaa', 반복하지 마십시오.|
| 설명 | [유니코드 문자 표](http://www.utf8-chartable.de/)에는 U+00A1을 넘는 기타 모든 UTF-8 문자 또는 '\t' 같은 특수 문자가 없음|
| 설명 | URI는 URI를 발음하는 고유의 방법이 있기 때문에 거부됩니다.|

텍스트를 가져오면 시스템에서 처리할 수 있도록 텍스트가 정규화됩니다. 그러나 데이터를 업로드하기 _전에_ 사용자가 수행해야 하는 몇몇 중요한 정규화 작업이 있습니다. 언어 데이터를 준비하는 경우 적절한 언어를 결정하려면 [기록 지침](cognitive-services-custom-speech-transcription-guidelines.md)을 참조합니다.

## <a name="import-the-language-data-set"></a>언어 데이터 집합 가져오기

"음향 데이터 세트" 행에서 "가져오기" 단추를 클릭하면 해당 사이트는 새 데이터 세트를 업로드하기 위한 페이지를 표시합니다.

언어 데이터 집합을 가져올 준비가 되면 [Custom Speech Service 포털](https://cris.ai)에 로그인합니다.  그럼 다음, 위쪽 리본에서 "사용자 지정 음성" 드롭 다운 메뉴를 클릭하고 "적응 데이터"를 선택합니다. Custom Speech Service에 처음으로 데이터를 업로드하는 경우 "데이터 세트"라는 빈 테이블이 표시됩니다.

새 데이터 세트를 가져오기 위해 "언어 데이터 세트" 행에서 "가져오기" 단추를 클릭하면 해당 사이트는 새 데이터 세트를 업로드하기 위한 페이지를 표시합니다. 향후에 데이터 집합을 확인할 수 있도록 이름 및 설명을 입력합니다. 다음으로, "파일 선택" 단추를 사용하여 언어 데이터의 텍스트 파일을 찾습니다. 그 후 "가져오기"를 클릭하면 데이터 집합이 업로드됩니다. 데이터 집합 크기에 따라 몇 분이 걸릴 수 있습니다.

![다음을 시도해 보세요.](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

가져오기가 완료되면 언어 데이터 테이블로 반환되고 언어 데이터 집합에 해당하는 항목이 표시됩니다. 고유 ID(GUID)가 할당되었음에 유의합니다. 데이터에는 또한 현재 상태를 반영하는 상태가 있습니다. 해당 상태는 처리를 위해 큐 대기하는 동안 "대기 상태"이며, 유효성 검사를 통과하는 동안 "처리 상태"이고, 데이터가 사용할 준비가 되면 "완료 상태"에 있게 됩니다. 데이터 유효성 검사는 데이터의 일부 텍스트 정규화 및 파일의 텍스트에 대한 일련의 검사를 수행합니다.

상태가 "완료"이면 "보고서 보기"를 클릭하여 언어 데이터 확인 보고서를 볼 수 있습니다. 확인을 통과하고 실패한 발언 수가 실패한 발언에 대한 세부 정보와 함께 표시됩니다. 아래 예제에서 두 가지 예제가 부적절한 문자 때문에 확인에 실패했습니다(이 데이터 집합에서 첫 번째 예제는 두 개의 이모티콘을 가졌고 두 번째 예제는 ASCII 인쇄 가능한 문자 집합 외의 여러 문자를 가졌습니다).

![다음을 시도해 보세요.](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

언어 데이터 집합의 상태가 "완료"이면 사용자 지정 언어 모델을 만드는 데 사용될 수 있습니다.

![다음을 시도해 보세요.](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>사용자 지정 언어 모델 만들기

언어 데이터가 준비되면 드롭 다운 “메뉴”에서 “언어 모델”을 클릭하여 사용자 지정 언어 모델 만들기 프로세스를 시작합니다. 이 페이지에는 현재 사용자 지정 언어 모델이 포함된 "Language Model"이라는 테이블이 포함됩니다. 어떤 사용자 지정 언어 모델도 아직 만들지 못한 경우 테이블은 비어있게 됩니다. 현재 로캘이 테이블 제목에 반영됩니다. 다른 언어에 대한 언어 모델을 만들려는 경우 "로캘 변경"을 클릭합니다. 지원되는 언어에 대한 추가 정보는 [로캘 변경](cognitive-services-custom-speech-change-locale.md) 섹션에서 확인할 수 있습니다. 새 모델을 만들려면 테이블 제목 아래의 "새로 만들기" 링크를 클릭합니다.

"Language Model 만들기" 페이지에 사용된 데이터 집합 같은 이 모델에 대한 관련 정보를 계속 추적할 수 있도록 "이름" 및 "설명"을 입력합니다. 다음으로, 드롭 다운 메뉴에서 "기본 Language Model"을 선택합니다. 이 모델은 사용자 지정에 대한 시작점이 됩니다. 선택할 두 가지 기본 언어 모델이 있습니다. _Microsoft 검색 및 받아쓰기 LM_은 명령, 검색 쿼리 또는 받아쓰기 같은 응용 프로그램의 지시 음성에 적합합니다. _Microsoft 대화형 LM_은 대화 스타일로 통용되는 음성을 인식하는 데 적합합니다. 이런 유형의 음성은 일반적으로 다른 사람을 대상으로 지시하며 콜센터나 회의에서 사용됩니다.

기본 언어 모델을 지정한 후 "언어 데이터" 드롭다운 메뉴를 사용하여 사용자 지정에 사용하려는 언어 데이터 집합을 선택합니다.

![다음을 시도해 보세요.](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

음향 모델 만들기와 마찬가지로 처리가 완료되면 새 모델의 오프라인 테스트 수행을 필요에 따라 선택할 수 있습니다. 음성-텍스트 성능 평가이므로 오프라인 테스트에는 음향 데이터 집합이 필요합니다.

언어 모델의 오프라인 테스트를 수행하려면 "오프라인 테스트" 옆에 있는 확인란을 선택합니다. 그런 다음, 드롭다운 메뉴에서 음향 모델을 선택합니다. 사용자 지정 음향 모델을 만들지 못한 경우 Microsoft 기본 음향 모델이 메뉴에서 유일한 모델이 됩니다. 대화형 LM 기본 모델을 선택한 경우 여기에서 대화형 AM를 사용해야 합니다. 검색 및 받아쓰기 LM 모델을 사용하는 경우 검색 및 받아쓰기 AM 모델을 선택해야 합니다.

마지막으로 평가 수행에 사용하려는 음향 데이터 집합을 선택합니다.

처리를 시작할 준비가 되면 "만들기"를 누릅니다. 이렇게 하면 언어 모델 테이블로 반환됩니다. 테이블에 이 모델에 해당하는 새 항목이 있게 됩니다. 상태는 모델의 상태를 반영하고 "대기", "처리" 및 "완료" 등 여러 상태를 거치게 됩니다.

모델이 "완료" 상태에 도달하면 모델을 엔드포인트에 배포할 수 있습니다. "보기 결과"를 클릭하면 수행된 경우 오프라인 테스트 결과가 표시됩니다.

특정 시점에 모델의 “이름” 또는 “설명”을 변경하려면 언어 모델 테이블의 해당 행에 있는 "편집" 링크를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 텍스트와 함께 사용할 사용자 지정 언어 모델을 개발했습니다. 오디오 파일 및 전사와 함께 사용할 사용자 지정 음향 모델을 만들려면 음향 모델을 만드는 방법에 대한 자습서를 계속 사용합니다.

> [!div class="nextstepaction"]
> [사용자 지정 음향 모델 만들기](cognitive-services-custom-speech-create-acoustic-model.md)
