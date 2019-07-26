---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a752427d1e5873f0a27fd231872e3a13b234d9ed
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402352"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

**RegistryReadWrite** 정책에 대 한 IoT Hub 연결 문자열을 가져오려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT hub를 엽니다.  IoT hub를 가져오는 가장 쉬운 방법은 **리소스 그룹**을 선택 하 고 iot hub가 있는 리소스 그룹을 선택한 다음 리소스 목록에서 iot hub를 선택 하는 것입니다.

2. IoT hub의 왼쪽 창에서 **공유 액세스 정책**을 선택 합니다.

3. 정책 목록에서 **registryReadWrite** 정책을 선택 합니다.

4. **공유 액세스 키**에서 **연결 문자열--기본 키** 의 복사 아이콘을 선택 하 고 값을 저장 합니다.

    ![연결 문자열을 검색하는 방법 표시](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-string.png)

IoT Hub 공유 액세스 정책 및 사용 권한에 대 한 자세한 내용은 [액세스 제어 및 권한](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)을 참조 하세요.
