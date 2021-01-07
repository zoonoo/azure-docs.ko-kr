---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017535"
---
이 자습서의 뒷부분에서 샘플 디바이스 애플리케이션을 실행하는 경우 다음과 같은 구성 값이 필요합니다.

* ID 범위: IoT Central 애플리케이션에서 **관리 > 디바이스 연결** 로 이동합니다. **ID 범위** 값을 기록해 둡니다.
* 그룹 기본 키: IoT Central 애플리케이션에서 **관리 > 디바이스 연결 > SAS-IoT-Devices** 로 이동합니다. 공유 액세스 서명 **기본 키** 값을 기록해 둡니다.

Cloud Shell을 사용하여 방금 검색한 그룹 SAS 키에서 디바이스 키를 생성합니다.

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

생성된 디바이스 키를 기록해 두고 이 자습서의 뒷부분에서 사용합니다.
