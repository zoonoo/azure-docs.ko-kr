---
title: 파일 포함
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67182807"
---
응용 프로그램을 시작 하는 동안 Python에 오류가 발생 하는 경우에는 간단한 오류 페이지만 반환 됩니다. 예를 들어 내부 서버 오류가 발생 했기 때문에 페이지를 표시할 수 없습니다. "

Python 애플리케이션 오류를 캡처하려면:

1. Azure Portal의 웹앱에서 **설정**을 선택합니다.
2. **설정** 탭에서 **애플리케이션 설정**을 선택합니다.
3. **앱 설정** 아래에 다음 키/값 쌍을 입력합니다.
    * 키: WSGI_LOG
    * 값: D:\home\site\wwwroot\logs.txt(선택한 파일 이름 입력)

이제 wwwroot 폴더의 logs.txt 파일에 오류가 표시됩니다.
