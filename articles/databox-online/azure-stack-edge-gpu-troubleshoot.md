---
title: Azure Portal을 사용하여 GPU가 있는 Azure Stack Edge Pro 문제 해결 | Microsoft Docs
description: Azure Stack Edge Pro GPU 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c6f7182fe058bacb1236ff10dfc1553d23a7e1f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645260"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 문제 해결 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에서 문제를 해결하는 방법을 설명합니다. 


## <a name="run-diagnostics"></a>진단 실행

디바이스 오류를 진단하고 해결하기 위해 진단 테스트를 실행합니다. 진단 테스트를 실행하려면 디바이스의 로컬 웹 UI에서 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **문제 해결 > 진단 테스트** 로 이동합니다. 실행할 테스트를 선택하고 **테스트 실행** 을 선택합니다. 테스트를 통해 네트워크, 디바이스, 웹 프록시, 시간 또는 클라우드 설정에서 문제가 될만한 내용을 진단합니다. 디바이스에서 테스트가 실행 중이라는 알림이 표시됩니다.

    ![테스트 선택 ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. 테스트가 완료된 후 결과가 표시됩니다. 

    ![테스트 결과 보기](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    테스트가 실패하면 권장 조치에 대한 URL이 표시됩니다. URL을 선택하여 권장 작업을 확인합니다.
 
    ![실패한 테스트에 대한 경고 살펴보기](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>지원 패키지 수집

로크 패키지는 Microsoft 지원에서 디바이스 문제를 해결하는 데 도움을 줄 수 있는 모든 관련 로그로 구성됩니다. 로그 패키지는 로컬 웹 UI를 통해 생성할 수 있습니다.

지원 패키지를 수집하려면 다음 단계를 수행합니다. 

1. 로컬 웹 UI에서 **문제 해결 > 지원** 으로 이동합니다. **지원 패키지 만들기** 를 선택합니다. 시스템이 지원 패키지를 수집하기 시작합니다. 패키지 수집에는 몇 분 정도 걸릴 수 있습니다.

    ![사용자 추가 선택](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. 지원 패키지가 생성된 후에는 **지원 패키지 다운로드** 를 선택합니다. 압축된 패키지가 선택한 경로에 다운로드됩니다. 패키지의 압축을 풀면 시스템 로그 파일을 볼 수 있습니다.

    ![사용자 2 추가 선택](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>고급 보안 로그 수집

고급 보안 로그는 Azure Stack Edge Pro 디바이스에 관한 소프트웨어 또는 하드웨어 침입 로그일 수 있습니다.

### <a name="software-intrusion-logs"></a>소프트웨어 침입 로그

소프트웨어 침입 또는 기본 방화벽 로그는 인바운드 및 아웃바운드 트래픽에 대해 수집됩니다. 

- 디바이스가 센터에서 이미지화되면 기본 방화벽 로깅이 사용으로 설정됩니다. 이 로그는 로컬 UI 또는 디바이스의 Windows PowerShell 인터페이스를 통해 지원 패키지를 만들 때 기본적으로 지원 패키지에 번들됩니다.

- 디바이스의 소프트웨어(NW) 침입을 검토하기 위해 지원 패키지에 방화벽 로그만 필요한 경우 지원 패키지를 만들 때 `-Include FirewallLog` 옵션을 사용합니다. 

- 특정 포함 옵션이 제공되지 않으면 방화벽 로그가 지원 패키지에 기본값으로 포함됩니다.

- 지원 패키지에서 방화벽 로그는 `pfirewall.log`이며 루트 폴더에 있습니다. 다음은 Azure Stack Edge Pro 디바이스의 소프트웨어 침입 로그 예제입니다. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>하드웨어 침입 로그

디바이스에 대한 하드웨어 침입을 탐지하기 위해 섀시 열기 또는 닫기와 같은 모든 섀시 이벤트가 현재 로그됩니다. 

- 디바이스의 시스템 이벤트 로그는 `racadm` cmdlet을 사용하여 읽습니다. 그런 다음 이 이벤트는 `HWIntrusion.txt` 파일에서 섀시 관련 이벤트를 기준으로 필터링됩니다.

- 지원 패키지에서 하드웨어 침입 로그만 가져오려면 지원 패키지를 만들 때 `-Include HWSelLog` 옵션을 사용합니다. 

- 특정 포함 옵션이 제공되지 않으면 하드웨어 침입 로그가 지원 패키지에 기본값으로 포함됩니다.

- 지원 패키지에서 하드웨어 침입 로그는 `HWIntrusion.txt`이며 루트 폴더에 있습니다. 다음은 Azure Stack Edge Pro 디바이스의 하드웨어 침입 로그 예제입니다. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>로그를 사용하여 문제 해결

업로드 및 새로 고침 프로세스 중에 발생한 모든 오류는 해당 오류 파일에 포함됩니다.

1. 오류 파일을 보려면 공유로 이동하여 공유를 선택하고 콘텐츠를 봅니다. 


2. _Microsoft Data Box Edge 폴더_ 를 선택합니다. 이 폴더에는 두 개의 하위 폴더가 있습니다.

    - 업로드 오류에 대한 로그 파일이 있는 Upload 폴더
    - 새로 고침 중 오류를 위한 Refresh 폴더

    다음은 새로 고침에 대한 샘플 로그 파일입니다.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. 이 파일에 오류가 표시되면(샘플에 강조 표시됨), 오류 코드를 적어두십시오. 이 경우에는 16001입니다. 다음 오류 참조에서 이 오류 코드에 대한 설명을 찾아봅니다.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>오류 목록을 사용하여 문제 해결

오류 목록은 식별된 시나리오에서 컴파일되며 자체 진단 및 문제 해결에 사용할 수 있습니다. 

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

디바이스에 액세스하기 위해 Azure Resource Manager를 구성하는 동안 표시될 수 있는 오류는 다음과 같습니다. 

| **문제/오류** |  **해결 방법** | 
|------------|-----------------|
|일반적인 문제|<li>[Edge 디바이스가 올바르게 구성되었는지 확인](#verify-the-device-is-configured-properly)합니다.<li> [클라이언트가 올바르게 구성되어 있는지 확인](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: 요청을 보내는 중 오류가 발생했습니다.<br>줄:1 문자:1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|이 오류는 Azure Stack Edge Pro 디바이스에 연결할 수 없거나 제대로 구성되지 않았음을 의미합니다. Edge 디바이스 및 클라이언트가 올바르게 구성되어 있는지 확인합니다. 참고 자료는 이 표의 **일반 문제** 행을 참조하세요.|
|서비스에서 오류를 반환했습니다. 자세한 내용은 InnerException 확인: 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대한 트러스트 관계를 설정할 수 없습니다. |   이 오류는 하나 이상의 자체 인증서 가져 오기 단계가 잘못 수행되었기 때문일 가능성이 큽니다. [여기](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)서 참고 자료를 찾을 수 있습니다. |
|작업이 잘못된 상태 코드 ‘ServiceUnavailable’를 반환함 <br> 응답 상태 코드가 성공을 나타내지 않음: 503(서비스를 사용할 수 없음). | 이 오류는 해당 조건의 결과일 수 있습니다.<li>ArmStsPool이 중지됨 상태입니다.</li><li>Azure Resource Manager/보안 토큰 서비스 웹 사이트 중 하나가 다운되었습니다.</li><li>Azure Resource Manager 클러스터 리소스가 다운되었습니다.</li><br><strong>참고:</strong>  어플라이언스를 다시 시작하면 문제가 해결될 수 있지만, 추가로 디버그할 수 있도록 지원 패키지를 수집해야 합니다.|
|AADSTS50126: 사용자 이름 또는 암호가 잘못되었습니다.<br>추적 ID: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>상관 관계 ID: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>타임스탬프: 2019-11-15 09:21:57Z: 원격 서버가 오류를 리턴함: (400) 잘못된 요청입니다.<br>줄:1 문자:1 |이 오류는 해당 조건의 결과일 수 있습니다.<li>잘못된 사용자 이름과 암호의 경우 [여기](/azure/azure-stack-edge-gpu-set-azure-resource-manager-password)의 단계를 수행한 다음, 올바른 암호를 사용하여 고객이 Azure Portal에서 암호를 변경했는지 유효성을 검사합니다.<li>잘못된 테넌트 ID의 경우 테넌트 ID는 고정 GUID이며 `c0257de7-538f-415c-993a-1b87a031879d`로 설정해야 합니다.</li>|
|connect-AzureRmAccount: AADSTS90056: 리소스가 사용하지 않게 설정되었거나 없습니다. 앱 코드를 확인하여 액세스하려는 리소스의 정확한 리소스 URL을 지정했는지 확인하세요.<br>추적 ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>상관 관계 ID: 75c8ef5a-830e-48b5-b039-595a96488ff9 타임스탬프: 2019-11-18 07:00:51Z: 원격 서버에서 오류를 반환함: (400) 잘못됨 |`Add-AzureRmEnvironment` 명령에 사용된 리소스 엔드포인트가 잘못되었습니다.|
|클라우드에서 엔드포인트를 가져올 수 없습니다.<br>네트워크에 연결되어 있는지 확인하세요. 오류 세부 정보: HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005): URL로 최대 재시도 초과: /metadata/endpoints?api-version=2015-01-01 (Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |이 오류는 대부분 Mac/Linux 환경에서 표시되며 다음 문제 때문에 발생합니다.<li>PEM 형식 인증서가 python 인증서 저장소에 추가되지 않았습니다.</li> |

### <a name="verify-the-device-is-configured-properly"></a>디바이스가 올바르게 구성되었는지 확인합니다.

1. 로컬 UI에서 디바이스 네트워크가 올바르게 구성되어 있는지 확인합니다.

2. [여기](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)에 언급된 대로 모든 엔드포인트의 인증서가 업데이트되었는지 확인합니다.

3. 로컬 UI의 **디바이스** 페이지에서 Azure Resource Manager 관리 및 로그인 엔드포인트를 가져옵니다.

4. 디바이스가 활성화되고 Azure에 등록되었는지 확인합니다.


### <a name="verify-the-client-is-configured-properly"></a>클라이언트가 올바르게 구성되어 있는지 확인

1. [여기](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client)에 설명된 대로 올바른 PowerShell 버전이 설치되어 있는지 유효성을 검사합니다.

2. [여기](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)에 설명된 대로 올바른 PowerShell 모듈이 설치되어 있는지 유효성을 검사합니다.

3. Azure Resource Manager 및 로그인 엔드포인트에 연결할 수 있는지 유효성을 검사합니다. 엔드포인트에 ping을 시도할 수 있습니다. 예를 들면 다음과 같습니다.

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   연결할 수 없는 경우 [여기](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)에 설명된 대로 DNS/호스트 파일 항목을 추가합니다.
   
4. [여기](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)에 설명된 대로 클라이언트 인증서가 설치되어 있는지 유효성을 검사합니다.

5. 고객이 PowerShell을 사용하는 경우 이 PowerShell 명령을 실행하여 자세한 메시지를 보려면 디버그 기본 설정을 사용으로 설정해야 합니다. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>디바이스의 Blob Storage 

다음은 Azure Stack Edge Pro/ Data Box Gateway 디바이스의 Blob Storage와 관련된 오류입니다.

| **문제/오류** |  **해결 방법** | 
|--------------------|-----------------|
|자식 리소스를 검색할 수 없습니다. HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.| **편집** 메뉴에서 **대상 Azure Stack API** 를 선택합니다. 그런 다음 Azure Storage Explorer를 다시 시작합니다.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 Windows의 경우 `C:\Windows\System32\drivers\etc\hosts` 또는 Linux의 경우 `/etc/hosts` 경로의 호스트 파일에 추가되었는지 확인합니다.|
|자식 리소스를 검색할 수 없습니다.<br> 세부 정보: 자체 서명된 인증서 |디바이스에 대한 SSL 인증서를 Azure Storage Explorer로 가져옵니다. <ol><li>Azure Portal에서 인증서를 다운로드합니다. 자세한 내용은 [인증서 다운로드](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)를 참조하세요.</li><li>**편집** 메뉴에서 SSL 인증서를 선택한 다음, **인증서 가져오기** 를 선택합니다.</li></ol>|
|다음 오류를 표시하기 전에 AzCopy 명령은 잠시 응답을 중지한 것처럼 표시됩니다.<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 `C:\Windows\System32\drivers\etc\hosts`의 호스트 파일에 추가되었는지 확인합니다.|
|다음 오류를 표시하기 전에 AzCopy 명령은 잠시 응답을 중지한 것처럼 표시됩니다.<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |디바이스의 SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)를 참조하세요.|
|다음 오류를 표시하기 전에 AzCopy 명령은 20분 동안 응답을 중지한 것처럼 표시됩니다.<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 `/etc/hosts`의 호스트 파일에 추가되었는지 확인합니다.|
|다음 오류를 표시하기 전에 AzCopy 명령은 20분 동안 응답을 중지한 것처럼 표시됩니다.<br>`Error parsing source location… The SSL connection could not be established`. |디바이스의 SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)를 참조하세요.|
|다음 오류를 표시하기 전에 AzCopy 명령은 20분 동안 응답을 중지한 것처럼 표시됩니다.<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 `/etc/hosts`의 호스트 파일에 추가되었는지 확인합니다.|
|이 오류를 표시하기 전에 AzCopy 명령이 20분 동안 응답하지 않는 것으로 나타납니다. `Error parsing source location… The SSL connection could not be established`.|디바이스의 SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)를 참조하세요.|
|HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.|Data Box에서 Python용 Microsoft Azure Storage 라이브러리의 설치된 버전이 지원되지 않습니다. 지원되는 버전에 대한 Azure Data Box Blob Storage 요구 사항을 참조하세요.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …| Python을 실행하기 전에 REQUESTS_CA_BUNDLE 환경 변수를 Base64로 인코딩된 SSL 인증서 파일의 경로로 설정합니다([인증서 다운로드](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate) 방법 참조). 예를 들면 다음과 같습니다.<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>또는 인증서를 시스템의 인증서 저장소에 추가한 다음 이 환경 변수를 해당 저장소의 경로로 설정합니다. 예를 들어 Ubuntu에서 <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|연결 시간 초과|Azure Stack Edge Pro에 로그인한 다음, 잠금 해제되어 있는지 확인합니다. 디바이스가 다시 시작될 때마다 사용자가 로그인할 때까지 잠긴 상태로 유지됩니다.|

## <a name="troubleshoot-iot-edge-errors"></a>IoT Edge 오류 문제 해결

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>다음 단계

- [디바이스 활성화 문제를 해결](azure-stack-edge-gpu-troubleshoot-activation.md)하는 방법에 관해 자세히 알아보세요.
