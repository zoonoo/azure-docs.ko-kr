---
title: 문서를 업로드하는 방법 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 문서 업로드 기능을 사용하여 훈련에 사용할 병렬 문서를 업로드할 수 있습니다. 병렬 문서는 서로가 서로의 번역인 문서 쌍입니다. 이 쌍에 포함된 하나의 문서에는 소스 언어의 문장이 포함되고, 다른 문서에는 이러한 문장을 대상 언어로 번역한 문장이 포함됩니다.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 1fa786bee960f71e4109041d935757a0d1edd75e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386941"
---
# <a name="upload-a-document"></a>문서 업로드

[Custom Translator](https://portal.customtranslator.azure.ai)에서 번역 모델을 훈련하는 데 사용할 병렬 문서를 업로드할 수 있습니다. [병렬 문서](what-are-parallel-documents.md)는 서로가 서로의 번역인 문서 쌍입니다. 이 쌍에 포함된 하나의 문서에는 소스 언어의 문장이 포함되고, 다른 문서에는 이러한 문장을 대상 언어로 번역한 문장이 포함됩니다.

문서를 업로드하기 전에 [문서 형식 및 명명 규칙 지침](document-formats-naming-convention.md)을 검토하여 Custom Translator에서 파일 형식이 지원되는지 확인하세요.

## <a name="how-to-upload-document"></a>문서를 업로드하는 방법

[Custom Translator](https://portal.customtranslator.azure.ai) 포털에서 “문서” 탭을 클릭하여 문서 페이지로 이동합니다.

![문서 업로드 링크](media/how-to/how-to-upload-1.png)


1.  문서 페이지에서 [파일 업로드] 단추를 클릭합니다.

    ![문서 업로드 페이지](media/how-to/how-to-upload-2.png)

2.  대화 상자에 다음 정보를 입력합니다.

    a.  문서 유형:

    -  학습: 학습 세트로 사용할 문서입니다.
    -  튜닝: 튜닝 세트로 사용할 문서입니다.
    -  테스트: 테스트 세트로 사용할 문서입니다.
    -  구 사전: 구 사전으로 사용할 문서입니다.
    -  문장 사전: 문장 사전으로 사용할 문서입니다.

    b.  언어 쌍

    다.  기존 문서 재정의: 이름이 같은 기존 문서를 덮어쓰려면 이 확인란을 선택합니다.

    d.  병렬 데이터와 콤보 데이터 중 하나를 선택하여 입력합니다.

    -  병렬 데이터:
        -  원본 파일: 로컬 컴퓨터에서 원본 언어 파일을 선택합니다.
        -  대상 파일: 로컬 컴퓨터에서 대상 언어 파일을 선택합니다.
        -  문서 이름: 병렬 파일을 업로드할 때만 사용합니다.

    - 콤보 데이터:
        -  콤보 파일: 로컬 컴퓨터에서 콤보 파일을 선택합니다. 콤보 파일에는 원본 언어 문장과 대상 언어 문장이 모두 들어 있습니다. 콤보 파일에서는 [명명 규칙](document-formats-naming-convention.md)이 중요합니다.

    e.  [업로드] 클릭

    ![문서 업로드 대화 상자](media/how-to/how-to-upload-dialog.png)

3.  이 시점에서는 문서를 처리하고 문장 추출을 시도합니다. “업로드 진행 과정 보기”를 클릭하여 문서가 처리되는 동안 상태를 확인할 수 있습니다.

    ![문서 업로드 처리 중 대화 상자](media/how-to/how-to-upload-processing-dialog.png)

4.  이 페이지에는 업로드에 포함된 각 파일의 상태와 오류(있는 경우)가 표시됩니다. “업로드 기록” 탭을 클릭하여 언제든지 지난 업로드 상태를 볼 수 있습니다.

    ![문서 업로드 기록 대화 상자](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>업로드 기록 보기

[업로드 기록] 페이지에서 문서 유형, 언어 쌍, 업로드 상태 등 모든 문서 업로드의 기록을 볼 수 있습니다.

1. [Custom Translator](https://portal.customtranslator.azure.ai) 포털에서 [업로드 기록] 탭을 클릭하여 기록을 봅니다.

    ![업로드 기록 탭](media/how-to/how-to-upload-history-1.png)

2. 이 페이지는 모든 지난 업로드의 상태를 보여 줍니다. 업로드가 최근 업로드부터 가장 오래된 업로드 순으로 표시됩니다. 각 업로드에 대해 문서 이름, 업로드 상태, 업로드 날짜, 업로드한 파일 개수, 업로드한 파일 유형, 파일의 언어 쌍이 표시됩니다.

    ![업로드 기록 페이지](media/how-to/how-to-document-history-2.png)

3. 업로드 기록 레코드를 클릭합니다. [업로드 기록 세부 사항] 페이지에서 업로드에 포함된 파일, 파일의 업로드 상태, 파일의 언어 및 오류 메시지(업로드에 오류가 있는 경우)를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [문서 세부 사항 페이지](how-to-view-document-details.md)에서 추출된 문장 목록을 검토합니다.
- [모델을 훈련하는 방법](how-to-train-model.md).
