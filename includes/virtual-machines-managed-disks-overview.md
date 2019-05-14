---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 91e9d3a99224c09ecfb5cc3b477a71a7f7bfed7a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199291"
---
## <a name="benefits-of-managed-disks"></a>관리 디스크의 이점

관리 디스크를 사용하여 얻을 수 있는 몇 가지 이점에 대해 살펴보겠습니다.

### <a name="highly-durable-and-available"></a>뛰어난 내구성 및 가용성

관리 디스크는 99.999%의 가용성을 위해 설계되었습니다. 관리 디스크는 높은 내구성을 위한 세 개의 데이터 복제본을 제공하여 이러한 가용성을 달성합니다. 즉, 하나 또는 두 개의 복제본에서 문제가 발생하는 경우 나머지 복제본을 사용하면 데이터의 지속성과 우수한 내결함성을 확보할 수 있습니다. 이러한 아키텍처를 통해 Azure는 IaaS(Infrastructure as a Service) 디스크에 엔터프라이즈급 내구성을 지속적으로 제공할 뿐만 아니라 연간 실패율 0%로 업계 최고를 자랑합니다.

### <a name="simple-and-scalable-vm-deployment"></a>간단하고 확장성 있는 VM 배포

관리 디스크를 사용하면 지역당 구독에 특정 유형의 VM **디스크**를 50,000개까지 만들 수 있고 따라서 단일 구독에 수천 개의 **VM**을 만들 수 있습니다. 이 기능을 사용하면 Marketplace 이미지를 사용하여 가상 머신 확장 집합에 VM을 1,000개까지 만들 수 있기 때문에 [가상 머신 확장 집합](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)의 확장성을 훨씬 더 높일 수도 있습니다.

### <a name="integration-with-availability-sets"></a>가용성 집합과 통합

관리 디스크는 가용성 집합과 통합되어 단일 실패 지점을 피할 만큼 [가용성 집합의 VM](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) 디스크가 서로 충분히 격리되도록 해줍니다. 디스크는 다른 저장소 배율 단위(스탬프)에 자동으로 배치됩니다. 스탬프가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 해당 스탬프의 디스크가 있는 VM 인스턴스만 실패합니다. 예를 들어 애플리케이션을 5개의 VM에서 실행 중이고 VM이 가용성 집합 내에 있다고 가정해 보겠습니다. 이러한 VM의 디스크는 동일한 스탬프에 저장되지 않습니다. 따라서 스탬프 하나가 작동이 중단되면 다른 애플리케이션 인스턴스가 계속해서 실행됩니다.

### <a name="integration-with-availability-zones"></a>가용성 영역과 통합

관리 디스크는 [가용성 영역](../articles/availability-zones/az-overview.md)을 지원하며, 이것은 데이터 센터 오류로부터 애플리케이션을 보호하는 고가용성 기능입니다. 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. Azure는 가용성 영역을 통해 업계 최고의 99.99% VM 작동 시간 SLA를 제공합니다.

### <a name="azure-backup-support"></a>Azure Backup 지원

지역적 재해로부터 보호하기 위해 시간 기반 백업 및 백업 보존 정책에 따라 백업 작업을 만드는 데 [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md)을 사용할 수 있습니다. 이를 통해 VM 복원을 원하는 대로 쉽게 수행할 수 있습니다. 현재, Azure Backup은 최대 4TiB(테비바이트)의 디스크 크기를 지원합니다. 자세한 내용은 [관리 디스크로 VM에 Azure Backup 사용](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)을 참조하세요.

### <a name="granular-access-control"></a>세부적인 액세스 제어

[Azure RBAC(역할 기반 액세스 제어)](../articles/role-based-access-control/overview.md)를 사용하여 관리 디스크에 대한 특정 권한을 한 명 이상의 사용자에게 할당할 수 있습니다. 관리 디스크는 읽기, 쓰기(만들기/업데이트), 삭제, [SAS(공유 액세스 서명) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) 검색 등 디스크에 대한 다양한 작업을 공개합니다. 업무를 수행하는 데 필요한 작업에만 액세스 권한을 부여할 수 있습니다. 예를 들어 관리 디스크를 저장소 계정에 복사하지 말아야 하는 경우에는 해당 관리 디스크에 대한 내보내기 작업에 액세스를 부여하지 않도록 선택할 수 있습니다. SAS URI를 사용하여 관리 디스크를 복사할 수 없도록 하기 위해 관리 디스크에 해당 권한을 부여하지 않도록 선택할 수 있습니다.

## <a name="disk-roles"></a>디스크 역할

Azure에는 데이터 디스크, OS 디스크 및 임시 디스크라는 3가지 주요 디스크 역할이 있습니다. 이러한 역할은 가상 머신에 연결된 디스크에 매핑됩니다.

![실제 디스크 역할](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>데이터 디스크 

데이터 디스크는 애플리케이션 데이터 또는 사용자가 보존해야 하는 기타 데이터를 저장하기 위해 가상 머신에 연결된 관리 디스크입니다. 데이터 디스크는 SCSI 드라이브로 등록되며 사용자가 선택한 문자로 레이블이 지정됩니다. 각 데이터 디스크의 최대 용량은 32,767GiB(기비바이트)입니다. 가상 머신의 크기에 따라 사용자가 해당 가상 머신에 연결할 수 있는 데이터의 디스크의 용량과 디스크를 호스트하기 위해 사용할 수 있는 저장소 유형이 결정됩니다.

### <a name="os-disk"></a>OS 디스크

모든 가상 머신은 하나의 연결된 운영 체제 디스크를 갖습니다. OS 디스크에는 VM이 만들어질 때 선택된 OS가 사전 설치되어 있습니다.

이 디스크의 최대 용량은 2,048GiB입니다.

### <a name="temporary-disk"></a>임시 디스크

모든 VM 디스크는 관리 디스크가 아닌 임시 디스크를 포함합니다. 이러한 임시 디스크는 애플리케이션 및 프로세스에 대한 단기 저장소를 제공하며 페이지 또는 스왑 파일과 같은 데이터 저장에 사용됩니다. 임시 디스크의 데이터는 [유지 관리 이벤트](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) 기간에 또는 [VM을 다시 배포](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)할 때 손실될 수 있습니다. Azure Linux VM에서 임시 디스크는 기본적으로 /dev/sdb이며 Windows VM의 임시 디스크는 기본적으로 E:입니다. VM을 정상적으로 재부팅하는 동안 임시 디스크의 데이터는 유지됩니다.

## <a name="managed-disk-snapshots"></a>관리 디스크 스냅숏

관리 디스크는 기본적으로 표준 관리 디스크로 저장되는 관리 디스크의 완전한 읽기 전용 복사본입니다. 스냅숏을 사용하면 관리 디스크를 언제든지 백업할 수 있습니다. 이 스냅숏은 원본 디스크와 별도로 존재하고 새 관리 디스크를 만드는 데 사용될 수도 있습니다. 사용된 크기에 따라 요금이 청구됩니다. 예를 들어 프로비저닝된 용량이 64GiB이고 실제 사용된 데이터 크기가 10GiB인 관리 디스크의 스냅숏을 만들면 사용된 10GiB의 데이터 크기에 대해서만 스냅숏 요금이 청구됩니다.  

관리 디스크를 사용하여 스냅숏을 만드는 방법에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

* [Windows에서 스냅숏을 사용하여 관리 디스크로 저장된 VHD 복사본 만들기](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Linux에서 스냅숏을 사용하여 관리 디스크로 저장된 VHD 복사본 만들기](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>이미지

관리 디스크는 관리되는 사용자 지정 이미지 만들기도 지원합니다. 스토리지 계정의 사용자 지정 VHD에서 이미지를 만들거나 일반화된(시스템에서 준비된) VM에서 직접 만들 수 있습니다. 이 프로세스는 단일 이미지를 캡처합니다. 이 이미지는 OS와 데이터 디스크를 모두 포함하여 VM과 연결된 모든 관리 디스크를 포함합니다. 이 관리되는 사용자 지정 이미지를 사용하면 저장소 계정을 복사하거나 관리할 필요 없이 사용자 지정 이미지를 사용하여 수백 개의 VM을 만들 수 있습니다.

이미지 만들기에 대한 내용은 다음 문서를 참조하세요.

* [Azure에서 일반화된 VM의 관리 이미지를 캡처하는 방법](../articles/virtual-machines/windows/capture-image-resource.md)
* [Azure CLI를 사용하여 Linux 가상 머신을 일반화하고 캡처하는 방법](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>이미지 및 스냅숏

이미지와 스냅숏의 차이를 이해하는 것이 중요합니다. 관리 디스크를 사용하면 할당이 취소되어 일반화된 VM의 이미지를 만들 수 있습니다. 이 이미지에는 VM에 연결된 모든 디스크가 포함됩니다. 이 이미지를 사용하여 VM을 만들 수 있고 여기에는 모든 디스크가 포함됩니다.

스냅숏은 스냅숏 생성 시점의 디스크 복사본입니다. 이것은 하나의 디스크에만 해당됩니다. VM의 디스크가 하나(OS 디스크)뿐인 경우 이 VM의 스냅숏 또는 이미지를 만들고 이 스냅숏 또는 이미지로부터 VM을 만들 수 있습니다.

스냅숏은 포함하는 디스크 이외의 어떠한 디스크도 인식하지 않습니다. 따라서 스트라이프 등의 여러 디스크를 조정해야 하는 시나리오에서 사용하는 경우 문제가 될 수 있습니다. 스냅숏은 서로 조정할 수 있어야 하는데 현재는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

디스크 유형에 대한 문서에서 Azure에서 제공하는 개별 디스크 유형과 사용자 요구에 잘 맞는 유형은 무엇인지에 대해 알아보세요.
