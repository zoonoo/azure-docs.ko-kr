---
title: Windows PowerShell 인터페이스를 통해 Microsoft Azure Stack Edge Pro FPGA 디바이스에 연결 및 관리
description: Windows PowerShell 인터페이스를 통한 Azure Stack Edge Pro FPGA 연결 및 관리에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 09271ddeac59446d134feaa0f2acb6c3e3d9d89f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461277"
---
# <a name="manage-an-azure-stack-edge-pro-fpga-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure Stack Edge Pro FPGA 디바이스 관리

Azure Stack Edge Pro FPGA 솔루션을 사용하면 데이터를 처리하고 네트워크를 통해 Azure에 보낼 수 있습니다. 이 문서에서는 Azure Stack Edge Pro FPGA 디바이스에 대한 구성 및 관리 작업을 설명합니다. Azure Portal, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용하여 디바이스를 관리할 수 있습니다.

이 문서에서는 PowerShell 인터페이스를 사용하여 수행하는 작업을 중점적으로 설명합니다. 

이 문서에는 다음 프로시저가 포함되어 있습니다.

- PowerShell 인터페이스에 연결합니다.
- 지원 패키지 만들기
- 인증서 업로드
- 디바이스 재설정
- 디바이스 정보 보기
- 컴퓨팅 로그 가져오기
- 컴퓨팅 모듈 모니터링 및 문제 해결

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결합니다.

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

IoT Edge 인증서를 업로드하여 IoT Edge 디바이스와 이 디바이스에 연결할 수 있는 다운스트림 디바이스 간에 보안 연결을 설정할 수 있습니다. 설치해야 하는 3개의 파일( *.pem* 형식)이 있습니다.

- 루트 CA 인증서 또는 소유자 CA
- 디바이스 CA 인증서
- 디바이스 프라이빗 키 

다음 예제에서는 이 cmdlet을 사용하여 IoT Edge 인증서를 설치하는 방법을 보여줍니다.

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-private-key.pem" -Credential "username"
```
이 cmdlet을 실행하면 네트워크 공유를 위한 암호를 제공하라는 메시지가 표시됩니다.

인증서에 대한 자세한 내용은 [Azure IoT Edge 인증서](../iot-edge/iot-edge-certs.md) 또는 [게이트웨이에서 인증서 설치](../iot-edge/how-to-create-transparent-gateway.md)를 참조하세요.

## <a name="view-device-information"></a>디바이스 정보 보기
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>디바이스 재설정

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>컴퓨팅 로그 가져오기

디바이스에서 컴퓨팅 역할이 구성된 경우 PowerShell 인터페이스를 통해 컴퓨팅계산 로그를 가져올 수도 있습니다.

1. [PowerShell 인터페이스에 연결하세요](#connect-to-the-powershell-interface).
2. `Get-AzureDataBoxEdgeComputeRoleLogs`를 사용하여 디바이스에 대한 컴퓨팅 로그를 가져옵니다.

    다음 예제에서는 이 cmdlet의 사용 방법을 보여줍니다.

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    다음은 cmdlet에 사용되는 매개 변수에 대한 설명입니다.
    - `Path`: 컴퓨팅 로그 패키지를 만들 공유의 네트워크 경로를 제공합니다.
    - `Credential`: 네트워크 공유에 대한 사용자 이름을 제공합니다. 이 cmdlet을 실행하는 경우 공유 암호를 제공해야 합니다.
    - `FullLogCollection`: 이 매개 변수를 사용하면 로그 패키지에 모든 컴퓨팅 로그가 포함됩니다. 기본적으로 로그 패키지에는 로그 하위 집합만 포함되어 있습니다.

## <a name="monitor-and-troubleshoot-compute-modules"></a>컴퓨팅 모듈 모니터링 및 문제 해결

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>원격 세션 종료

원격 PowerShell 세션을 종료하려면 PowerShell 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-prep.md)를 배포합니다.