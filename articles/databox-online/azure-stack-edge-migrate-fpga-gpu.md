---
title: Azure Stack Edge Pro FPGA에서 GPU 물리적 디바이스로 마이그레이션하는 방법에 대한 가이드
description: 이 가이드에는 Azure Stack Edge Pro FPGA 디바이스에서 Azure Stack Edge Pro GPU 디바이스로 워크로드를 마이그레이션하는 방법에 대한 지침이 포함되어 있습니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 430e34a1ca631be00ef46170affd4b56c79894a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566407"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro FPGA에서 Azure Stack Edge Pro GPU로 워크로드 마이그레이션

이 문서에서는 Azure Stack Edge Pro FPGA 디바이스에서 Azure Stack Edge Pro GPU 디바이스로 워크로드와 데이터를 마이그레이션하는 방법을 설명합니다. 마이그레이션 프로세스는 마이그레이션 계획 및 마이그레이션 고려 사항 검토라는 두 디바이스를 비교하는 것으로 시작됩니다. 마이그레이션 절차에서는 확인 및 디바이스 정리로 끝나는 자세한 단계를 제공합니다.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>마이그레이션 정보

마이그레이션은 워크로드 및 애플리케이션 데이터를 한 스토리지 위치에서 다른 스토리지 위치로 이동하는 프로세스입니다. 이 프로세스에서는 활성 애플리케이션을 중단하거나 비활성화하지 않고 조직의 현재 데이터를 한 스토리지 디바이스에서 다른 스토리지 디바이스로 정확히 복사한 다음, 모든 I/O(입/출력) 작업을 새 디바이스로 리디렉션합니다. 

이 마이그레이션 가이드에서는 Azure Stack Edge Pro FPGA 디바이스에서 Azure Stack Edge Pro GPU 디바이스로 데이터를 마이그레이션하는 데 필요한 과정을 단계별로 연습합니다. 이 문서는 데이터 센터의 Azure Stack Edge 디바이스를 운영, 배포, 관리하는 IT(정보 기술) 전문가 및 지식 근로자를 대상으로 합니다.

이 문서에서는 Azure Stack Edge Pro FPGA 디바이스를 *원본* 디바이스, Azure Stack Edge Pro GPU 디바이스를 *대상* 디바이스라고 부릅니다. 

## <a name="comparison-summary"></a>비교 요약

이 섹션에서는 Azure Stack Edge Pro GPU 디바이스와 Azure Stack Edge Pro FPGA 디바이스의 기능을 비교 요약합니다. 원본 디바이스와 대상 디바이스의 하드웨어는 거의 동일합니다. 하드웨어 가속 카드와 스토리지 용량만 다를 수 있습니다.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    기능  | Azure Stack Edge Pro GPU(대상 디바이스)  | Azure Stack Edge Pro FPGA(원본 디바이스)|
|----------------|-----------------------|------------------------|
| 하드웨어       | 하드웨어 가속: Nvidia T4 GPU 1개 또는 2개 <br> 컴퓨팅, 메모리, 네트워크 인터페이스, 전원 공급 장치 및 전원 코드 사양은 FPGA가 있는 디바이스와 동일합니다.  | 하드웨어 가속: Intel Arria 10 FPGA <br> 컴퓨팅, 메모리, 네트워크 인터페이스, 전원 공급 장치 및 전원 코드 사양은 GPU가 있는 디바이스와 동일합니다.          |
| 사용 가능한 스토리지 | 4.19TB <br> 패리티 복원력 및 내부 사용을 위한 공간을 예약한 후 | 12.5TB <br> 내부 사용을 위한 공간을 예약한 후 |
| 보안       | 인증서 |                                                     |
| 워크로드      | IoT Edge 워크로드 <br> VM 워크로드 <br> Kubernetes 워크로드| IoT Edge 워크로드 |
| 가격 책정        | [가격](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [가격](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>마이그레이션 계획

마이그레이션 계획을 세우려면 다음 정보를 고려해야 합니다.

- 마이그레이션 일정을 작성합니다. 
- 데이터를 마이그레이션하면 가동 중지 시간이 발생할 수 있습니다. 프로세스가 중단되는 가동 중지 유지 관리 기간 동안 마이그레이션을 예약하는 것이 좋습니다. 이 문서의 뒷부분에 설명된 대로 이 가동 중지 시간에 구성을 설정하고 복원할 것입니다.
- 총 가동 중지 시간을 파악하고 모든 관련자에게 전달합니다.
- 원본 디바이스에서 마이그레이션해야 하는 로컬 데이터를 식별합니다. 예방 조치로 기존 스토리지에 있는 모든 데이터의 최신 백업이 있는지 확인합니다. 


## <a name="migration-considerations"></a>마이그레이션 고려 사항 

마이그레이션을 진행하기 전에 다음 정보를 고려해야 합니다. 

- Azure Stack Edge Pro GPU 디바이스는 Azure Stack Edge Pro FPGA 리소스에 대해 활성화할 수 없습니다. [Azure Stack Edge Pro GPU 주문 작성](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)에 설명된 대로 Azure Stack Edge Pro GPU 디바이스에 대한 새 리소스를 만들어야 합니다.
- FPGA를 사용하는 원본 디바이스에 배포된 Machine Learning 모델은 GPU를 사용하는 대상 디바이스에 맞게 변경해야 합니다. 모델과 관련하여 도움이 필요한 경우 Microsoft에 지원에 문의하면 됩니다. FPGA를 사용하지 않는(CPU만 사용) 원본 디바이스에 배포된 사용자 지정 모델은 대상 디바이스와 똑같이(CPU 사용) 작동해야 합니다.
- 원본 디바이스에 배포된 IoT Edge 모듈을 변경해야 모듈을 대상 디바이스에 성공적으로 배포할 수 있습니다. 
- 원본 디바이스는 NFS 3.0 및 4.1 프로토콜을 지원합니다. 대상 디바이스는 NFS 3.0 프로토콜만 지원합니다.
- 원본 디바이스는 SMB 및 NFS 프로토콜을 지원합니다. 대상 디바이스는 공유를 위한 SMB 및 NFS 프로토콜 외에도 스토리지 계정을 사용하여 REST 프로토콜을 통해 스토리지를 지원합니다.
- 원본 디바이스의 공유 액세스는 IP 주소를 사용하는 반면, 대상 디바이스의 공유 액세스는 디바이스 이름을 사용합니다.

## <a name="migration-steps-at-a-glance"></a>마이그레이션 단계 한 눈에 보기

다음 표에는 마이그레이션에 필요한 단계와 이러한 단계를 수행할 위치를 설명하는 전반적인 마이그레이션 흐름이 요약되어 있습니다.

| 단계 | 할 일| 디바이스 |
|---------------|-------------|----------------|
| 원본 디바이스 준비*       | 1. 구성 데이터 기록 <br>2. 공유 데이터 백업 <br>3. IoT Edge 워크로드 준비| 원본 디바이스  |
| 대상 디바이스 준비*       |1. 새 주문 작성 <br>2. 구성 및 활성화| 대상 장치  |
| 데이터 마이그레이션       | 1. 공유의 데이터 마이그레이션 <br>2. IoT Edge 워크로드 다시 배포| 대상 장치  |
| 데이터 확인            |마이그레이션된 데이터 확인 |대상 장치  |
| 정리, 반환              |데이터 지우기 및 반환| 원본 디바이스  |

**원본 디바이스와 대상 디바이스를 동시에 준비할 수 있습니다.*

## <a name="prepare-source-device"></a>원본 디바이스 준비

준비에는 Edge 클라우드 공유, Edge 로컬 공유 및 디바이스에 배포된 IoT Edge 모듈을 식별하는 작업이 포함됩니다. 

### <a name="1-record-configuration-data"></a>1. 구성 데이터 기록

원본 디바이스에서 로컬 UI를 통해 이 단계를 수행합니다.

*원본* 디바이스에 구성 데이터를 기록합니다. 디바이스 구성을 기록하는 데 도움이 되도록 [배포 검사 목록](azure-stack-edge-gpu-deploy-checklist.md)을 사용합니다. 마이그레이션하는 동안 이 구성 정보를 사용하여 새 대상 디바이스를 구성할 것입니다. 

### <a name="2-back-up-share-data"></a>2. 공유 데이터 백업

디바이스 데이터는 다음 형식 중 하나입니다.

- Edge 클라우드 공유의 데이터
- 로컬 공유의 데이터

#### <a name="data-in-edge-cloud-shares"></a>Edge 클라우드 공유의 데이터

Edge 클라우드는 디바이스에서 Azure로 계층 데이터를 공유합니다. *원본* 디바이스에서 Azure Portal을 통해 이 단계를 수행합니다. 

- 원본 디바이스에 있는 모든 Edge 클라우드 공유 및 사용자 목록을 만듭니다.
- 보유한 모든 대역폭 일정 목록을 만듭니다. 대상 디바이스에서 이러한 대역폭 일정을 다시 만들 것입니다.
- 사용 가능한 네트워크 대역폭에 따라 디바이스에 대역폭 일정을 구성하여 클라우드에 계층화된 데이터를 최대화할 수 있습니다. 디바이스에서 로컬 데이터를 최소화합니다.
- 공유가 클라우드에 완전히 계층화되었는지 확인합니다. Azure Portal에서 공유 상태를 확인하여 계층화를 확인할 수 있습니다.  

#### <a name="data-in-edge-local-shares"></a>Edge 로컬 공유의 데이터

Edge 로컬 공유의 데이터는 디바이스에 남아 있습니다. *원본* 디바이스에서 Azure Portal을 통해 이 단계를 수행합니다. 

- 디바이스에서 Edge 로컬 공유 목록을 만듭니다.
- 일회성 데이터 마이그레이션을 수행하는 경우 다른 온-프레미스 서버에 Edge 로컬 공유 데이터의 복사본을 만듭니다. `robocopy`(SMB) 또는 `rsync`(NFS)와 같은 복사 도구를 사용하여 데이터를 복사할 수 있습니다. 필요에 따라 로컬 공유의 데이터를 백업하기 위해 타사 데이터 보호 솔루션을 이미 배포한 분들도 있을 것입니다. 다음 타사 솔루션은 Azure Stack Edge Pro FPGA 디바이스와 함께 사용할 수 있습니다.

    | 타사 소프트웨어           | 솔루션 참조                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 자세한 내용은 Cohesity에 문의하세요.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 자세한 내용은 Commvault에 문의하세요.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 자세한 내용은 Veritas에 문의하세요.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 자세한 내용은 Veeam에 문의하세요. |


### <a name="3-prepare-iot-edge-workloads"></a>3. IoT Edge 워크로드 준비

- IoT Edge 모듈을 배포하고 FPGA 가속을 사용하는 경우 이 모듈을 GPU 디바이스에서 실행하기 전에 수정이 필요할 수 있습니다. [IoT Edge 모듈 수정](azure-stack-edge-gpu-modify-fpga-modules-gpu.md)의 지침을 따르세요. 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>대상 디바이스 준비

### <a name="1-create-new-order"></a>1. 새 주문 작성

*대상* 디바이스에 대한 새 주문(및 새 리소스)을 만들어야 합니다. 대상 디바이스는 FPGA 리소스가 아닌 GPU 리소스에 대해 활성화되어야 합니다.

주문을 넣으려면 Azure Portal에서 [새 Azure Stack Edge 리소스를 만듭니다](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).


### <a name="2-set-up-activate"></a>2. 설정, 활성화

앞에서 만든 새 리소스에 대해 *대상* 디바이스를 설정하고 활성화해야 합니다. 

다음 단계에 따라 Azure Portal에서 *대상* 디바이스를 구성합니다.

1. [배포 검사 목록](azure-stack-edge-gpu-deploy-checklist.md)에 필요한 정보를 수집합니다. 원본 디바이스 구성에서 저장한 정보를 사용할 수 있습니다. 
1. [제품을 개봉](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)하고, [랙을 탑재](azure-stack-edge-gpu-deploy-install.md#rack-the-device)하고, [디바이스 케이블을 연결](azure-stack-edge-gpu-deploy-install.md#cable-the-device)합니다. 
1. [디바이스의 로컬 UI에 연결](azure-stack-edge-gpu-deploy-connect.md)합니다.
1. 고정 IP를 사용하는 경우 이전 디바이스에 사용한 것과 다른 IP 주소를 사용하여 네트워크를 구성합니다. [네트워크 설정 구성](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) 방법을 참조하세요.
1. 이전 디바이스와 동일한 디바이스 이름을 할당하고 DNS 도메인을 입력합니다. [디바이스 설정 구성](azure-stack-edge-gpu-deploy-set-up-device-update-time.md) 방법을 참조하세요.
1. 새 디바이스에서 인증서를 구성합니다. [인증서 구성](azure-stack-edge-gpu-deploy-configure-certificates.md) 방법을 참조하세요.
1. Azure Portal에서 활성화 키를 가져오고 새 디바이스를 활성화합니다. [디바이스 활성화](azure-stack-edge-gpu-deploy-activate.md) 방법을 참조하세요.

공유 데이터를 복원하고 이전 디바이스에서 실행 중이던 워크로드를 배포할 준비가 완료되었습니다.

## <a name="migrate-data"></a>데이터 마이그레이션

이제 원본 디바이스에서 *대상* 디바이스의 Edge 클라우드 공유 및 Edge 로컬 공유로 데이터를 복사하겠습니다.

### <a name="1-from-edge-cloud-shares"></a>1. Edge 클라우드 공유에서 할 일

다음 단계에 따라 대상 디바이스에서 Edge 클라우드 공유의 데이터를 동기화합니다.

1. 원본 디바이스에서 만든 공유 이름에 해당하는 [공유를 추가](azure-stack-edge-j-series-manage-shares.md#add-a-share)합니다. 공유를 만들 때 **Blob 컨테이너 선택** 이 **기존 항목 사용** 으로 설정되었는지 확인한 다음, 이전 디바이스에서 사용된 컨테이너를 선택합니다.
1. 이전 디바이스에 대한 액세스 권한이 있는 [사용자를 추가](azure-stack-edge-j-series-manage-users.md#add-a-user)합니다.
1. Azure에서 공유 [데이터를 새로 고칩니다](azure-stack-edge-j-series-manage-shares.md#refresh-shares). 공유를 새로 고치면 기존 컨테이너에서 공유로 모든 클라우드 데이터를 풀다운합니다.
1. 공유와 연결할 대역폭 일정을 다시 만듭니다. 자세한 단계는 [대역폭 일정 추가](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule)를 참조하세요.


### <a name="2-from-edge-local-shares"></a>2. Edge 로컬 공유에서 할 일

IoT 워크로드에 대한 로컬 공유 데이터를 보호하기 위해 타사 백업 솔루션을 배포한 분들도 있을 것입니다. 이제 해당 데이터를 복원해야 합니다.

교체 디바이스가 완전히 구성된 후에는 로컬 스토리지에 디바이스를 사용하도록 설정합니다. 

다음 단계에 따라 로컬 공유의 데이터를 복구합니다.

1. [디바이스에서 컴퓨팅을 구성](azure-stack-edge-gpu-deploy-configure-compute.md)합니다.
1. 모든 로컬 공유를 대상 디바이스에 추가합니다. 자세한 단계는 [로컬 공유 추가](azure-stack-edge-gpu-manage-shares.md#add-a-local-share)를 참조하세요.
1. 원본 디바이스의 SMB 공유에 액세스할 때는 IP 주소가 사용되고, 대상 디바이스에서는 디바이스 이름이 사용됩니다. [Azure Stack Edge Pro GPU의 SMB 공유에 연결](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-smb-share)을 참조하세요. 대상 디바이스의 NFS 공유에 연결하려면 디바이스와 연결된 새 IP 주소를 사용해야 합니다. [Azure Stack Edge Pro GPU에서 NFS 공유에 연결](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-nfs-share)을 참조하세요. 

    SMB 또는 NFS를 통해 공유 데이터를 중간 서버로 복사한 경우 중간 서버의 데이터를 대상 디바이스의 공유로 복사할 수 있습니다. 원본 디바이스와 대상 디바이스가 모두 *온라인* 상태인 경우 원본 디바이스에서 직접 데이터를 복사할 수도 있습니다.

    타사 소프트웨어를 사용하여 로컬 공유의 데이터를 백업한 경우 선택한 데이터 보호 솔루션에서 제공하는 복구 절차를 실행해야 합니다. 다음 표의 참조를 확인하세요.

    | 타사 소프트웨어           | 솔루션 참조                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 자세한 내용은 Cohesity에 문의하세요.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 자세한 내용은 Commvault에 문의하세요. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 자세한 내용은 Veritas에 문의하세요.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 자세한 내용은 Veeam에 문의하세요. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. IoT Edge 워크로드 다시 배포

IoT Edge 모듈을 준비한 후에는 대상 디바이스에 IoT Edge 워크로드를 배포해야 합니다. IoT Edge 모듈을 배포하는 동안 오류가 발생하면 다음 문서를 참조하세요.

- [Azure IoT Edge에 대한 일반적인 문제 및 해결 방법](../iot-edge/troubleshoot-common-errors.md). 
- [IoT Edge 런타임 오류](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>데이터 확인

마이그레이션 후에는 모든 데이터가 마이그레이션되었으며 대상 디바이스에 워크로드가 배포되었는지 확인합니다.

## <a name="erase-data-return"></a>데이터 지우기, 반환

데이터 마이그레이션이 완료된 후에는 로컬 데이터를 지우고 원본 디바이스를 반환합니다. [Azure Stack Edge Pro 디바이스 반환](azure-stack-edge-return-device.md)의 단계를 따르세요.


## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro GPU 디바이스에 IoT Edge 워크로드를 배포하는 방법 알아보기](azure-stack-edge-gpu-deploy-compute-module-simple.md)