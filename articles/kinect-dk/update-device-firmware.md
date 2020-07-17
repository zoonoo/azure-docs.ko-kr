---
title: Azure Kinect 진한 펌웨어 업데이트
description: Azure Kinect 펌웨어 도구를 사용 하 여 Azure Kinect 진한 장치 펌웨어를 업데이트 하는 방법을 알아봅니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 펌웨어, 업데이트, 복구
ms.openlocfilehash: eb60003a4233110cb33208bcb8e9784737bb2a8b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85277004"
---
# <a name="update-azure-kinect-dk-firmware"></a>Azure Kinect 진한 펌웨어 업데이트

이 문서에서는 Azure Kinect에서 장치 펌웨어를 업데이트 하는 방법에 대 한 지침을 제공 합니다.

Azure Kinect는 펌웨어를 자동으로 업데이트 하지 않습니다. [Azure Kinect 펌웨어 도구](azure-kinect-firmware-tool.md) 를 사용 하 여 수동으로 펌웨어를 사용 가능한 최신 버전으로 업데이트할 수 있습니다.

## <a name="prepare-for-firmware-update"></a>펌웨어 업데이트 준비

1. [SDK를 다운로드](sensor-sdk-download.md)합니다.
2. SDK를 설치합니다.
3. Sdk 설치 위치 (SDK 설치 위치) \tools\에서 다음을 찾습니다.

    - AzureKinectFirmwareTool.exe
    - 펌웨어 폴더의 펌웨어 bin 파일 (예: *AzureKinectDK_Fw_1.5.926614*.

4. 장치를 호스트 PC에 연결 하 여 전원을 켭니다.

> [!IMPORTANT]
> 펌웨어를 업데이트 하는 동안 USB 및 전원 공급 장치를 연결 된 상태로 유지 합니다. 업데이트 중에 두 연결을 제거 하면 펌웨어가 손상 된 상태로 전환 될 수 있습니다.

## <a name="update-device-firmware"></a>디바이스 펌웨어 업데이트

1. (SDK 설치 위치) \tools\ 폴더에서 명령 프롬프트를 엽니다.
2. Azure Kinect 펌웨어 도구를 사용 하 여 펌웨어 업데이트

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    예:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. 펌웨어 업데이트가 완료 될 때까지 기다립니다. 이미지 크기에 따라 몇 분 정도 걸릴 수 있습니다.

### <a name="verify-device-firmware-version"></a>장치 펌웨어 버전 확인

1. 펌웨어가 업데이트 되었는지 확인 합니다.

    `AzureKinectFirmwareTool.exe -q`

2. 다음 예제를 봅니다.

    ```console
       >AzureKinectFirmwareTool.exe -q
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000805192412
Current Firmware Versions:
  RGB camera firmware:      1.6.102
  Depth camera firmware:    1.6.75
  Depth config file:        6109.7
  Audio firmware:           1.6.14
  Build Config:             Production
  Certificate Type:         Microsoft
    ```

3. 위의 출력이 표시 되 면 펌웨어가 업데이트 됩니다.

4. 펌웨어 업데이트 후에 [Azure Kinect viewer](azure-kinect-viewer.md) 를 실행 하 여 모든 센서가 예상 대로 작동 하는지 확인할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

펌웨어 업데이트는 여러 가지 이유로 실패할 수 있습니다. 펌웨어 업데이트가 실패할 경우 다음 완화 단계를 수행 합니다.

1. 펌웨어 업데이트 명령을 한 번 실행 해 봅니다.

2. 펌웨어 버전을 쿼리하여 장치가 계속 연결 되어 있는지 확인 합니다.        AzureKinectFirmareTool.exe

3. 다른 모든 작업이 실패 하는 경우 [복구](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) 단계를 따라 공장 펌웨어로 되돌리고 다시 시도 합니다.

추가 문제는 [Microsoft 지원 페이지](https://aka.ms/kinectsupport) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Kinect 펌웨어 도구](azure-kinect-firmware-tool.md)
