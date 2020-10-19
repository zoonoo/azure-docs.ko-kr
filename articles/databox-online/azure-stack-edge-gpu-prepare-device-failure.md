---
title: Edge Pro 장치 오류 Azure Stack 준비
description: Azure Stack Edge Pro 실패 한 장치에 대 한 대체를 가져오는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: f7c7caf6c01c2e7ea9aaf141a2597ef338735d3d
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173592"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Azure Stack Edge Pro GPU 장치 오류에 대 한 준비

이 문서는 Azure Stack Edge Pro GPU 장치에서 장치 구성 및 데이터를 저장 하 고 백업 하는 방법을 자세히 설명 하 여 장치 오류를 준비 하는 데 도움이 됩니다. 

이 문서에는 Azure Stack Edge Pro GPU 장치에 배포 된 Kubernetes 및 IoT 컨테이너를 백업 하는 단계가 포함 되어 있지 않습니다. 

## <a name="understand-device-failures"></a>장치 오류 이해

Azure Stack Edge Pro GPU 장치에는 두 가지 유형의 하드웨어 오류가 발생할 수 있습니다.

- 하드웨어 구성 요소를 교체 해야 하는 지속할 오류 이러한 오류를 통해 장치를 저하 된 상태로 운영할 수 있습니다. 이러한 오류의 예로는 실패 한 단일 정전 (전원 공급 장치) 또는 장치의 단일 실패 디스크가 있습니다. 이러한 각 경우에 장치는 계속 작동할 수 있습니다. 실패 한 구성 요소를 교체 하려면 가장 빨리 Microsoft 지원에 문의 하는 것이 좋습니다.

- 전체 장치를 교체 해야 하는 지속할 되지 않은 오류 이 오류의 예는 장치에서 두 개의 디스크가 실패 한 경우입니다. 이러한 경우 Microsoft 지원에 연결 하 고 장치 교체가 필요한 것으로 확인 된 후에 Azure Stack에 지 장치를 쉽게 교체할 수 있도록 지원 합니다.

지속할 되지 않는 오류를 준비 하려면 장치에서 다음을 백업 해야 합니다.

- 장치 구성에 대 한 정보입니다.
- Edge 로컬 공유 및에 지 클라우드 공유에 있는 데이터입니다.
- 장치에서 실행 되는 Vm과 연결 된 파일 및 폴더입니다.


## <a name="back-up-device-configuration"></a>장치 구성 백업

장치를 처음 구성 하는 동안 [배포 검사 목록](azure-stack-edge-gpu-deploy-checklist.md)에 설명 된 대로 장치 구성 정보의 복사본을 유지 하는 것이 중요 합니다. 복구 중에이 구성 정보는 새 교체 장치에 적용 하는 데 사용 됩니다. 

## <a name="protect-device-data"></a>디바이스 데이터 보호

장치 데이터는 다음 유형 중 하나일 수 있습니다.

- Edge 클라우드 공유의 데이터
- 로컬 공유의 데이터
- Vm의 파일 및 폴더

다음 섹션에서는 장치에서 이러한 각 유형의 데이터를 보호 하기 위한 단계와 권장 사항을 설명 합니다.

## <a name="protect-data-in-edge-cloud-shares"></a>Edge 클라우드 공유의 데이터 보호

장치에서 Azure로 데이터를 계층화 하는 Edge 클라우드 공유를 만들 수 있습니다. 사용 가능한 네트워크 대역폭에 따라 지속할 되지 않는 오류가 발생할 경우 데이터 손실을 최소화 하도록 장치에서 대역폭 템플릿을 구성 합니다.

> [!IMPORTANT] 
> 장치에 지속할 되지 않은 오류가 있으면 장치에서 Azure로 계층화 되지 않은 로컬 데이터가 손실 될 수 있습니다. 

## <a name="protect-data-in-edge-local-shares"></a>Edge 로컬 공유의 데이터 보호

Kubernetes 또는 IoT Edge을 배포 하는 경우 응용 프로그램 데이터를 장치에 로컬로 저장 하 고 Azure Storage와 동기화 하지 않도록를 구성 합니다. 데이터는 장치의 공유에 저장 됩니다. 이러한 공유의 데이터를 백업 하는 것이 중요 하다는 것을 알 수 있습니다.

다음 타사 데이터 보호 솔루션은 로컬 SMB 또는 NFS 공유에 있는 데이터에 대 한 백업 솔루션을 제공할 수 있습니다. 

| 타사 소프트웨어           | 솔루션에 대 한 참조                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | https://www.cohesity.com/solution/cloud/azure/ <br> 자세한 내용은 Cohesity에 문의 하세요.          |
| Commvault                      | https://www.commvault.com/azure <br> 자세한 내용은 Commvault에 문의 하세요.          |
| Veritas                        | http://veritas.com/azure <br> 자세한 내용은 Veritas에 문의 하세요.   |


## <a name="protect-files-and-folders-on-vms"></a>Vm에서 파일 및 폴더 보호

Azure Stack Edge는 Azure Backup 및 기타 타사 데이터 보호 솔루션과 연동 하 여 장치에 배포 된 Vm에 포함 된 데이터를 보호 하는 백업 솔루션을 제공 합니다. 다음 표에서는 선택할 수 있는 사용 가능한 솔루션에 대 한 참조를 나열 합니다.


| 백업 솔루션        | 지원되는 OS   | 참조                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Azure Backup에 대 한 MARS (Microsoft Azure Recovery Services) 에이전트 | Windows        | [MARS 에이전트 정보](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Microsoft Azure 통합, 백업 및 복구 솔루션 brief](https://www.cohesity.com/solution/cloud/azure) <br>자세한 내용은 Cohesity에 문의 하세요.                          |
| Commvault               | Windows, Linux | https://www.commvault.com/azure <br>자세한 내용은 Commvault에 문의 하세요.                          |
| Veritas                 | Windows, Linux | http://veritas.com/azure <br> 자세한 내용은 Veritas에 문의 하세요.                    |



## <a name="next-steps"></a>다음 단계

- [실패 한 Azure Stack Edge PRO GPU 장치에서 복구](azure-stack-edge-gpu-recover-device-failure.md)하는 방법을 알아봅니다.
