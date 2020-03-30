---
title: 윈도우 파워 쉘 인터페이스를 통해 마이크로 소프트 Azure 데이터 박스 에지 장치에 연결하고 관리 | 마이크로 소프트 문서
description: Windows PowerShell 인터페이스를 통해 데이터 상자 에지를 연결하고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265481"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure 데이터 상자 에지 장치 관리

Azure 데이터 상자 가장자리 솔루션을 사용하면 데이터를 처리하고 네트워크를 통해 Azure로 보낼 수 있습니다. 이 문서에서는 데이터 상자 가장자리 장치에 대한 구성 및 관리 작업 중 일부에 대해 설명합니다. Azure 포털, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용하여 장치를 관리할 수 있습니다.

이 문서에서는 PowerShell 인터페이스를 사용하여 수행하는 작업에 중점을 둡니다.

이 문서에는 다음 절차가 포함되어 있습니다.

- PowerShell 인터페이스에 연결
- 지원 패키지 만들기
- 인증서 업로드
- 장치 재설정
- 장치 정보 보기
- 계산 로그 받기
- 컴퓨팅 모듈 모니터링 및 문제 해결

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

또한 IoT Edge 인증서를 업로드하여 IoT Edge 장치와 연결할 수 있는 다운스트림 장치 간의 안전한 연결을 활성화할 수도 있습니다. 설치해야 하는 세 가지 IoT Edge*인증서(.pem* 형식)가 있습니다.

- 루트 CA 인증서 또는 소유자 CA
- 디바이스 CA 인증서
- 장치 키 인증서

다음 예제에서는 IoT Edge 인증서를 설치하기 위해 이 cmdlet의 사용을 보여 주며 있습니다.

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
이 cmdlet을 실행하면 네트워크 공유에 대한 암호를 입력하라는 메시지가 표시됩니다.

인증서에 대한 자세한 내용은 [Azure IoT Edge 인증서](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) 또는 [게이트웨이에 인증서 설치로](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)이동하십시오.

## <a name="view-device-information"></a>장치 정보 보기
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>디바이스 재설정

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>계산 로그 받기

장치에서 계산 역할이 구성된 경우 PowerShell 인터페이스를 통해 계산 로그를 얻을 수도 있습니다.

1. [PowerShell 인터페이스에 연결합니다.](#connect-to-the-powershell-interface)
2. 을 `Get-AzureDataBoxEdgeComputeRoleLogs` 사용하여 장치에 대한 계산 로그를 가져옵니다.

    다음 예제에서는 이 cmdlet의 사용을 보여 주며 있습니다.

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    다음은 cmdlet에 사용되는 매개 변수에 대한 설명입니다.
    - `Path`: 계산 로그 패키지를 만들 려는 공유에 대한 네트워크 경로를 제공합니다.
    - `Credential`: 네트워크 공유에 대한 사용자 이름을 제공합니다. 이 cmdlet을 실행하는 경우 공유 암호를 제공해야 합니다.
    - `FullLogCollection`: 이 매개 변수는 로그 패키지에 모든 계산 로그가 포함되도록 합니다. 기본적으로 로그 패키지에는 로그의 하위 집합만 포함됩니다.

## <a name="monitor-and-troubleshoot-compute-modules"></a>컴퓨팅 모듈 모니터링 및 문제 해결

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>원격 세션 종료

원격 PowerShell 세션을 종료하려면 PowerShell 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Data Box Edge](data-box-edge-deploy-prep.md)를 배포합니다.
