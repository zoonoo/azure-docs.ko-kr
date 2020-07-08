---
title: Azure Kinect 펌웨어 도구
description: Azure Kinect 펌웨어 도구를 사용 하 여 장치 펌웨어를 쿼리하고 업데이트 하는 방법을 알아봅니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 펌웨어, 업데이트
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276975"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Azure Kinect 진한 펌웨어 도구

Azure Kinect 펌웨어 도구를 사용 하 여 Azure Kinect 진한 장치 펌웨어를 쿼리하고 업데이트할 수 있습니다.

## <a name="list-connected-devices"></a>연결 된 장치 나열

-L 옵션을 사용 하 여 연결 된 장치 목록을 가져올 수 있습니다.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>장치 펌웨어 버전 확인

-Q 옵션 (예:)을 사용 하 여 첫 번째 연결 된 장치의 현재 펌웨어 버전을 확인할 수 있습니다 `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

장치가 둘 이상 연결 된 경우 명령에 전체 일련 번호를 추가 하 여 쿼리할 장치를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>디바이스 펌웨어 업데이트

이 도구의 가장 일반적인 용도는 장치 펌웨어를 업데이트 하는 것입니다. 옵션을 사용 하 여 도구를 호출 하 여 업데이트를 수행 합니다 `-u` . 펌웨어 업데이트는 업데이트 해야 하는 펌웨어 파일에 따라 몇 분 정도 걸릴 수 있습니다.

단계별 펌웨어 업데이트 지침은 [Azure Kinect 펌웨어 업데이트](update-device-firmware.md)를 참조 하세요.  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

장치가 둘 이상 연결 된 경우 명령에 전체 일련 번호를 추가 하 여 쿼리 하려는 장치를 지정할 수 있습니다.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>디바이스 다시 설정

장치를 알려진 상태로 전환 해야 하는 경우-r 옵션을 사용 하 여 연결 된 Azure Kinect 진한을 다시 설정할 수 있습니다.

장치가 둘 이상 연결 된 경우 명령에 전체 일련 번호를 추가 하 여 쿼리 하려는 장치를 지정할 수 있습니다.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>펌웨어 검사

펌웨어를 검사 하면 실제 장치를 업데이트 하기 전에 펌웨어 bin 파일에서 버전 정보를 가져올 수 있습니다.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>펌웨어 업데이트 도구 옵션

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[장치 펌웨어를 업데이트 하는 단계별 지침](update-device-firmware.md)
