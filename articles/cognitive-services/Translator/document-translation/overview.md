---
title: 문서 번역이란?
description: 클라우드 기반 배치 문서 변환 서비스 및 프로세스에 대한 개요입니다.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 0d9ef13de29ac140d94e9e4c05b14f35b9e5834c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968172"
---
# <a name="what-is-document-translation-preview"></a>문서 번역(미리 보기)이란?

문서 번역은 [Azure Translator](../translator-info-overview.md) 서비스의 클라우드 기반 기능이며 REST API의 Azure Cognitive Service 제품군의 일부입니다. 문서 번역 API는 문서 구조와 데이터 형식을 유지하면서 90개 언어와 방언으로 문서를 번역합니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.  

* [**빠른 시작**](get-started-with-document-translation.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](create-sas-tokens.md)에는 보다 구체적이거나 사용자 지정된 방식으로 기능을 사용하기 위한 지침이 포함되어 있습니다.  

## <a name="document-translation-key-features"></a>문서 번역 주요 기능

| 기능 | Description |
| ---------| -------------|
| **대용량 파일 번역**| 전체 문서를 비동기적으로 번역합니다.|
|**대용량 파일 번역**|90개 언어 및 방언으로 여러 파일을 번역합니다.|
|**원본 파일 프레젠테이션 보존**| 원래 레이아웃과 형식을 유지하면서 파일을 번역합니다.|
|**사용자 지정 번역 적용**| 일반 및 [사용자 지정 번역](../customization.md#custom-translator) 모델을 사용하여 문서를 번역합니다.|
|**사용자 지정 글로서리 적용**|사용자 지정 글로서리를 사용하여 문서를 번역합니다.|

## <a name="how-to-get-started"></a>시작하는 방법

이 방법 가이드에서는 문서 변환기를 사용하여 빠르게 시작하는 방법을 알아봅니다. 시작하려면 활성 [Azure 계정](https://azure.microsoft.com/free/cognitive-services/)이 필요합니다.  계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free)에 만들 수 있습니다.

> [!div class="nextstepaction"]
> [시작](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>지원되는 문서 형식

문서 번역에서 지원되는 문서 파일 형식은 다음과 같습니다.

| 파일 형식| 파일 확장명|Description|
|---|---|--|
|Adobe PDF|.pdf|Adobe Acrobat Portable 문서 형식|
|HTML|.html|Hyper Text Markup Language|
|지역화 교환 파일 형식|.xlf. , xliff| 번역 메모리 시스템에서 내보낸 병렬 문서 형식입니다. 사용되는 언어는 파일 안에 정의되어 있습니다.|
|Microsoft Excel|.xlsx|데이터 분석 및 설명서에 대한 스프레드 시트 파일입니다.|
|Microsoft Outlook|.msg|Microsoft Outlook 내에서 만들어지거나 저장된 이메일 메시지입니다.|
|Microsoft PowerPoint|.pptx| 슬라이드 쇼 형식으로 콘텐츠를 표시하는 데 사용되는 프레젠테이션 파일입니다.|
|Microsoft Word|.docx| 텍스트 문서 파일입니다.|
|탭으로 구분된 값/TAB|.tsv/.tab| 스프레드시트 프로그램에서 사용하는 탭으로 구분된 원시 데이터 파일입니다.|
|텍스트|.txt| 서식이 지정되지 않은 텍스트 문서입니다.|

## <a name="supported-glossary-formats"></a>지원되는 글로서리 형식

문서 번역에서 지원되는 글로서리 파일 형식은 다음과 같습니다.

| 파일 형식| 파일 확장명|Description|
|---|---|--|
|지역화 교환 파일 형식|.xlf. , xliff| 번역 메모리 시스템에서 내보낸 병렬 문서 형식입니다. 사용되는 언어는 파일 안에 정의되어 있습니다.|
|탭으로 구분된 값/TAB|.tsv/.tab| 스프레드시트 프로그램에서 사용하는 탭으로 구분된 원시 데이터 파일입니다.|

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [문서 번역 시작](get-started-with-document-translation.md)
