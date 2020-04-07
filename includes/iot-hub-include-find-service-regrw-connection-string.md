---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756949"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

**서비스 연결** 및 **레지스트리 쓰기** 권한을 부여하는 공유 액세스 정책을 만들고 이 정책에 대한 연결 문자열을 얻으려면 다음 단계를 따르십시오.

1. Azure [포털에서](https://portal.azure.com) **리소스 그룹을**선택합니다. 허브가 있는 리소스 그룹을 선택한 다음 리소스 목록에서 허브를 선택합니다.

1. 허브의 왼쪽 창에서 공유 액세스 **정책을**선택합니다.

1. 정책 목록 위의 위쪽 메뉴에서 **추가를**선택합니다.

1. **공유 액세스 정책 추가에서** *serviceAndRegistryReadWrite*와 같은 정책에 대한 설명 이름을 입력합니다. **사용 권한에서** **레지스트리 쓰기** 및 서비스 **연결을**선택한 다음 **을 선택합니다.** (레지스트리 **쓰기를** **선택하면**레지스트리 읽기 권한이 자동으로 포함됩니다.)

    ![새 공유 액세스 정책을 추가하는 방법 표시](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. 정책 목록에서 새 정책을 선택합니다.

1. **공유 액세스 키에서**연결 문자열의 복사 아이콘을 선택하고 **기본 키를** 선택하고 값을 저장합니다.

    ![연결 문자열을 검색하는 방법 표시](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 사용 권한을](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)참조하십시오.
