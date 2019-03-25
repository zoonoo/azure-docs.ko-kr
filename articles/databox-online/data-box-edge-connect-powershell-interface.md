---
title: 연결 하 고 Windows PowerShell 인터페이스를 통해 Microsoft Azure 데이터 가장자리가 상자의 장치 관리 | Microsoft Docs
description: 연결 하 고 다음 Windows PowerShell 인터페이스를 통해 가장자리가 상자의 데이터를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 9b0e94deda205497cda4ebf383f302c6c3bb896a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403598"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure 데이터 상자 Edge 장치를 관리 합니다.

Azure 데이터 가장자리가 상자의 솔루션을 사용 하 여 데이터를 처리 하 고 Azure로 네트워크를 통해 보낼 수 있습니다. 이 문서에서는 데이터 상자 Edge 장치에 대 한 구성 및 관리 작업 중 일부를 설명 합니다. 장치를 관리 하려면 Azure portal, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용할 수 있습니다.

이 문서에서는 PowerShell 인터페이스를 사용 하 여 수행 하는 작업에 중점을 둡니다.

이 문서는 다음 절차에 포함 됩니다.

- PowerShell 인터페이스에 연결
- 지원 세션 시작
- 지원 패키지 만들기
- 인증서 업로드
- 장치 재설정
- 장치 정보 보기
- 계산 로그 가져오기
- 모니터링 및 계산 모듈 문제 해결

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="view-device-information"></a>장치 정보 보기
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>장치를 다시 설정

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>계산 로그 가져오기

계산 역할을 장치에 구성 된 경우에 PowerShell 인터페이스를 통해 계산 로그를 가져올 수 있습니다.

1. [PowerShell 인터페이스를 연결할](#connect-to-the-powershell-interface)합니다.
2. 사용 하 여는 `Get-AzureDataBoxEdgeComputeRoleLogs` 장치에 대 한 계산 로그를 가져오려고 합니다.

    다음 예제에서는이 cmdlet의 사용법을 보여 줍니다.

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    Cmdlet에 사용 되는 매개 변수 설명은 다음과 같습니다.
    - `Path`: 계산 로그 패키지를 만들려는 공유 네트워크 경로 제공 합니다.
    - `Credential`: 네트워크 공유에 대 한 사용자 이름과 암호를 제공 합니다.
    - `RoleInstanceName`: 이 문자열을 제공 `IotRole` 이 매개 변수에 대 한 합니다.
    - `FullLogCollection`: 이 매개 변수는 로그 패키지의 모든 계산 로그에 포함 되어 있음을 확인 합니다. 기본적으로 로그 패키지에는 로그의 하위 집합만 포함 되어 있습니다.

## <a name="monitor-and-troubleshoot-compute-modules"></a>모니터링 및 계산 모듈 문제 해결

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]


## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Data Box Edge](data-box-edge-deploy-prep.md)를 배포합니다.
