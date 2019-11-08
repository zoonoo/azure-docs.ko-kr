---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e3b3d944508a4261b78def0b3bee13f7395a8bf0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749012"
---
Azure Portal에서 저장소 계정 액세스 키 또는 연결 문자열을 보고 복사 하려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 스토리지 계정을 찾습니다.
3. 스토리지 계정 개요의 **설정** 섹션에서 **액세스 키**를 선택합니다. 계정 액세스 키는 물론 각 키의 전체 연결 문자열이 나타납니다.
4. **key1** 아래에서 **키** 값을 찾고, **복사** 단추를 클릭하여 계정 키를 복사합니다.
5. 또는 전체 연결 문자열을 복사할 수 있습니다. **key1** 아래에서 **연결 문자열** 값을 찾고, **복사** 단추를 클릭하여 연결 문자열을 복사합니다.

    ![Azure Portal에서 액세스 키를 보는 방법을 보여 주는 스크린샷](media/storage-view-keys-include/portal-connection-string.png)

두 키 중 하나를 사용 하 여 Azure Storage에 액세스할 수 있지만 일반적으로 첫 번째 키를 사용 하 고 키를 회전할 때 두 번째 키의 사용을 예약 하는 것이 좋습니다.
