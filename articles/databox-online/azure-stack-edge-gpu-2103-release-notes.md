---
title: Azure Stack Edge Pro 2103 릴리스 정보
description: 2103 릴리스를 실행 하는 Azure Stack Edge Pro에 대 한 중요 한 미해결 문제 및 해결 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 846d4a259f0fcd204bcad6c898efc999c3765fd3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962683"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Azure Stack Edge 2103 릴리스 정보

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

다음 릴리스 정보는 Azure Stack Edge 장치의 2103 릴리스에 대 한 중요 한 미해결 문제 및 해결 된 문제를 식별 합니다. 이러한 릴리스 정보는 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 장치에 적용 됩니다. 특정 모델에 해당 하는 기능 및 문제는 해당 하는 경우에 호출 됩니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. 장치를 배포 하기 전에 릴리스 정보에 포함 된 정보를 주의 깊게 검토 하세요.

이 문서는 소프트웨어 버전 번호 **2.2.1540.2890** 에 매핑되는 **Azure Stack Edge 2103** 릴리스에 적용 됩니다. 2.1.1377.2170 (Azure Stack Edge 2010) 소프트웨어를 하나 이상 실행 하는 경우이 소프트웨어를 장치에 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

Azure Stack Edge 2103 릴리스에서는 다음과 같은 새로운 기능을 사용할 수 있습니다. 
 
- **Virtual Machines의 새로운 기능** -이 릴리스를 시작 하면 Azure Portal를 통해 가상 머신에서 다음과 같은 관리 작업을 수행할 수 있습니다.
    - 기존 VM에 여러 네트워크 인터페이스를 추가 하거나 제거 합니다.
    - 기존 VM에 여러 디스크를 추가 하거나 제거 합니다.
    - VM의 크기를 조정 합니다.
    - Windows 또는 Linux VM을 배포 하는 동안 사용자 지정 데이터를 추가 합니다.

  [장치에서 vm 콘솔에 연결](azure-stack-edge-gpu-connect-virtual-machine-console.md) 하 고 vm 문제를 해결할 수도 있습니다.

- **Https를 통해 PowerShell 인터페이스에 연결** -이 릴리스를 시작 하면 *http* 를 통해 장치에 대 한 원격 PowerShell 세션을 더 이상 열 수 없게 됩니다. 기본적으로 모든 세션에는 *https* 가 사용 됩니다. 자세한 내용은 장치의 [PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 하는 방법을 참조 하세요.

- **계산에 대 한 개선 사항** -다음과 같은 기능을 비롯 한 몇 가지 향상 된 기능과 향상 된 기능이 있습니다.

    - **전체 계산 플랫폼 품질**. 이 릴리스에는 전체 계산 플랫폼 품질을 향상 시키기 위한 버그 수정이 포함 되어 있습니다. [2103 릴리스에서 해결 된 문제](#issues-fixed-in-2103-release)를 참조 하세요. 
    - **계산 플랫폼 구성 요소**. 보안 업데이트가 계산 VM 이미지에 적용 되었습니다. Kubernetes 버전에 대 한 IoT Edge 및 Azure Arc도 업데이트 되었습니다.
    - **진단**. 리소스 및 네트워크 조건을 확인 하기 위해 새 API가 릴리스됩니다. 장치의 PowerShell 인터페이스에 연결 하 고 `Test-HcsKubernetesStatus` 명령을 사용 하 여 장치의 네트워크 준비 상태를 확인할 수 있습니다.
    - 디버깅을 향상 시킬 수 있는 **로그 컬렉션** 입니다. 
    - 계산 IP 주소에 대 한 IP 주소 충돌을 검색 하는 데 사용할 수 있는 **경고 인프라** 입니다. 
    - Kubernetes 및 로컬 Azure Resource Manager **작업을 혼합** 합니다. 

- **기본적으로 자동 관리 로깅** -이 릴리스를 시작 하면 자동 관리 로그 수집이 장치에서 기본적으로 사용 하도록 설정 됩니다. 이 기능을 통해 Microsoft는 시스템 상태 표시기에 따라 사전에 로그를 수집 하 여 장치 문제를 효과적으로 해결할 수 있습니다. 자세한 내용은 [장치의 자동 관리 로그 수집](azure-stack-edge-gpu-proactive-log-collection.md)을 참조 하세요.

## <a name="issues-fixed-in-2103-release"></a>2103 릴리스에서 해결 된 문제

다음 표에서는 이전 릴리스에서 발표 된 릴리스와 현재 릴리스에서 수정 된 문제를 보여 줍니다.

| 아니요. | 기능 | 문제 | 
| --- | --- | --- |
|**1.**|Kubernetes |웹 프록시를 사용 하는 경우 Edge 컨테이너 레지스트리가 작동 하지 않습니다.|
|**2.**|Kubernetes |Edge 컨테이너 레지스트리는 IoT Edge 모듈에서 작동 하지 않습니다.| 

## <a name="known-issues-in-2103-release"></a>2103 릴리스의 알려진 문제

다음 표에서는 2103 릴리스의 알려진 문제에 대 한 요약을 제공 합니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
|**1.**|미리 보기 기능 |이 릴리스의 경우 로컬 Azure Resource Manager, Vm, Vm의 클라우드 관리, Kubernetes 클라우드 관리, Azure Arc enabled Kubernetes, Azure Stack Edge Pro R 용 VPN 및 Azure Stack Edge 미니 R 용 VPN, Azure Stack Edge Pro GPU 용 MP (다중 프로세스 서비스)를 모두 미리 보기에서 사용할 수 있습니다.  |이러한 기능은 이후 릴리스에서 일반적으로 제공 될 예정입니다. |
|**2.**|GPU Vm |이 릴리스 이전에는 GPU VM 수명 주기가 업데이트 흐름에서 관리 되지 않았습니다. 따라서 2103 릴리스로 업데이트 하는 경우 업데이트 하는 동안 GPU Vm이 자동으로 중지 되지 않습니다. 장치를 업데이트 하기 전에 플래그를 사용 하 여 GPU Vm을 수동으로 중지 해야 `stop-stayProvisioned` 합니다. 자세한 내용은 [VM 일시 중단 또는 종료](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm)를 참조 하세요.<br> 업데이트 전에 실행 되는 모든 GPU Vm은 업데이트 후에 시작 됩니다. 이러한 경우 Vm에서 실행 되는 워크 로드가 정상적으로 종료 되지 않습니다. 그리고 업데이트 후에도 Vm이 원치 않는 상태가 될 수 있습니다. <br>업데이트 전에를 통해 중지 된 모든 GPU Vm은 `stop-stayProvisioned` 업데이트 후 자동으로 시작 됩니다. <br>Azure Portal를 통해 GPU Vm을 중지 하는 경우 장치 업데이트 후 VM을 수동으로 시작 해야 합니다.| Kubernetes를 사용 하 여 GPU Vm을 실행 하는 경우 업데이트 바로 전에 GPU Vm을 중지 합니다. <br>GPU Vm이 중지 되 면 Kubernetes는 원래 Vm에서 사용 된 Gpu를 사용 합니다. <br>더 긴 GPU Vm은 중지 된 상태 이므로 Kubernetes는 Gpu를 통해 수행할 가능성이 높습니다. |
|**3.**|사용자 지정 스크립트 VM 확장 |이전 릴리스에서 생성 된 Windows Vm에 알려진 문제가 있으며 장치가 2103로 업데이트 되었습니다. <br> 이러한 Vm에 사용자 지정 스크립트 확장을 추가 하면 Windows VM 게스트 에이전트 (버전 2.7.41491.901 해당)가 업데이트에서 중단 되어 확장 배포 시간이 초과 됩니다. | 이 문제를 해결하려면 <ul><li> RDP (원격 데스크톱 프로토콜)를 사용 하 여 Windows VM에 연결 합니다. </li><li> `waappagent.exe`가 컴퓨터에서 실행 되 고 있는지 확인 `Get-Process WaAppAgent` 합니다. </li><li> `waappagent.exe`가 실행 되 고 있지 않으면 서비스를 다시 시작 `rdagent` `Get-Service RdAgent` \| `Restart-Service` 합니다. 5 분 동안 기다립니다.</li><li> `waappagent.exe`가 실행 되는 동안 프로세스를 중지 `WindowsAzureGuest.exe` 합니다. </li><li>프로세스를 중지 하면 프로세스가 최신 버전으로 다시 실행 되기 시작 합니다.</li><li>다음 명령을 사용 하 여 Windows VM 게스트 에이전트 버전이 2.7.41491.971 인지 확인 `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` 합니다.</li><li>[WINDOWS VM에서 사용자 지정 스크립트 확장을 설정](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md)합니다. </li><ul> | 
|**4.**|MP (다중 프로세스 서비스) |장치 소프트웨어 및 Kubernetes 클러스터가 업데이트 되 면 작업에 대 한 MPS 설정이 유지 되지 않습니다.   |Mp를 [다시 사용 하도록 설정](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 하 고 mp를 사용 하는 워크 로드를 다시 배포 합니다. |


## <a name="known-issues-from-previous-releases"></a>이전 릴리스의 알려진 문제

다음 표에서는 이전 릴리스에서 수행 된 알려진 문제에 대 한 요약을 제공 합니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | SQL database를 만들려면 관리자 액세스 권한이 필요 합니다.   |의 1-2 단계 대신 다음 단계를 수행 [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) 합니다. <ul><li>장치의 로컬 UI에서 계산 인터페이스를 사용 하도록 설정 합니다. Compute **> 포트 # > 계산 > 적용을 선택 합니다.**</li><li>`sqlcmd`클라이언트 컴퓨터에서 다운로드https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>계산 인터페이스 IP 주소 (사용 하도록 설정 된 포트)에 연결 하 여 주소 끝에 ", 1401"을 추가 합니다.</li><li>최종 명령은 sqlcmd-S {Interface IP}, 1401-U SA-P "와 같습니다. Passw0rd ".</li>그러면 현재 설명서의 3-4 단계가 동일 해야 합니다. </li></ul> |
| **2.** |새로 고침| **새로 고침** 을 통해 복원 된 blob에 대 한 증분 변경은 지원 되지 않습니다. |Blob 끝점의 경우 새로 고침 후 blob의 부분 업데이트로 인해 업데이트가 클라우드에 업로드 되지 않을 수 있습니다. 예를 들어 다음과 같은 작업의 시퀀스입니다.<ul><li>클라우드에서 blob을 만듭니다. 또는 장치에서 이전에 업로드 된 blob을 삭제 합니다.</li><li>새로 고침 기능을 사용 하 여 클라우드에서 blob을 어플라이언스로 새로 고칩니다.</li><li>Azure SDK REST Api를 사용 하 여 blob의 일부만 업데이트 합니다.</li></ul>이러한 작업으로 인해 blob의 업데이트 된 섹션이 클라우드에서 업데이트 되지 않을 수 있습니다. <br>**해결 방법**: robocopy와 같은 도구를 사용 하거나 탐색기 또는 명령줄을 통해 일반 파일 복사를 사용 하 여 전체 blob을 바꿉니다.|
|**3.**|제한|조정 하는 동안 장치에 대 한 새 쓰기가 허용 되지 않는 경우 NFS 클라이언트의 쓰기가 "권한 거부" 오류로 인해 실패 합니다.| 오류는 다음과 같이 표시 됩니다.<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: ' test ' 디렉터리를 만들 수 없습니다. 사용 권한이 거부 되었습니다.|
|**4.**|Blob Storage 수집|Blob storage 수집에 AzCopy 버전 10을 사용 하는 경우 다음 인수를 사용 하 여 AzCopy를 실행 합니다. `Azcopy <other arguments> --cap-mbps 2000`| AzCopy에 대해 이러한 제한을 제공 하지 않으면 장치에 많은 요청을 전송 하 여 서비스에 문제가 발생할 수 있습니다.|
|**5.**|계층화 된 저장소 계정|계층화 된 저장소 계정을 사용 하는 경우 다음이 적용 됩니다.<ul><li> 블록 blob만 지원 됩니다. 페이지 Blob은 지원되지 않습니다.</li><li>스냅숏 또는 복사 API는 지원 되지 않습니다.</li><li> 에서 `distcp` 복사 작업을 많이 사용 하므로 Hadoop 워크 로드 수집은 지원 되지 않습니다.</li></ul>||
|**6.**|NFS 공유 연결|여러 프로세스에서 동일한 공유로 복사 하 고 특성을 사용 하지 않는 경우 복사 하는 `nolock` 동안 오류가 표시 될 수 있습니다.|`nolock`NFS 공유에 파일을 복사 하려면 특성을 탑재 명령에 전달 해야 합니다. 예를 들어 `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`을 참조하십시오.|
|**7.**|Kubernetes 클러스터|Kubernetes 클러스터를 실행 하는 장치에 업데이트를 적용 하는 경우 Kubernetes 가상 머신이 다시 시작 되 고 재부팅 됩니다. 이 인스턴스에서 지정 된 복제본과 함께 배포 되는 pod만 업데이트 후 자동으로 복원 됩니다.  |복제 세트를 지정 하지 않고 복제 컨트롤러 외부에서 개별 pod를 만든 경우 이러한 pod는 장치 업데이트 후 자동으로 복원 되지 않습니다. 이러한 pod를 복원 해야 합니다.<br>복제 세트는 노드 실패 또는 중단 노드 업그레이드와 같은 어떤 이유로 든 삭제 되었거나 종료 된 pod를 대체 합니다. 따라서 응용 프로그램에 단일 pod만 필요한 경우에도 복제본 집합을 사용 하는 것이 좋습니다.|
|**8.**|Kubernetes 클러스터|Kubernetes on Azure Stack Edge Pro는 투구 v3 이상 에서만 지원 됩니다. 자세한 내용은 질문과 [대답: Tiller 제거](https://v3.helm.sh/docs/faq/)를 참조 하세요.|
|**9.**|Azure Arc를 지원하는 Kubernetes |GA 릴리스의 경우 Azure Arc enabled Kubernetes는 버전 0.1.18에서 0.2.9로 업데이트 됩니다. Azure Arc enabled Kubernetes 업데이트는 Azure Stack Edge 장치에서 지원 되지 않으므로 Azure Arc enabled Kubernetes를 다시 배포 해야 합니다.|다음 단계를 수행합니다.<ol><li>[장치 소프트웨어 및 Kubernetes 업데이트를 적용](azure-stack-edge-gpu-install-update.md)합니다.</li><li>[장치의 PowerShell 인터페이스](azure-stack-edge-gpu-connect-powershell-interface.md)에 연결 합니다.</li><li>기존 Azure Arc 에이전트를 제거 합니다. 다음을 입력합니다. `Remove-HcsKubernetesAzureArcAgent`</li><li>[Azure Arc를 새 리소스에](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)배포 합니다. 기존 Azure Arc 리소스를 사용 하지 마세요.</li></ol>|
|**5-10.**|Azure Arc를 지원하는 Kubernetes|웹 프록시가 Azure Stack Edge Pro 장치에 구성 된 경우 Azure Arc 배포가 지원 되지 않습니다.||
|**pt.**|Kubernetes |포트 31000은 Kubernetes 대시보드에 예약 되어 있습니다. 포트 31001은 Edge 컨테이너 레지스트리에 대해 예약 되어 있습니다. 마찬가지로 기본 구성에서 IP 주소 172.28.0.1와 172.28.0.10는 각각 Kubernetes service 및 Core DNS 서비스용으로 예약 되어 있습니다.|예약 된 Ip를 사용 하지 마십시오.|
|**10.**|Kubernetes |Kubernetes는 현재 다중 프로토콜 LoadBalancer 서비스를 허용 하지 않습니다. 예를 들어 TCP와 UDP 모두에서 수신 대기 해야 하는 DNS 서비스입니다. |MetalLB를 사용 하는 Kubernetes의이 제한 사항을 해결 하기 위해 동일한 pod 선택기에 두 가지 서비스 (TCP에 대해 하나, 하나는 UDP 용)를 만들 수 있습니다. 이러한 서비스는 동일한 공유 키와 loadBalancerIP를 사용 하 여 동일한 IP 주소를 공유 합니다. 사용 가능한 IP 주소 보다 많은 서비스를 사용 하는 경우 ip를 공유할 수도 있습니다. <br> 자세한 내용은 [IP 주소 공유](https://metallb.universe.tf/usage/#ip-address-sharing)를 참조 하세요.|
|**/10.**|Kubernetes 클러스터|기존 Azure IoT Edge marketplace 모듈은 Azure Stack Edge 장치에서 IoT Edge을 실행 하기 위해 수정이 필요할 수 있습니다.|자세한 내용은 marketplace에서 Azure IoT Edge 모듈을 수정 하 여 Azure Stack Edge 장치에서 실행을 참조 하세요.<!-- insert link-->|
|**14.**|Kubernetes |파일 기반 바인드 탑재는 Azure Stack에 지 장치의 Kubernetes에서 Azure IoT Edge 지원 되지 않습니다.|IoT Edge는 번역 계층을 사용 하 여 `ContainerCreate` 옵션을 Kubernetes 구문으로 변환 합니다. `Binds`디렉터리에 대 한 맵을 만드는 중 이므로 `hostpath` 파일 기반 바인드 탑재는 IoT Edge 컨테이너의 경로에 바인딩할 수 없습니다. 가능 하면 부모 디렉터리를 매핑합니다.|
|**15.**|Kubernetes |IoT Edge에 대 한 자체 인증서를 가져와서 장치에서 계산을 구성한 후 Azure Stack에 지 장치에 해당 인증서를 추가 하는 경우 새 인증서가 선택 되지 않습니다.|이 문제를 해결 하려면 장치에서 계산을 구성 하기 전에 인증서를 업로드 해야 합니다. 계산이 이미 구성 되어 있으면 [장치의 PowerShell 인터페이스에 연결 하 고 IoT Edge 명령을 실행](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands)합니다. `iotedged`Pod를 다시 시작 `edgehub` 합니다.|
|**x.**|인증서 |특정 한 경우 로컬 UI에서 인증서 상태를 업데이트 하는 데 몇 초 정도 걸릴 수 있습니다. |로컬 UI의 다음 시나리오에 영향을 줄 수 있습니다.<ul><li>**인증서** 페이지의 **상태** 열입니다.</li><li>**시작** 페이지의 **보안** 타일입니다.</li><li>**개요** 페이지의 **구성** 타일입니다.</li></ul>  |
|**17.**|IoT Edge |IoT Edge를 통해 배포 된 모듈은 호스트 네트워크를 사용할 수 없습니다. | |
|**개가.**|Compute + Kubernetes |Compute/Kubernetes는 NTLM 웹 프록시를 지원 하지 않습니다. ||
|**mb.**|Kubernetes + 업데이트 |2008 릴리스와 같은 이전 버전의 소프트웨어 버전에는 업데이트를 ClusterConnectionException과 함께 실패 시키는 경합 상태 업데이트 문제가 있습니다. |최신 빌드를 사용 하면이 문제를 방지할 수 있습니다. 이 문제가 계속 발생 하는 경우 해결 방법은 업그레이드를 다시 시도 하 고 작동 해야 합니다.|
|**20**|Internet Explorer|향상 된 보안 기능을 사용 하는 경우 로컬 웹 UI 페이지에 액세스할 수 없습니다. | 보안 강화를 사용 하지 않도록 설정 하 고 브라우저를 다시 시작 합니다.|
|**일.**|Kubernetes 대시보드 | SSL 인증서가 포함 된 Kubernetes 대시보드의 *Https* 끝점은 지원 되지 않습니다. | |
|**가로.**|Kubernetes |Kubernetes는 .NET 응용 프로그램에서 사용 하는 환경 변수 이름에서 ":"을 지원 하지 않습니다. 이는 Event grid IoT Edge 모듈이 Azure Stack Edge 장치 및 기타 응용 프로그램에서 작동 하는 데도 필요 합니다. 자세한 내용은 [ASP.NET core 설명서](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables)를 참조 하세요.|":"을 이중 밑줄로 바꿉니다. 자세한 내용은 [Kubernetes issue](https://github.com/kubernetes/kubernetes/issues/53201) 항목을 참조 하세요.|
|**불가능.** |Azure Arc + Kubernetes 클러스터 |기본적으로 `yamls` Git 리포지토리에서 리소스를 삭제 하면 해당 리소스가 Kubernetes 클러스터에서 삭제 되지 않습니다.  |Git 리포지토리에서 삭제 될 때 리소스 삭제를 허용 하려면 `--sync-garbage-collection` 호 OperatorParams에서를 설정 합니다. 자세한 내용은 [구성 삭제](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters)를 참조 하세요. |
|**24.**|NFS |장치에서 데이터를 쓰기 위해 NFS 공유 탑재를 사용 하는 응용 프로그램은 단독 쓰기를 사용 해야 합니다. 이렇게 하면 쓰기를 디스크에 쓸 수 있습니다.| |
|**가지의.**|계산 구성 |네트워크에 존재 하지 않는 시스템에 대 한 게이트웨이 또는 스위치 또는 라우터가 ARP (주소 확인 프로토콜) 요청에 응답 하는 네트워크 구성에서 계산 구성이 실패 합니다.| |
|**26.**|Compute 및 Kubernetes |Kubernetes가 장치에 처음으로 설정 된 경우 사용 가능한 모든 Gpu를 클레임 합니다. 따라서 Kubernetes를 설정한 후에는 Gpu를 사용 하 여 Azure Resource Manager Vm을 만들 수 없습니다. |장치에 2 개의 Gpu가 있는 경우 GPU를 사용 하는 VM 1 개를 만든 다음 Kubernetes을 구성할 수 있습니다. 이 경우 Kubernetes는 사용 가능한 나머지 1 개의 GPU를 사용 합니다. |


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>다음 단계

- [디바이스 업데이트](azure-stack-edge-gpu-install-update.md)
