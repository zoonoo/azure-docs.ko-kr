---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85114549"
---

## <a name="using-this-example-knowledge-base"></a>이 예제 기술 자료 사용

이 빠른 시작의 기술 자료는 2개의 대화형 QnA 쌍으로 시작합니다. 이는 후속 프롬프트를 새로운 쌍에 연결하여 예제를 단순화하고 업데이트 메서드에 사용할 매우 예측 가능한 ID를 갖기 위해 의도적으로 수행됩니다. 이 빠른 시작에 대해 특정 순서로 계획 및 구현되었습니다.

시간이 지남에 따라 기존 QnA 쌍에 종속된 추가 작업 프롬프트를 통해 기술 자료를 개발하려는 경우 다음을 선택할 수 있습니다.
* 더 큰 기술 자료의 경우 자동화를 지원하는 텍스트 편집기나 TSV 도구에서 기술 자료를 관리한 다음, 기술 자료를 한 번에 업데이트로 완전히 대체합니다.
* 더 작은 기술 자료의 경우 QnA Maker 포털에서 후속 프롬프트를 완전히 관리합니다.

이 빠른 시작에 사용되는 QnA 쌍에 대한 세부 정보:
* QnA 쌍 유형 - 이 기술 자료에는 업데이트 후 chitchat 및 도메인별 정보의 2가지 유형의 QnA 쌍이 있습니다. 이는 기술 자료가 챗봇과 같은 대화 애플리케이션에 연결된 경우에 일반적입니다.
* 기술 자료 답변은 메타데이터로 필터링되거나 후속 프롬프트를 사용할 수 있지만, 이 빠른 시작에서는 이를 보여주지 않습니다. [여기](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)에서 언어 제한이 없는 generateAnswer 예제를 찾아보세요.
* 대답 텍스트는 markdown이고 이미지(공개적으로 사용할 수 있는 인터넷 기반 이미지), 링크(공개적으로 사용할 수 있는 URL에 대한 링크) 및 글머리 기호 지점과 같이 [다양한 markdown](../reference-markdown-format.md)을 포함할 수 있습니다. 이 빠른 시작에서는 해당 다양성을 사용하지 않습니다.
