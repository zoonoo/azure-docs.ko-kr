---
title: 포함 파일
description: 포함 파일
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ecd7e070c353d706865b245427758c11a7bbfa5d
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111896662"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Azure Storage 계정을 IoT Hub에 연결

IoT 허브에 연결된 Azure Storage 계정을 만들어야 합니다. 이러한 계정을 만드는 방법은 [스토리지 계정 만들기](../articles/storage/common/storage-account-create.md)를 참조하세요. IoT Hub와 Azure Storage 계정을 연결하면 IoT Hub는 SAS URI를 생성합니다. 디바이스는 Blob 컨테이너에 안전하게 파일을 업로드하는 데 SAS URI를 사용할 수 있습니다. IoT Hub 서비스 및 디바이스 SDK에서는 SAS URI를 생성하는 프로세스를 조정하여 파일을 업로드하는 데 디바이스를 사용할 수 있도록 합니다.

## <a name="create-a-container"></a>컨테이너 만들기

스토리지 계정에 Blob 컨테이너를 만들려면 다음 단계를 수행합니다.

1. 스토리지 계정의 왼쪽 창에서 **데이터 스토리지** 의 **컨테이너** 를 선택합니다.
1. 컨테이너 블레이드에서 **+ 컨테이너** 를 선택합니다.
1. **새 컨테이너** 창이 열리면 컨테이너의 이름을 입력하고 **만들기** 를 선택합니다.

컨테이너를 생성한 후에는 [Azure Portal을 사용하여 파일 업로드 구성](../articles/iot-hub/iot-hub-configure-file-upload.md)의 안내에 따릅니다. Blob 컨테이너가 IoT 허브와 연결되고 파일 알림을 사용하는지 확인합니다.

![포털에서 파일 알림 사용](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
