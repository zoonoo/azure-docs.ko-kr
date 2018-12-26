---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 5169432af65a046465c9d1ced349687d6fdc8bb7
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2018
ms.locfileid: "35394029"
---
응용 프로그램을 시작하는 동안 Python에서 오류가 발생하는 경우 간단한 오류 페이지만 반환됩니다(예: "내부 서버 오류가 발생하여 페이지를 표시할 수 없습니다.").

Python 응용 프로그램 오류를 캡처하려면:

1. Azure Portal의 웹앱에서 **설정**을 선택합니다.
2. **설정** 탭에서 **응용 프로그램 설정**을 선택합니다.
3. **앱 설정** 아래에 다음 키/값 쌍을 입력합니다.
    * 키: WSGI_LOG
    * 값: D:\home\site\wwwroot\logs.txt(선택한 파일 이름 입력)

이제 wwwroot 폴더의 logs.txt 파일에 오류가 표시됩니다.
