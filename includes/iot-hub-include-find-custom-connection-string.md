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
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050468"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

**서비스 연결** 및 **레지스트리 읽기** 권한을 부여하는 공유 액세스 정책을 만들고 이 정책에 대한 연결 문자열을 가져오려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹**을 선택합니다. 허브가 있는 리소스 그룹을 선택한 다음, 리소스 목록에서 허브를 선택합니다.

1. 허브의 왼쪽 창에서 **공유 액세스 정책**을 선택합니다.

1. 정책 목록 위의 상단 메뉴에서 **추가**를 선택합니다.

1. **공유 액세스 정책 추가**에서 정책에 대한 설명형 이름(예: *serviceAndRegistryRead*)을 입력합니다. **사용 권한**에서 **레지스트리 읽기**를 및 **서비스 연결**을 선택한 후 **만들기**를 선택합니다.

    ![새 공유 액세스 정책을 추가하는 방법 표시](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. 정책 목록에서 새 정책을 선택합니다.

1. **공유 액세스 키**에서 **연결 문자열 -- 기본 키**에 대한 복사 아이콘을 선택하고 값을 저장합니다.

    ![연결 문자열을 검색하는 방법 표시](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 권한](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)을 참조하세요.
