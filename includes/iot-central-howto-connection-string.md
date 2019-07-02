---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182329"
---
1. 다음과 같이 `dps-keygen` 명령줄 유틸리티를 사용하여 연결 문자열을 생성합니다.

    [키 생성기 유틸리티](https://github.com/Azure/dps-keygen)를 설치하려면 다음 명령을 실행합니다.

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 연결 문자열을 생성하려면 앞에서 적어 놓은 연결 세부 정보를 사용하여 다음 명령을 실행합니다.

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. 디바이스 코드에 사용할 연결 문자열을 `dps-keygen` 출력에서 복사합니다.