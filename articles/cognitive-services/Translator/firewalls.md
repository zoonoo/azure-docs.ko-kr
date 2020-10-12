---
title: 방화벽 뒤에서 변환-변환기
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator는 도메인 이름 또는 IP 필터링을 사용 하 여 방화벽 뒤에서 변환할 수 있습니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 78a53c99f5f184c1b6b45d59d86c23efb898d7dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83996961"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Translator를 사용 하 여 IP 방화벽 뒤에서 변환 하는 방법

Translator는 도메인 이름 또는 IP 필터링을 사용 하 여 방화벽 뒤에서 변환할 수 있습니다. 사람들이 선호하는 방법은 도메인 이름 필터링입니다. IP로 필터링되는 방화벽 뒤에서 Microsoft Translator를 실행하는 것은 **좋지 않습니다**. 나중에 공지 없이 설치가 중단될 수 있습니다.

## <a name="translator-ip-addresses"></a>Translator IP 주소
2019 년 8 월 21 일을 기준으로 하는 api.cognitive.microsofttranslator.com의 IP 주소:

* **아시아 태평양:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **유럽:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **북아메리카:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Translator에서 IP 방화벽 뒤에 변환](reference/v3-0-translate.md)
