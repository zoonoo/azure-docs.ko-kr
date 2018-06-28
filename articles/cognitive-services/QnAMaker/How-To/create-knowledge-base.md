---
title: 기술 자료를 만드는 방법 - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: 기술 자료를 만드는 방법
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35383159"
---
# <a name="create-a-knowledge-base"></a>기술 자료 만들기

QnA Maker를 사용하면 매우 간단하게 기존 데이터 원본을 온보드하여 기술 자료를 만들 수 있습니다. FAQ 페이지, 제품 설명서, 구조화된 문서로 새 QnA Maker 기술 자료를 만들거나 편집자로서 추가할 수 있습니다.

## <a name="steps"></a>단계

1. 시작하려면 azure 자격 증명을 사용하여 [QnA Maker 포털](https://qnamaker.ai)에 로그인하고 **새 서비스 만들기**를 클릭합니다.

    ![기술 자료 만들기 ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. 아직 QnA Maker 서비스를 만들지 않은 경우 **QnA 서비스 만들기**를 선택합니다. 이미 만들었으면 2단계의 드롭다운 목록에서 QnA Maker 서비스를 선택합니다. 기술 자료를 호스트할 QnA Maker 서비스를 선택합니다.

    ![QnA 서비스 설정](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. 기술 자료를 만들려면 다음 정보를 입력해야 합니다.

    ![데이터 원본 설정](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 서비스의 **이름**을 지정합니다. 중복되는 이름과 특수 문자도 지원됩니다.
    - 추출할 URL을 붙여넣습니다. 지원되는 원본 형식에 대한 자세한 내용은 [여기](../Concepts/data-sources-supported.md)서 확인할 수 있습니다.
    - 또는 데이터가 추출된 파일을 업로드합니다. 추가할 수 있는 문서 수는 [가격 책정 정보](https://aka.ms/qnamaker-pricing
)를 참조하세요.
    - 수동으로 QnA를 추가하려는 경우 파일 연결을 건너뛰어도 됩니다.

4. **만들기**를 선택합니다.

    ![기술 자료 만들기](../media/qnamaker-how-to-create-kb/create-kb.png)

5. 데이터를 추출할 때까지 몇 분 정도 걸립니다.

    ![추출](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. 기술 자료가 성공적으로 만들어지면 **기술 자료** 페이지로 리디렉션됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 가져오기](../Tutorials/migrate-knowledge-base.md)
