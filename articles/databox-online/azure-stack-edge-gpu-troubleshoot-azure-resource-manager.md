---
title: Azure Stack Edge Pro GPU에서 Azure Resource Manager 구성 문제 해결| Microsoft Docs
description: Azure Stack Edge 디바이스에서 Azure Resource Manager 구성 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: d8226d780c3caabb94f0780f3a4296765207f1b1
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987842"
---
# <a name="troubleshoot-azure-resource-manager-issues-on-an-azure-stack-edge-device"></a>Azure Stack Edge 디바이스에서 Azure Resource Manager 문제 해결 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge 디바이스의 리소스 관리를 방해할 수 있는 Azure Resource Manager 문제를 해결하는 방법을 설명합니다. Azure Resource Manager는 Azure 계정에서 리소스를 생성, 업데이트 및 삭제할 수 있는 관리 계층을 제공합니다.
 
## <a name="azure-resource-manager-configuration-errors"></a>Azure Resource Manager 구성 오류

다음 오류는 Azure Resource Manager 구성에 문제가 있음을 나타낼 수 있습니다. 

| **문제/오류** |  **해결 방법** | 
|------------|-----------------|
|일반적인 문제|<li>[디바이스가 올바르게 구성되었는지 확인](#verify-the-device-is-configured-properly)하세요.<li> [클라이언트가 올바르게 구성되어 있는지 확인](#verify-the-client-is-configured-properly)하세요.|
|Add-AzureRmEnvironment: 요청을 보내는 중 오류가 발생했습니다.<br>줄:1 문자:1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|디바이스에 연결할 수 없거나 올바르게 구성되지 않았습니다. 디바이스 및 클라이언트가 올바르게 구성되어 있는지 확인하세요. 참고 자료는 이 표의 **일반 문제** 행을 참조하세요.|
|서비스에서 오류를 반환했습니다. 자세한 내용은 InnerException 확인: 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대한 트러스트 관계를 설정할 수 없습니다. |  디바이스에서 인증서를 만들고 설치하는 동안 오류가 발생했습니다. 자세한 내용은 [인증서 만들기 및 설치](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)를 참조하세요. |
|작업이 잘못된 상태 코드 ‘ServiceUnavailable’를 반환함 <br> 응답 상태 코드가 성공을 나타내지 않음: 503(서비스를 사용할 수 없음). | 이 오류는 다음과 같은 상태로 인해 발생했을 수 있습니다.<li>ArmStsPool이 중지됨 상태입니다.</li><li>Azure Resource Manager가 중단되었거나 보안 토큰 서비스의 웹 사이트가 중단되었습니다.</li><li>Azure Resource Manager 클러스터 리소스가 다운되었습니다.</li><br>디바이스를 다시 시작하면 문제가 해결될 수 있습니다. 추가로 디버그하려면 [지원 패키지를 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)합니다.|
|AADSTS50126: 사용자 이름 또는 암호가 잘못되었습니다.<br>추적 ID: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>상관 관계 ID: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>타임스탬프: 2019-11-15 09:21:57Z: 원격 서버가 오류를 리턴함: (400) 잘못된 요청입니다.<br>줄:1 문자:1 |이 오류는 다음과 같은 상태로 인해 발생했을 수 있습니다.<li>잘못된 사용자 이름 및 암호인 경우 [Azure Portal에서 Azure Storage Manager 암호를 재설정](./azure-stack-edge-gpu-set-azure-resource-manager-password.md)했는지 확인한 후에 올바른 암호를 사용합니다.<li>잘못된 테넌트 ID의 경우 테넌트 ID가 `c0257de7-538f-415c-993a-1b87a031879d`로 설정되어 있는지 확인합니다.</li>|
|connect-AzureRmAccount: AADSTS90056: 리소스가 사용하지 않게 설정되었거나 없습니다. 앱 코드를 확인하여 액세스하려는 리소스의 정확한 리소스 URL을 지정했는지 확인하세요.<br>추적 ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>상관 관계 ID: 75c8ef5a-830e-48b5-b039-595a96488ff9 타임스탬프: 2019-11-18 07:00:51Z: 원격 서버에서 오류를 반환함: (400) 잘못됨 |`Add-AzureRmEnvironment` 명령에 사용된 Azure Resource Manager 엔드포인트가 잘못되었습니다.<br>Azure Resource Manager 엔드포인트를 찾으려면 디바이스 로컬 웹 UI의 **디바이스** 페이지에서 **디바이스 엔드포인트** 를 확인합니다.<br>PowerShell 지침은 [Azure Resource Manager 환경 설정](azure-stack-edge-gpu-connect-resource-manager.md#step-7-set-azure-resource-manager-environment)을 참조하세요. |
|클라우드에서 엔드포인트를 가져올 수 없습니다.<br>네트워크에 연결되어 있는지 확인하세요. 오류 세부 정보: HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005): URL로 최대 재시도 초과: /metadata/endpoints?api-version=2015-01-01 (Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |이 오류는 대부분 Mac 또는 Linux 환경에서 나타납니다. PEM 형식 인증서가 Python 인증서 저장소에 추가되지 않았기 때문에 오류가 발생합니다. |


## <a name="troubleshoot-general-issues-with-azure-resource-manager"></a>Azure Resource Manager와 관련된 일반적인 문제 해결

Azure Resource Manager와 관련된 일반적인 문제의 경우 디바이스와 클라이언트가 올바르게 구성되었는지 확인하세요. 종단 간 절차는 [Azure Stack Edge Pro GPU 디바이스에서 Azure Resource Manager에 연결](azure-stack-edge-gpu-connect-resource-manager.md)을 참조하세요.


### <a name="verify-the-device-is-configured-properly"></a>디바이스가 올바르게 구성되었는지 확인합니다.

1. 로컬 UI에서 디바이스 네트워크가 올바르게 구성되어 있는지 확인합니다.

2. [모든 엔드포인트에 대해 인증서가 업데이트되었는지 확인](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)합니다.

3. 로컬 UI의 **디바이스** 페이지에서 Azure Resource Manager 관리 및 로그인 엔드포인트를 가져옵니다.

4. 디바이스가 활성화되고 Azure에 등록되었는지 확인합니다.


### <a name="verify-the-client-is-configured-properly"></a>클라이언트가 올바르게 구성되어 있는지 확인

1. [올바른 PowerShell 버전이 설치되었는지 확인](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client)합니다.

2. [올바른 PowerShell 모듈이 설치되었는지 확인](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)합니다.

3. Azure Resource Manager 및 로그인 엔드포인트에 연결할 수 있는지 유효성을 검사합니다. 엔드포인트에 ping을 시도할 수 있습니다. 예를 들면 다음과 같습니다.

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   연결할 수 없는 경우 [DNS/호스트 파일 항목을 추가](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)합니다.
   
4. [클라이언트 인증서가 설치되어 있는지 확인](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)합니다.

5. PowerShell을 사용하는 경우 다음 PowerShell 명령을 실행하여 자세한 메시지를 보려면 디버그 기본 설정을 사용하도록 설정합니다. 

    `$debugpreference = "continue"`


## <a name="next-steps"></a>다음 단계

- [디바이스 활성화 문제를 해결](azure-stack-edge-gpu-troubleshoot-activation.md)합니다.
- [디바이스 문제를 해결](azure-stack-edge-gpu-troubleshoot.md)합니다.