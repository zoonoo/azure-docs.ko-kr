---
title: Azure Stack Edge 미리 보기 릴리스 정보| Microsoft Docs
description: 미리 보기 사용 가능 릴리스가 실행 중인 Azure Stack Edge에 대한 주요 미해결 문제 및 해결 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: bf41c34b205bc9dd7951ad9910c8a9c6df62104d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460878"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>GPU를 사용하는 Azure Stack Edge Pro 미리 보기 릴리스 정보

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

다음 릴리스 정보에서는 GPU를 사용하는 Azure Stack Edge Pro 디바이스용 2008 미리 보기 릴리스에 대한 중요한 미해결 문제와 해결된 문제를 설명합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Azure Stack Edge Pro 디바이스를 배포하기 전에 릴리스 정보에 수록된 정보를 주의 깊게 검토하세요.

이 문서는 소프트웨어 릴리스인 **Azure Stack Edge Pro 2008** 에 적용됩니다.

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>새로운 기능

Azure Stack Edge 2008 릴리스에는 다음과 같은 새로운 기능이 추가되었습니다. 실행 중인 특정 미리 보기 소프트웨어 버전에 따라 이러한 기능의 하위 집합이 표시될 수 있습니다. 

- **스토리지 클래스** - 이전 릴리스에서는 Azure Stack Edge Pro 디바이스에서 실행되는 Kubernetes 클러스터에 배포된 상태 저장 애플리케이션에 대해 SMB 또는 NFS 공유를 통해서만 스토리지를 정적으로 프로비전할 수 있었습니다. 이 릴리스에서는 스토리지를 동적으로 프로비전할 수 있는 스토리지 클래스가 추가되었습니다. 자세한 내용은 [Azure Stack Edge Pro GPU 디바이스에서 Kubernetes 스토리지 관리](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning)를 참조하세요. 
- **메트릭 서버가 있는 Kubernetes 대시보드** - 이 릴리스에서는 메트릭 서버 추가 기능과 함께 Kubernetes 대시보드가 추가됩니다. 대시보드를 사용하여 Azure Stack Edge Pro 디바이스에서 실행되는 애플리케이션의 개요를 확인하고, Kubernetes 클러스터 리소스의 상태를 보고, 디바이스에서 발생한 오류를 확인할 수 있습니다. 메트릭 서버는 디바이스의 Kubernetes 리소스에서 CPU 및 메모리 사용량을 집계합니다. 자세한 내용은 [Kubernetes 대시보드를 사용하여 Azure Stack Edge PRO GPU 장치 모니터링](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)을 참조하세요.
- **Azure Stack Edge Pro Azure Arc** - 이 릴리스부터는 Azure Arc를 통해 Azure Stack Edge Pro 디바이스에 애플리케이션 워크로드를 배포할 수 있습니다. Azure Arc는 Kubernetes 클러스터에 애플리케이션을 배포할 수 있는 하이브리드 관리 도구입니다. 자세한 내용은 [Azure Stack Edge Pro 디바이스에서 Azure Arc를 통해 워크로드 배포](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)를 참조하세요.  

## <a name="known-issues"></a>알려진 문제 

다음 표에서는 Azure Stack Edge Pro 디바이스의 알려진 문제에 대한 요약을 제공합니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | SQL 데이터베이스를 만들려면 관리자 액세스 권한이 필요합니다.   |[https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database)의 1-2단계 대신, 다음 단계를 수행합니다. <ul><li>디바이스의 로컬 UI에서 컴퓨팅 인터페이스를 사용하도록 설정합니다. **컴퓨팅 > 포트 번호 > 컴퓨팅에 사용 > 적용** 을 선택합니다.</li><li>클라이언트 컴퓨터에 [sqlcmd utility](/sql/tools/sqlcmd-utility)를 다운로드합니다. </li><li>컴퓨팅 인터페이스 IP 주소에 연결하고(사용하도록 설정된 포트) 주소 끝에 “,1401”을 추가합니다.</li><li>최종 명령은 sqlcmd -S {Interface IP},1401 -U SA -P “Strong!Passw0rd”와 비슷합니다.</li>그 이후에 현재 설명서의 3-4단계가 같아야 합니다. </li></ul> |
| **2.** |새로 고침| **새로 고침** 을 통해 복원된 Blob에 대한 증분 변경은 지원되지 않습니다. |Blob 엔드포인트의 경우 새로 고침 후 Blob의 부분 업데이트로 인해 업데이트가 클라우드에 업로드되지 않을 수 있습니다. 다음과 같은 작업 시퀀스를 예로 들 수 있습니다.<ul><li>클라우드에서 Blob을 만듭니다. 또는 디바이스에서 이전에 업로드된 Blob을 삭제합니다.</li><li>새로 고침 기능을 사용하여 클라우드에서 어플라이언스로 Blob을 새로 고칩니다.</li><li>Azure SDK REST API를 사용하여 Blob의 일부만 업데이트합니다.</li></ul>이러한 작업으로 인해 Blob의 업데이트된 섹션이 클라우드에서 업데이트되지 않을 수 있습니다. <br>**해결 방법**: robocopy와 같은 도구나 탐색기 또는 명령줄을 통한 일반 파일 복사를 사용하여 전체 Blob을 바꿉니다.|
|**3.**|제한|대역폭 제한 중에 디바이스에 대한 새 쓰기가 허용되지 않는 경우, NFS 클라이언트의 쓰기가 “사용 권한이 거부됨” 오류를 나타내며 실패합니다.| 이 오류는 다음과 같이 표시됩니다.<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: ‘test’ 디렉터리를 만들 수 없습니다. 사용 권한이 거부되었습니다.|
|**4.**|Blob Storage 수집|Blob Storage 수집에 AzCopy 버전 10을 사용하는 경우 인수 `Azcopy <other arguments> --cap-mbps 2000`을 사용하여 AzCopy를 실행합니다.| AzCopy에 대해 이러한 제한을 제공하지 않으면, 디바이스에 많은 요청을 전송하여 서비스에 문제를 유발할 수 있습니다.|
|**5.**|계층화된 스토리지 계정|계층화된 스토리지 계정을 사용하는 경우 다음이 적용됩니다.<ul><li> 블록 Blob만 지원됩니다. 페이지 Blob은 지원되지 않습니다.</li><li>스냅샷 또는 복사 API는 지원되지 않습니다.</li><li> `distcp`를 통한 Hadoop 워크로드 수집은 복사 작업을 과도하게 사용하므로 지원되지 않습니다.</li></ul>||
|**6.**|NFS 공유 연결|다중 프로세스가 동일한 공유에 복사되고 `nolock` 특성이 사용되지 않는 경우, 복사하는 동안 오류가 발생할 수 있습니다.|NFS 공유에 파일을 복사하려면 `nolock` 특성을 탑재 명령에 전달해야 합니다. 예: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`|
|**7.**|Kubernetes 클러스터|Kubernetes 클러스터를 실행하는 디바이스에 업데이트를 적용하는 경우 kubernetes 가상 머신이 다시 시작되고 재부팅됩니다. 이 인스턴스에서 지정된 복제본과 함께 배포되는 Pod만 업데이트 후에 자동으로 복원됩니다.  |복제본 세트를 지정하지 않고 복제 컨트롤러 외부에서 개별 Pod를 만든 경우, 이러한 Pod는 디바이스 업데이트 후에 자동으로 복원되지 않습니다. 이러한 Pod를 복원해야 합니다.<br>복제본 세트는 노드 실패 또는 중단형 노드 업그레이드 등을 비롯한 이유로 삭제되었거나 종료된 Pod를 대체합니다. 따라서 애플리케이션에 단일 Pod만 필요한 경우에도 복제본 세트를 사용하는 것이 좋습니다.|
|**8.**|Kubernetes 클러스터|Azure Stack Edge Pro의 Kubernetes는 Helm v3 이상에서만 지원됩니다. 자세한 내용은 [질문과 대답: Tiller 제거](https://v3.helm.sh/docs/faq/)를 참조하세요.|
|**9.**|Azure Arc + Azure Stack Edge Pro|웹 프록시가 Azure Stack Edge Pro 디바이스에 구성된 경우 Azure Arc 배포가 지원되지 않습니다.||
|**10.**|Kubernetes |포트 31000은 Kubernetes 대시보드용으로 예약되어 있습니다. 마찬가지로 기본 구성에서 IP 주소 172.28.0.1과 172.28.0.10은 각각 Kubernetes 서비스 및 Core DNS 서비스용으로 예약되어 있습니다.|예약된 IP는 사용하지 마세요.|
|**11.**|Kubernetes |현재 Kubernetes는 다중 프로토콜 LoadBalancer 서비스를 허용하지 않습니다. TCP와 UDP 모두에서 수신 대기해야 하는 DNS 서비스를 예로 들 수 있습니다. |MetalLB를 사용하여 Kubernetes의 제한을 해결하기 위해 같은 Pod 선택기에 두 가지 서비스(TCP용 1개, UDP용 1개)를 만들 수 있습니다. 이러한 서비스는 같은 공유 키와 spec.loadBalancerIP를 사용하여 같은 IP 주소를 공유합니다. 사용 가능한 IP 주소보다 더 많은 서비스를 사용하는 경우 IP를 공유할 수도 있습니다. <br> 자세한 내용은 [IP 주소 공유](https://metallb.universe.tf/usage/#ip-address-sharing)를 참조하세요.|
|**12.**|Kubernetes 클러스터|기존 Azure IoT Edge 마켓플레이스 모듈은 Kubernetes 클러스터에서 Azure Stack Edge 디바이스의 IoT Edge에 대한 호스팅 플랫폼으로 실행되지 않습니다.|모듈을 Azure Stack Edge 디바이스에 배포하기 전에 수정해야 합니다. 자세한 내용은 Azure Stack Edge 디바이스에서 실행되도록 마켓플레이스의 Azure IoT Edge 모듈 수정을 참조하세요.<!-- insert link-->|
|**13.**|Kubernetes |파일 기반 바인드 탑재는 Azure Stack Edge 디바이스에 있는 Kubernetes의 Azure IoT Edge에서 지원되지 않습니다.|IoT Edge는 변환 계층을 사용하여 `ContainerCreate` 옵션을 Kubernetes 구문으로 변환합니다. `hostpath` 디렉터리에 대한 `Binds` 맵을 만드는 작업, 즉 파일 기반 바인드 탑재는 IoT Edge 컨테이너의 경로에 바인딩될 수 없습니다.|
|**14.**|Kubernetes |IoT Edge에 대한 자체 인증서를 가져와 Azure Stack Edge 디바이스에 해당 인증서를 추가하는 경우, 새 인증서가 Helm 차트 업데이트의 일부로 선택되지 않습니다.|이 문제를 해결하려면 [디바이스의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md)합니다. `iotedged` 및 `edgehub` Pod를 다시 시작합니다.|
|**15.**|인증서 |특정한 경우 로컬 UI의 인증서 상태를 업데이트하는 데 몇 초 정도 걸릴 수 있습니다. |로컬 UI의 다음 시나리오에 영향을 줄 수 있습니다.<ul><li>**인증서** 페이지의 **상태** 열.</li><li>**시작** 페이지의 **보안** 타일.</li><li>**개요** 페이지의 **구성** 타일.</li></ul>  |

## <a name="next-steps"></a>다음 단계

- [GPU를 사용하여 Azure Stack Edge Pro 디바이스 배포 준비](azure-stack-edge-gpu-deploy-prep.md)