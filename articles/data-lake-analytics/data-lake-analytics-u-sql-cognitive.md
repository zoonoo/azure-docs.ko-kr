---
title: Azure Data Lake Analytics의 U-SQL 인식 기능
description: U-SQL에서 인텔리전스 인식 기능을 사용하는 방법에 알아봅니다.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: bb64f08fae3a211b3ec26177ab2604edc20a8fdd
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315680"
---
# <a name="get-started-with-the-cognitive-capabilities-of-u-sql"></a>U-SQL의 인식 기능 시작

## <a name="overview"></a>개요
U-SQL에 대한 인식 기능은 개발자가 빅 데이터 프로그램에서 인텔리전스를 사용하도록 합니다. 

인식 기능을 사용할 수 있는 샘플은 다음과 같습니다.
* 이미지 [얼굴 감지](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 이미지 [Emotion 검색](https://github.com/Azure-Samples/usql-cognitive-imaging-emotion-detection-hello-world)
* 이미지 [개체 검색 (태그 지정)](https://github.com/Azure-Samples/usql-cognitive-imaging-object-tagging-hello-world)
* 이미지 [OCR(Optical Character Recognition)](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 본문 [핵심 구 추출 & 감정 분석](https://github.com/Azure-Samples/usql-cognitive-text-hello-world)

## <a name="registering-cognitive-extensions-in-u-sql"></a>U-SQL에서 인식 확장 등록
시작 하기 전에이 문서의 단계에 따라 U-SQL에 인지 확장을 등록 합니다. [인식 확장을 U-SQL에 등록](/u-sql/objects-and-extensions/cognitive-capabilities-in#registeringExtensions)합니다.

## <a name="next-steps"></a>다음 단계
* [U-SQL/인식 샘플](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
