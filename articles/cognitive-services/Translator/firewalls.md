---
title: 방화벽 뒤에서 변환 - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API를 사용하여 IP 방화벽 뒤에서 변환합니다.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: 7dec156eeb49f7b9f088fb721893f6c82239d509
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219654"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Translator Text API를 사용하여 IP 방화벽 뒤에서 변환하는 방법

Translator Text API는 도메인 이름 또는 IP 필터링을 사용하여 방화벽 뒤에서 변환할 수 있습니다. 사람들이 선호하는 방법은 도메인 이름 필터링입니다. IP로 필터링되는 방화벽 뒤에서 Microsoft Translator를 실행하는 것은 **좋지 않습니다**. 나중에 공지 없이 설치가 중단될 수 있습니다. 

## <a name="translator-ip-addresses"></a>Translator IP 주소
api.cognitive.microsofttranslator.com의 IP 주소 - 2018년 11월 20일 기준 Microsoft Translator Text API:

* **아시아 태평양:** 40.90.139.163, 104.44.89.44
* **유럽:** 40.90.138.4, 40.90.141.99
* **북아메리카:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Translator API 호출의 IP 방화벽 뒤에 변환](reference/v3-0-translate.md)
