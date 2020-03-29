---
title: 모범 사례 - QnA Maker
description: 모범 사례를 사용하여 기술 자료를 개선하고 애플리케이션/챗봇 최종 사용자에게 보다 나은 결과를 제공할 수 있습니다.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053126"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료의 모범 사례

[기술 자료 개발 수명 주기](../Concepts/development-lifecycle-knowledge-base.md)에서는 KB를 종합적으로 관리하는 방법을 안내합니다. 이러한 모범 사례를 사용하여 기술 기반을 개선하고 클라이언트 응용 프로그램 또는 챗봇의 최종 사용자에게 더 나은 결과를 제공할 수 있습니다.

## <a name="extraction"></a>추출

QnA Maker 서비스는 콘텐츠에서 QnA를 추출하고 지원되는 파일 및 HTML 형식 목록을 확장하는 알고리즘을 지속적으로 개선하고 있습니다. 문서 형식에 따라 데이터 추출에 대한 [지침](../Concepts/content-types.md)을 수행합니다.

일반적으로 FAQ 페이지는 독립 실행형이어야 하며, 다른 정보와 결합되지 않아야 합니다. 제품 설명서는 제목에 명확해야 하고 인덱스 페이지까지 명확하면 더욱 좋습니다.

### <a name="configuring-multi-turn"></a>멀티 턴 구성

멀티 턴 추출을 사용하도록 설정하여 [기술 기반을 만듭니다.](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) 기술 자료가 질문 계층 구조를 수행하거나 지원해야 하는 경우 이 계층을 문서에서 추출하거나 문서를 추출한 후 만들 수 있습니다.

## <a name="creating-good-questions-and-answers"></a>좋은 질문 및 답변 만들기

### <a name="good-questions"></a>좋은 질문

가장 좋은 질문은 간단합니다. 각 질문의 키워드 또는 문구를 고려한 다음, 해당 키워드 또는 문구에 대한 간단한 질문을 만듭니다.

필요한 개수만큼 대체 질문을 추가하되, 간단한 변경으로 유지합니다. 질문의 주요 목표에 속하지 않는 단어나 문구를 더 추가하는 것은 QnA Maker에서 일치 항목을 찾는 데 도움이 되지 않습니다.


### <a name="add-relevant-alternative-questions"></a>관련 대체 질문 추가

사용자는 대화 식 텍스트 스타일 `How do I add a toner cartridge to my printer?` 또는 와 같은 `toner cartridge`키워드 검색을 사용하여 질문을 입력할 수 있습니다. 기술 자료에는 최상의 답변을 올바르게 반환하려면 두 가지 스타일의 질문이 있어야 합니다. 고객이 입력하는 키워드가 확실하지 않은 경우 Application Insights 데이터를 사용하여 쿼리를 분석합니다.

### <a name="good-answers"></a>좋은 답변

가장 좋은 대답은 간단한 대답이지만 너무 간단하지는 않습니다. `yes` 와 `no`같은 답변을 사용하지 마십시오. 답변이 다른 소스에 연결되거나 미디어 및 링크에 대한 풍부한 경험을 제공해야 하는 경우 [메타데이터 태그를](../how-to/edit-knowledge-base.md#add-metadata) 사용하여 `strictFilters` 답변을 구분한 다음 속성에 메타데이터 태그가 있는 [쿼리를 제출하여](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) 정답 버전을 가져옵니다.

|Answer|폴업 프롬프트|
|--|--|
|키보드의 전원 버튼으로 Surface 노트북의 전원을 들어 오세요.|* 키 조합은 절전, 종료 및 다시 시작합니다.<br>* 표면 노트북을 하드 부팅하는 방법<br>* 표면 노트북의 BIOS를 변경하는 방법<br>* 수면, 종료 및 다시 시작 의 차이|
|고객 서비스는 24시간 전화, Skype 및 문자 메시지를 통해 제공됩니다.|※ 판매 연락처 정보입니다.<br> ※ 사무실 및 매장 위치 및 직접 방문 에 대 한 시간.<br> * 표면 노트북 액세서리.|

## <a name="chit-chat"></a>잠답
봇에 잡담을 추가하여 적은 노력으로 봇을 대화형이고 몰입도 높게 만듭니다. KB를 만들 때 미리 정의된 성격의 chit-chat 데이터 세트를 쉽게 추가하고 언제든지 변경할 수 있습니다. [KB에 잡담을 추가](../How-To/chit-chat-knowledge-base.md)하는 방법을 알아봅니다.

Chit-chat은 [여러 언어로 지원됩니다.](../how-to/chit-chat-knowledge-base.md#language-support)

### <a name="choosing-a-personality"></a>개성 선택
Chit-chat은 미리 정의된 여러 성격에 대해 지원됩니다.

|성격 |QnA 메이커 데이터 집합 파일 |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|친숙한 |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|재치 |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|돌보는 |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|열정 |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

응답은 격식체에서 비격식체의 범위까지 지정됩니다. 봇에 원하는 어조와 가장 가깝게 맞춰진 개성을 선택해야 합니다. 데이터 집합을 보고 봇의 기본 역할을 하는 [데이터 집합을](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)선택한 다음 응답을 사용자 지정할 수 있습니다.

### <a name="edit-bot-specific-questions"></a>봇 관련 질문 편집
잠답 데이터 집합의 일부이며 일반 답변으로 채워진 몇 가지 봇 관련 질문이 있습니다. 이 대답을 변경하여 봇 세부 정보를 가장 잘 반영합니다.

잡담 QnA를 구체적으로 만드는 것이 좋습니다.

* 귀하는 누구인가요?
* 무엇을 할 수 있나요?
* 나이가 어떻게 되세요?
* 만든 사람은 누구인가요?
* 안녕하세요.

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>메타데이터 태그로 사용자 지정 치트 채팅 추가

자신의 chit-chat QnA 쌍을 추가하는 경우 이러한 답변이 반환되도록 메타데이터를 추가해야 합니다. 메타데이터 이름/값 `editorial:chitchat`쌍은 .

## <a name="searching-for-answers"></a>답변 검색

GenerateAnswer API는 질문과 답변을 모두 사용하여 사용자의 쿼리에 대한 최상의 답변을 검색합니다.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>답변이 관련이 없는 경우에만 질문 검색

[`RankerType=QuestionOnly`](#choosing-ranker-type) 답변을 검색하지 않으려면 을 사용합니다.

예를 들어 기술 자료가 전체 양식의 질문으로 약어 카탈로그를 답변으로 하는 경우입니다. 대답의 값은 적절한 답을 찾는 데 도움이되지 않습니다.

## <a name="rankingscoring"></a>순위 지정/채점
QnA Maker가 지원하는 순위 기능을 최대로 활용하시기 바랍니다. 그러면 특정 사용자 쿼리에 적절하게 답변할 가능성이 높아집니다.

### <a name="choosing-a-threshold"></a>임계값 선택

임계값으로 사용되는 기본 [신뢰도 점수는](confidence-score.md) 0이지만 필요에 따라 [KB의 임계값을 변경할](confidence-score.md#set-threshold) 수 있습니다. 모든 KB가 다르므로 KB에 가장 적합한 임계값을 에 테스트하고 선택해야 합니다.

### <a name="choosing-ranker-type"></a>랭커 유형 선택
기본적으로 QnA Maker는 질문과 답변을 검색합니다. 질문을 통해서만 검색하려면 답변을 생성하려면 생성응답 요청의 POST `RankerType=QuestionOnly` 본문에서 를 사용합니다.

### <a name="add-alternate-questions"></a>대체 질문 추가
[대체 질문](../How-To/edit-knowledge-base.md)은 사용자 쿼리와 일치할 가능성을 높입니다. 대체 질문은 같은 질문을 여러 가지 방식으로 받을 수 있는 경우에 유용합니다. 여기에는 문장 구조 및 단어 스타일 변경이 포함됩니다.

|원래 쿼리|대체 쿼리|변경|
|--|--|--|
|주차 가능하나요?|주차장이 있나요?|문장 구조|
 |Hi|Yo<br>Hey there!|단어 스타일 또는 속어|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>메타데이터 태그를 사용하여 질문과 답변 필터링

[메타데이터는](../How-To/edit-knowledge-base.md) 클라이언트 응용 프로그램이 모든 답변을 가져가지 않고 메타데이터 태그를 기반으로 사용자 쿼리의 결과를 좁히는 기능을 추가합니다. 쿼리가 같더라도 메타데이터 태그에 따라 기술 자료 답변이 달라질 수 있습니다. 예를 들어 식당 지점의 위치가 다르면 *"주차장 위치는 어디인가요"* 에 대한 대답이 달라질 수 있습니다. 즉, 메타데이터는 *위치: 시애틀* 또는 *위치: 레드몬드*입니다.

### <a name="use-synonyms"></a>동의어 사용
영어의 동의어에 대한 지원이 있지만 [Alterations API를](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) 통해 대/소문자를 구분하지 않는 단어 변경을 사용하여 다른 형태를 취하는 키워드에 동의어를 추가합니다. 동의어는 QnA Maker 서비스 수준에 추가되고 서비스의 모든 기술 기반에서 공유됩니다.

|원래 단어|동의어|
|--|--|
|구입|구매<br>넷 뱅킹<br>인터넷 뱅킹|

### <a name="use-distinct-words-to-differentiate-questions"></a>고유한 단어를 사용하여 질문을 구분
사용자 쿼리를 기술 자료의 질문과 일치시키는 QnA Maker 순위 알고리즘은 각 질문이 다른 요구 사항을 다루는 경우에 가장 효과적입니다. 질문 사이에 같은 단어 집합을 반복하면 해당 단어가 포함된 특정 사용자 쿼리에 대해 올바른 대답이 선택될 확률이 감소합니다.

예를 들어, 다음과 같은 질문을 사용하여 두 개의 별도 QnA가 있을 수 있습니다.

|QnA|
|--|
|주차 *위치*는 어디인가요|
|ATM *위치는* 어디입니까?|

이러한 두 QnA가 매우 유사한 단어로 구성되었으므로 이 유사성은 *"`<x>`위치는 어디인가요"* 와 같이 구성된 사용자 쿼리에 대해 유사한 점수가 발생할 수 있습니다. 대신 KB에서 많은 질문에 있을 수 있는 "위치"와 같은 단어를 피하여 *"주차장이 있는 위치"와* *"ATM은 어디에 있는지"와*같은 쿼리와 명확하게 구분해 보십시오.

## <a name="collaborate"></a>공동 작업
QnA Maker를 통해 사용자들이 기술 자료를 [공동으로 작업](../How-to/collaborate-knowledge-base.md)할 수 있습니다. 사용자는 기술 자료에 액세스하기 위해 Azure QnA Maker 리소스 그룹에 대한 액세스 권한이 필요합니다. 기술 자료 편집 및 유지 관리를 아웃소싱하려는 조직도 있을 것이며, 이 경우에도 여전히 Azure 리소스에 대한 액세스를 보호할 수 있습니다. 이 편집자-승인자 모델은 서로 다른 구독에 최대 2개의 동일한 [QnA Maker 서비스](../How-to/set-up-qnamaker-service-azure.md)를 설정하고 하나를 편집-테스트 주기용으로 지정하여 수행할 수 있습니다. 테스트가 완료되면 [가져오기-내보내기](../Tutorials/migrate-knowledge-base.md) 프로세스를 사용하여 최종적으로 기술 자료를 게시하고 엔드포인트를 업데이트하는 승인자의 QnA Maker 서비스로 기술 자료 콘텐츠를 전송할 수 있습니다.



## <a name="active-learning"></a>능동적 학습

[활성 학습](../How-to/use-active-learning.md)은 품질의 범위가 넓은 사용자 기반 쿼리의 수가 많을 때 가장 적절한 대체 질문을 제안합니다. 그러므로 클라이언트 애플리케이션의 사용자 쿼리가 검열되지 않고 활성 학습 피드백 루프에 참여할 수 있도록 해야 합니다. QnA Maker 포털에서 질문이 제안되면 **[제안으로 필터링한](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** 다음 해당 제안을 검토하고 수락하거나 거부할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-to/edit-knowledge-base.md)
