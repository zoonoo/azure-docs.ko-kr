---
title: Azure Data Lake Analytics에서 U-SQL 인식 기능 사용
description: U-SQL에서 인텔리전스 인식 기능을 사용하는 방법에 알아봅니다.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: ab40d466d7b60dd09b8953012c80d0e84f4ac471
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802071"
---
# <a name="get-started-with-the-cognitive-capabilities-of-u-sql"></a>U-SQL의 인식 기능 시작

## <a name="overview"></a>개요
U-SQL에 대한 인식 기능은 개발자가 빅 데이터 프로그램에서 인텔리전스를 사용하도록 합니다. 

다음과 같은 인식 기능을 사용할 수 있습니다.
* 이미징: 얼굴 감지 [샘플](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 이미징: 감정 감지 [샘플](https://github.com/Azure-Samples/usql-cognitive-imaging-emotion-detection-hello-world)
* 이미징: 개체 감지(태그 지정) [샘플](https://github.com/Azure-Samples/usql-cognitive-imaging-object-tagging-hello-world)
* 이미징: OCR(광학 문자 인식) [샘플](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 텍스트: 핵심 구 추출 및 감정 분석 [샘플](https://github.com/Azure-Samples/usql-cognitive-text-hello-world)

## <a name="registering-cognitive-extensions-in-u-sql"></a>U-SQL에서 인식 확장 등록
시작하기 전에 U-SQL에서 인식 확장을 등록하려면 이 문서의 단계를 수행합니다. [U-SQL에서 인식 확장 등록](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/cognitive-capabilities-in-u-sql#registeringExtensions)

## <a name="next-steps"></a>다음 단계
* [U-SQL/인식 샘플](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
