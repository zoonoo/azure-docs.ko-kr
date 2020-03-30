---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883843"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

**레지스트리ReadWrite** 정책에 대한 IoT Hub 연결 문자열을 얻으려면 다음 단계를 따르십시오.

1. Azure [포털에서](https://portal.azure.com) **리소스 그룹을**선택합니다. 허브가 있는 리소스 그룹을 선택한 다음 리소스 목록에서 허브를 선택합니다.

2. 허브의 왼쪽 창에서 공유 액세스 **정책을**선택합니다.

3. 정책 목록에서 **레지스트리ReadWrite** 정책을 선택합니다.

4. **공유 액세스 키에서**연결 문자열의 복사 아이콘을 선택하고 **기본 키를** 선택하고 값을 저장합니다.

    ![연결 문자열을 검색하는 방법 표시](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 사용 권한을](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)참조하십시오.
