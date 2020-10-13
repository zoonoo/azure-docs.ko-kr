---
title: Azure Stack Edge Pro 장치 오류에서 복구
description: Azure Stack Edge Pro 실패 장치에서 복구 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: ef99ff874376e0d76e45e4b0b2c36a78d938130b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843878"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>실패 한 Azure Stack Edge Pro GPU 장치에서 복구 

이 문서에서는 Azure Stack Edge Pro GPU 장치에서 지속할 되지 않은 오류 로부터 복구 하는 방법을 설명 합니다. Azure Stack Edge Pro GPU 장치에서 지속할 되지 않은 오류는 장치 교체가 필요 합니다.

## <a name="before-you-begin"></a>시작하기 전에

아래와 같은 파일과 버전이 있어야 합니다.

- 장치 오류와 관련 하 여 Microsoft 지원에 연결 하 고 장치 교체를 권장 합니다. 
- [장치 오류 준비](azure-stack-edge-gpu-prepare-device-failure.md)에 설명 된 대로 장치 구성을 백업 했습니다.


## <a name="configure-replacement-device"></a>교체 장치 구성

장치에서 지속할 오류가 발생할 경우 교체 장치를 주문 해야 합니다. 교체 장치에 대 한 구성 단계는 동일 하 게 유지 됩니다. 

실패 한 장치에서 백업한 장치 구성 정보를 검색 합니다. 이 정보를 사용 하 여 교체 장치를 구성 합니다.  

교체 장치를 구성 하려면 다음 단계를 수행 합니다.

1. [배포 검사 목록](azure-stack-edge-gpu-deploy-checklist.md)에 필요한 정보를 수집 합니다. 이전 장치 구성에서 저장 한 정보를 사용 해야 합니다. 
1. 오류가 발생 한 것과 동일한 구성의 새 장치를 주문 합니다.  순서를 적용 하려면 Azure Portal에 [새 Azure Stack Edge 리소스를 만듭니다](azure-stack-edge-gpu-deploy-prep.md#) .
1. 장치 [압축 풀기](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [랙 탑재](azure-stack-edge-gpu-deploy-install.md#rack-the-device) 및 [케이블](azure-stack-edge-gpu-deploy-install.md#cable-the-device)연결 
1. [장치의 로컬 UI에 연결](azure-stack-edge-gpu-deploy-connect.md)합니다.
1. 이전 장치에 사용한 것과 동일한 IP 주소를 사용 하 여 네트워크를 구성 합니다. 이렇게 하면 사용자 환경에서 사용 되는 클라이언트 컴퓨터에 미치는 영향이 최소화 됩니다. [네트워크 설정을 구성](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)하는 방법을 참조 하세요.
1. 이전 장치와 동일한 장치 이름 및 DNS 도메인을 할당 합니다. 이렇게 하면 클라이언트가 동일한 장치 이름을 사용 하 여 새 장치와 통신할 수 있습니다. [장치 설정을 구성](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)하는 방법을 참조 하세요.
1. 이전 장치에 대해 수행한 것과 동일한 방식으로 새 장치에서 인증서를 구성 합니다. 새 장치에는 새 노드 일련 번호가 있습니다. 이전 장치에서 사용자 고유의 인증서를 사용한 경우에는 새 노드 인증서를 받아야 합니다. [인증서를 구성](azure-stack-edge-gpu-deploy-configure-certificates.md)하는 방법을 참조 하세요.
1. Azure Portal에서 활성화 키를 가져오고 새 장치를 활성화 합니다. [장치를 활성화](azure-stack-edge-gpu-deploy-activate.md)하는 방법을 참조 하세요.

이제 이전 장치에서 실행 중인 워크 로드를 배포할 준비가 되었습니다.

## <a name="restore-edge-cloud-shares"></a>Edge 클라우드 공유 복원

다음 단계를 수행 하 여 장치의 Edge 클라우드 공유에서 데이터를 복원 합니다.

1. 실패 한 장치에서 이전에 만든 것과 동일한 공유 이름을 사용 하 여 [공유를 추가](azure-stack-edge-j-series-manage-shares.md#add-a-share) 합니다. 공유를 만드는 동안 **blob 컨테이너** 가 **기존 옵션 사용** 으로 설정 되어 있는지 확인 하 고 이전 장치와 함께 사용 된 컨테이너를 선택 합니다.
1. 이전 장치에 대 한 액세스 권한이 있는 [사용자를 추가](azure-stack-edge-j-series-manage-users.md#add-a-user) 합니다.
1. 이전에 장치에서 공유와 연결 된 [저장소 계정을 추가](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account) 합니다. Edge 저장소 계정을 만드는 동안 기존 컨테이너에서를 선택 하 고 이전 장치에서 매핑된 Azure Storage 계정에 매핑된 컨테이너를 가리킵니다. 이전 장치의 Edge 저장소 계정에 기록 된 장치의 모든 데이터가 매핑된 Azure Storage 계정의 선택한 저장소 컨테이너에 업로드 되었습니다.
1. Azure에서 [공유 데이터를 새로 고칩니다](azure-stack-edge-j-series-manage-shares.md#refresh-shares) . 이렇게 하면 기존 컨테이너에서 공유로 모든 클라우드 데이터를 끌어옵니다.

## <a name="restore-edge-local-shares"></a>Edge 로컬 공유 복원

잠재적인 장치 오류에 대비 하려면 Kubernetes 또는 IoT 워크 로드에서 로컬 공유 데이터를 보호 하기 위해 다음 백업 솔루션을 배포 했을 수 있습니다.

| 타사 소프트웨어           | 솔루션에 대 한 참조                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 자세한 내용은 Cohesity에 문의 하세요.          |
| Veritas                        | 자세한 내용은 Veritas에 문의 하세요.   |

교체 장치가 완전히 구성 된 후에는 로컬 저장소에 대해 장치를 사용 하도록 설정 합니다. 

로컬 공유에서 데이터를 복구 하려면 다음 단계를 수행 합니다. 

1. [장치에서 계산을 구성](azure-stack-edge-gpu-deploy-configure-compute.md)합니다.
1. [로컬 공유를 다시 추가](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) 합니다.
1. 선택한 데이터 보호 솔루션에서 제공 하는 복구 절차를 실행 합니다. 위의 표에서 참조를 참조 하세요.

## <a name="restore-vm-files-and-folders"></a>VM 파일 및 폴더 복원

잠재적인 장치 오류를 준비 하려면 Vm에서 데이터를 보호 하기 위해 다음 백업 솔루션 중 하나를 배포 했을 수 있습니다.



| 백업 솔루션        | 지원되는 OS   | 참조                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Azure Backup에 대 한 MARS (Microsoft Azure Recovery Services) 에이전트 | Windows        | [MARS 에이전트 정보](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Microsoft Azure 통합, 백업 및 복구 솔루션 brief](https://www.cohesity.com/solution/cloud/azure) <br>자세한 내용은 Cohesity에 문의 하세요.                          |
| Veritas                 | Windows, Linux | 자세한 내용은 Veritas에 문의 하세요.                    |

교체 장치가 완전히 구성 된 후 이전에 사용한 VM 이미지를 사용 하 여 Vm을 다시 배포할 수 있습니다. 

Vm의 데이터를 복구 하려면 다음 단계를 수행 합니다.
 
1. 장치의 [vm 이미지에서 vm을 배포](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 합니다. 
1. VM에 선택 된 데이터 보호 솔루션을 설치 합니다.
1. 선택한 데이터 보호 솔루션에서 제공 하는 복구 절차를 실행 합니다. 위의 표에서 참조를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro 장치를 반환](azure-stack-edge-return-device.md)하는 방법에 대해 알아봅니다.
