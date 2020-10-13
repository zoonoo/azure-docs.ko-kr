---
title: Azure Stack Edge Pro Preview 릴리스 정보 | Microsoft Docs
description: 일반 공급 릴리스를 실행 하는 Azure Stack Edge Pro에 대 한 중요 한 미해결 문제 및 해결 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: d166b0a4c4b69f03d7dba9d997d7d07fbd81ef41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893980"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>GPU 미리 보기 릴리스 정보를 사용 하 여 Azure Stack Edge Pro

다음 릴리스 정보는 GPU를 사용 하는 Azure Stack Edge Pro 장치의 2008 preview 릴리스에 대 한 중요 한 미해결 문제 및 해결 된 문제를 식별 합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Azure Stack Edge Pro 장치를 배포 하기 전에 릴리스 정보에 포함 된 정보를 주의 깊게 검토 하세요.

이 문서는 다음 소프트웨어 릴리스 **Azure Stack Edge Pro 2008**에 적용 됩니다. 

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>새로운 기능

Azure Stack Edge 2008 릴리스에는 다음과 같은 새로운 기능이 추가 되었습니다. 실행 중인 특정 미리 보기 소프트웨어 버전에 따라 이러한 기능의 하위 집합이 표시 될 수 있습니다. 

- **저장소 클래스** -이전 릴리스에서는 Azure Stack Edge Pro 장치에서 실행 되는 Kubernetes 클러스터에 배포 된 상태 저장 응용 프로그램에 대해 SMB 또는 NFS 공유를 통해 저장소를 정적으로 프로 비전 할 수 있습니다. 이 릴리스에서는 저장소를 동적으로 프로 비전 할 수 있는 저장소 클래스가 추가 되었습니다. 자세한 내용은 [Azure Stack Edge PRO GPU 장치에서 저장소 관리 Kubernetes](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning)를 참조 하세요. 
- **메트릭 서버가 있는 Kubernetes 대시보드** -이 릴리스에서는 Kubernetes 대시보드가 메트릭 서버 추가 기능을 사용 하 여 추가 됩니다. 대시보드를 사용 하 여 Azure Stack Edge Pro 장치에서 실행 되는 응용 프로그램의 개요를 확인 하 고, Kubernetes 클러스터 리소스의 상태를 확인 하 고, 장치에서 발생 한 모든 오류를 확인할 수 있습니다. 메트릭 서버는 장치의 Kubernetes 리소스에서 CPU 및 메모리 사용량을 집계 합니다. 자세한 내용은 [Kubernetes 대시보드를 사용 하 여 Azure Stack Edge PRO GPU 장치 모니터링을](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)참조 하세요.
- **Azure Stack Edge pro 용 Azure arc** -이 릴리스를 시작 하면 azure arc를 통해 Azure Stack Edge Pro 장치에 응용 프로그램 워크 로드를 배포할 수 있습니다. Azure Arc는 Kubernetes 클러스터에 응용 프로그램을 배포 하는 데 사용할 수 있는 하이브리드 관리 도구입니다. 자세한 내용은 [Azure Stack Edge Pro 장치에서 Azure Arc를 통해 워크 로드 배포](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)를 참조 하세요.  

## <a name="known-issues"></a>알려진 문제 

다음 표에서는 Azure Stack Edge Pro 장치에 대 한 알려진 문제를 요약 하 여 보여 줍니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | SQL database를 만들려면 관리자 액세스 권한이 필요 합니다.   |의 1-2 단계 대신 다음 단계를 수행 [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) 합니다. <ul><li>장치의 로컬 UI에서 계산 인터페이스를 사용 하도록 설정 합니다. Compute **> 포트 # > 계산 > 적용을 선택 합니다.**</li><li>`sqlcmd`클라이언트 컴퓨터에서 다운로드https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>계산 인터페이스 IP 주소 (사용 하도록 설정 된 포트)에 연결 하 여 주소 끝에 ", 1401"을 추가 합니다.</li><li>최종 명령은 sqlcmd-S {Interface IP}, 1401-U SA-P "와 같습니다. Passw0rd ".</li>그러면 현재 설명서의 3-4 단계가 동일 해야 합니다. </li></ul> |
| **2.** |새로 고침| **새로 고침** 을 통해 복원 된 blob에 대 한 증분 변경은 지원 되지 않습니다. |Blob 끝점의 경우 새로 고침 후 blob의 부분 업데이트로 인해 업데이트가 클라우드에 업로드 되지 않을 수 있습니다. 예를 들어 다음과 같은 작업의 시퀀스입니다.<ul><li>클라우드에서 blob을 만듭니다. 또는 장치에서 이전에 업로드 된 blob을 삭제 합니다.</li><li>새로 고침 기능을 사용 하 여 클라우드에서 blob을 어플라이언스로 새로 고칩니다.</li><li>Azure SDK REST Api를 사용 하 여 blob의 일부만 업데이트 합니다.</li></ul>이러한 작업으로 인해 blob의 업데이트 된 섹션이 클라우드에서 업데이트 되지 않을 수 있습니다. <br>**해결 방법**: robocopy와 같은 도구를 사용 하거나 탐색기 또는 명령줄을 통해 일반 파일 복사를 사용 하 여 전체 blob을 바꿉니다.|
|**3.**|제한|조정 하는 동안 장치에 새 쓰기가 허용 되지 않는 경우 NFS 클라이언트에서 수행 하는 쓰기가 "권한 거부" 오류로 인해 실패 합니다.| 오류는 다음과 같이 표시 됩니다.<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: ' test ' 디렉터리를 만들 수 없습니다. 사용 권한이 거부 되었습니다.|
|**4.**|Blob Storage 수집|Blob storage 수집에 AzCopy 버전 10을 사용 하는 경우 다음 인수를 사용 하 여 AzCopy를 실행 합니다. `Azcopy <other arguments> --cap-mbps 2000`| AzCopy에 대해 이러한 제한을 제공 하지 않으면 잠재적으로 많은 수의 요청을 장치로 전송 하 여 서비스에 문제가 발생할 수 있습니다.|
|**5.**|계층화 된 저장소 계정|계층화 된 저장소 계정을 사용 하는 경우 다음이 적용 됩니다.<ul><li> 블록 blob만 지원 됩니다. 페이지 Blob은 지원되지 않습니다.</li><li>스냅숏 또는 복사 API는 지원 되지 않습니다.</li><li> 에서 `distcp` 복사 작업을 많이 사용 하므로 Hadoop 워크 로드 수집은 지원 되지 않습니다.</li></ul>||
|**6.**|NFS 공유 연결|여러 프로세스에서 동일한 공유로 복사 하 고 `nolock` 특성을 사용 하지 않는 경우 복사 하는 동안 오류가 표시 될 수 있습니다.|`nolock`NFS 공유에 파일을 복사 하려면 특성을 탑재 명령에 전달 해야 합니다. 예: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`|
|**7.**|Kubernetes 클러스터|Kubernetes 클러스터를 실행 하는 장치에 업데이트를 적용 하는 경우 kubernetes 가상 머신이 다시 시작 되 고 재부팅 됩니다. 이 인스턴스에서 지정 된 복제본과 함께 배포 되는 pod만 업데이트 후 자동으로 복원 됩니다.  |복제 세트를 지정 하지 않고 복제 컨트롤러 외부에서 개별 pod를 만든 경우 이러한 pod는 장치 업데이트 후 자동으로 복원 되지 않습니다. 이러한 pod를 복원 해야 합니다.<br>복제 세트는 노드 실패 또는 중단 노드 업그레이드와 같은 어떤 이유로 든 삭제 되었거나 종료 된 pod를 대체 합니다. 따라서 응용 프로그램에 단일 pod만 필요한 경우에도 복제본 집합을 사용 하는 것이 좋습니다.|
|**8.**|Kubernetes 클러스터|Kubernetes on Azure Stack Edge Pro는 투구 v3 이상 에서만 지원 됩니다. 자세한 내용은 질문과 [대답: Tiller 제거](https://v3.helm.sh/docs/faq/)를 참조 하세요.|
|**9.**|Azure Arc + Azure Stack Edge Pro|웹 프록시가 Azure Stack Edge Pro 장치에 구성 된 경우 Azure Arc 배포가 지원 되지 않습니다.||
|**5-10.**|Kubernetes |포트 31000은 Kubernetes 대시보드에 예약 되어 있습니다. 마찬가지로 기본 구성에서 IP 주소 172.28.0.1와 172.28.0.10는 각각 Kubernetes service 및 Core DNS 서비스용으로 예약 되어 있습니다.|예약 된 Ip를 사용 하지 마십시오.|
|**pt.**|Kubernetes |Kubernetes는 현재 다중 프로토콜 LoadBalancer 서비스를 허용 하지 않습니다. 예를 들어 TCP와 UDP 모두에서 수신 대기 해야 하는 DNS 서비스입니다. |MetalLB를 사용 하는 Kubernetes의이 제한 사항을 해결 하기 위해 동일한 pod 선택기에 두 가지 서비스 (TCP에 대해 하나, 하나는 UDP 용)를 만들 수 있습니다. 이러한 서비스는 동일한 공유 키와 loadBalancerIP를 사용 하 여 동일한 IP 주소를 공유 합니다. 사용 가능한 IP 주소 보다 많은 서비스를 사용 하는 경우 ip를 공유할 수도 있습니다. <br> 자세한 내용은 [IP 주소 공유](https://metallb.universe.tf/usage/#ip-address-sharing)를 참조 하세요.|
|**10.**|Kubernetes 클러스터|기존 Azure IoT Edge marketplace 모듈은 Azure Stack Edge 장치에서 IoT Edge에 대 한 호스팅 플랫폼으로 Kubernetes 클러스터에서 실행 되지 않습니다.|모듈은 Azure Stack Edge 장치에 배포 되기 전에 수정 해야 합니다. 자세한 내용은 marketplace에서 Azure IoT Edge 모듈을 수정 하 여 Azure Stack Edge 장치에서 실행을 참조 하세요.<!-- insert link-->|
|**/10.**|Kubernetes |파일 기반 바인드 탑재는 Azure Stack에 지 장치의 Kubernetes에서 Azure IoT Edge 지원 되지 않습니다.|IoT Edge는 번역 계층을 사용 하 여 `ContainerCreate` 옵션을 Kubernetes 구문으로 변환 합니다. `Binds`호스트 경로 디렉터리에 맵 만들기 또는 만들기 및 파일 기반 바인드 탑재는 IoT Edge 컨테이너의 경로에 바인딩할 수 없습니다.|
|**14.**|Kubernetes |IoT Edge에 대 한 자체 인증서를 가져와서 Azure Stack에 지 장치에 추가 하는 경우, 새 인증서는 투구 차트 업데이트의 일부로 선택 되지 않습니다.|이 문제를 해결 하려면 [장치의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md)합니다. `iotedged`Pod를 다시 시작 `edgehub` 합니다.|
|**15.**|인증서 |특정 한 경우 로컬 UI에서 인증서 상태를 업데이트 하는 데 몇 초 정도 걸릴 수 있습니다. |로컬 UI의 다음 시나리오에 영향을 줄 수 있습니다.<ul><li>**인증서** 페이지의 **상태** 열입니다.</li><li>**시작** 페이지의 **보안** 타일입니다.</li><li>**개요** 페이지의 **구성** 타일입니다.</li></ul>  |

## <a name="next-steps"></a>다음 단계

- [GPU를 사용 하 여 Azure Stack Edge Pro 장치 배포 준비](azure-stack-edge-gpu-deploy-prep.md)

