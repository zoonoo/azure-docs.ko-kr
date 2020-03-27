---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75379441"
---
Form Recognizer는 다음 요구 사항을 충족하는 입력 문서에서 작동합니다.

* 형식은 JPG, PNG, PDF(텍스트 또는 스캔) 또는 TIFF여야 합니다. 문자 추출 및 위치에 오류가 발생할 가능성이 없으므로 텍스트가 포함된 PDF가 가장 좋습니다.
* PDF가 암호로 잠긴 경우에는 제출하기 전에 잠금을 제거해야 합니다.
* PDF 및 TIFF 문서는 200 페이지 이하여야 하며, 학습 데이터 세트의 총 크기는 500 페이지 이하로 설정해야 합니다.
* 이미지에 대한 크기는 600 x 100 픽셀에서 4,200 x 4,200 픽셀 사이여야 합니다.
* 종이 문서에서 스캔한 경우 양식은 고품질 스캔이어야 합니다.
* 텍스트는 라틴어 알파벳(영어 문자)을 사용해야 합니다.
* 자율 학습(레이블이 지정된 데이터 제외)의 경우 데이터에는 키와 값이 포함되어야 합니다.
* 자율 학습(레이블이 지정된 데이터 제외)의 경우 키가 값의 위 또는 왼쪽에 표시되어야 합니다. 이는 아래 또는 오른쪽에 표시되지 않습니다.

Form Recognizer에서 현재 지원하지 않는 입력 데이터의 유형은 다음과 같습니다.

* 복합 테이블(중첩된 테이블, 병합된 헤더 또는 셀 등)
* 확인란 또는 라디오 단추
