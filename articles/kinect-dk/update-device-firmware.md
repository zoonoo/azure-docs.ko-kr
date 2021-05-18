---
title: Azure Kinect DK 펌웨어 업데이트
description: Azure Kinect 펌웨어 도구를 사용하여 Azure Kinect DK 디바이스 펌웨어를 업데이트하는 방법을 알아봅니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 펌웨어, 업데이트, 복구
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94356229"
---
# <a name="update-azure-kinect-dk-firmware"></a>Azure Kinect DK 펌웨어 업데이트

이 문서에서는 Azure Kinect DK에서 디바이스 펌웨어를 업데이트하는 방법에 대한 참고 자료를 제공합니다.

Azure Kinect DK는 펌웨어를 자동으로 업데이트하지 않습니다. [Azure Kinect 펌웨어 도구](azure-kinect-firmware-tool.md)를 사용하여 펌웨어를 사용 가능한 최신 버전으로 수동 업데이트할 수 있습니다.

## <a name="prepare-for-firmware-update"></a>펌웨어 업데이트 준비

1. [SDK 다운로드](sensor-sdk-download.md).
2. SDK를 설치합니다.
3. (SDK 설치 위치)\tools\ 아래의 SDK 설치 위치에서 다음을 찾습니다.

    - AzureKinectFirmwareTool.exe
    - 펌웨어 폴더의 펌웨어 .bin 파일(예: *AzureKinectDK_Fw_1.5.926614.bin*)

4. 디바이스를 호스트 PC에 연결하고 전원도 켭니다.

> [!IMPORTANT]
> 펌웨어를 업데이트하는 동안 USB 및 전원 공급이 연결된 상태를 유지합니다. 업데이트 중에 연결이 제거하면 펌웨어가 손상된 상태에 빠질 수 있습니다.

## <a name="update-device-firmware"></a>디바이스 펌웨어 업데이트

1. (SDK 설치 위치)\tools\ 폴더에서 명령 프롬프트를 엽니다.
2. Azure Kinect 펌웨어 도구를 사용하여 펌웨어 업데이트

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    예:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. 펌웨어 업데이트가 완료될 때까지 기다립니다. 이미지 크기에 따라 몇 분 정도 걸릴 수 있습니다.

### <a name="verify-device-firmware-version"></a>디바이스 펌웨어 버전 확인

1. 펌웨어가 업데이트된 것을 확인합니다.

    `AzureKinectFirmwareTool.exe -q`

2. 다음 예를 봅니다.

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

3. 위의 출력이 표시되면 펌웨어가 업데이트된 것입니다.

4. 펌웨어 업데이트 후에 [Azure Kinect 뷰어](azure-kinect-viewer.md)를 실행하여 모든 센서가 예상대로 작동하고 있는지 확인할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

펌웨어 업데이트는 여러 가지 이유로 실패할 수 있습니다. 펌웨어 업데이트가 실패했을 때 다음과 같은 완화 단계를 시도합니다.

1. 펌웨어 업데이트 명령을 두 번째로 실행해 봅니다.

2. 펌웨어 버전을 쿼리하여 디바이스가 여전히 연결되어 있는지 확인합니다.        AzureKinectFirmareTool.exe

3. 다른 모든 작업이 실패하는 경우 [복구](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) 단계를 따라 공장 펌웨어로 되돌리고 다시 시도합니다.

추가적인 문제에 대해서는 [Microsoft 지원 페이지](./index.yml)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Kinect 펌웨어 도구](azure-kinect-firmware-tool.md)