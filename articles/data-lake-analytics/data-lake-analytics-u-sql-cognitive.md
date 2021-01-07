---
title: Azure Data Lake Analytics의 U-SQL 인식 기능
description: U-SQL에서 인식 기능의 인텔리전스를 사용 하는 방법에 대해 알아봅니다. 이 코드 샘플은 시작 하는 데 도움이 됩니다.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/05/2018
ms.openlocfilehash: 19d947b8b595107c76c1201d05e4d5ade3d7a092
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220061"
---
# <a name="get-started-with-the-cognitive-capabilities-of-u-sql"></a>U-SQL의 인식 기능 시작

## <a name="overview"></a>개요
U-SQL에 대한 인식 기능은 개발자가 빅 데이터 프로그램에서 인텔리전스를 사용하도록 합니다. 

인식 기능을 사용할 수 있는 샘플은 다음과 같습니다.
* 이미징: [얼굴 감지](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 이미징: [Emotion 검색](https://github.com/Azure-Samples/usql-cognitive-imaging-emotion-detection-hello-world)
* 이미징: [개체 검색 (태그 지정)](https://github.com/Azure-Samples/usql-cognitive-imaging-object-tagging-hello-world)
* 이미징: [OCR (광학 문자 인식)](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 텍스트: [핵심 구 추출 및 감정 분석](https://github.com/Azure-Samples/usql-cognitive-text-hello-world)

## <a name="registering-cognitive-extensions-in-u-sql"></a>U-SQL에서 인식 확장 등록
시작하기 전에 U-SQL에서 인식 확장을 등록하려면 이 문서의 단계를 수행합니다. [U-SQL에서 인식 확장 등록](/u-sql/objects-and-extensions/cognitive-capabilities-in#registeringExtensions)

## <a name="next-steps"></a>다음 단계
* [U-SQL/인식 샘플](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](./data-lake-analytics-u-sql-get-started.md)