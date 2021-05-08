---
title: Azure Virtual Machines의 SAP HANA Backup 가이드 | Microsoft Docs
description: Azure 가상 머신의 SAP HANA에 대한 두 가지 주요 백업 방법을 제공합니다
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0004afb5895d7549e5db8ad5e53b52fa17991520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667913"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA Backup 가이드

## <a name="getting-started"></a>시작하기

Azure 가상 머신에서 실행되는 SAP HANA에 대한 백업 가이드에서는 Azure 관련 항목만 설명합니다. 일반 SAP HANA 백업 관련 항목은 SAP HANA 설명서를 확인하세요. 타당하고 유효한 백업 전략을 수립하기 위한 원칙 데이터베이스 백업 전략, 이유 및 동기를 파악하고 있어야 하며, 백업 프로시저, 백업 보존 기간 및 복원 프로시저에 대한 소속 회사의 요구 사항을 잘 알고 있어야 합니다.

SAP HANA는 Azure M-Series와 같은 다양한 Azure VM 유형에서 공식적으로 지원됩니다. SAP HANA 인증 Azure VM 및 HANA의 대규모 인스턴스 유닛 전체 목록을 보려면 [Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)(공인 IaaS 플랫폼 찾기)를 확인하세요. Microsoft Azure는 SAP HANA가 물리 서버에서 가상화되지 않은 상태로 실행되는 다양한 단위를 제공합니다. 해당 서비스를 [HANA 대규모 인스턴스](hana-overview-architecture.md)라고 합니다. 본 가이드에서는 HANA 대규모 인스턴스 관련 백업 프로세스 및 도구에 대해서는 다루지 않습니다. Azure Virtual Machines로 한정됩니다. HANA 대규모 인스턴스 관련 백업 및 복원 프로세스에 대한 세부 정보는[HLI 백업 및 복원](./hana-backup-restore.md) 문서를 확인하세요.

본 문서는 Azure Virtual Machines의 SAP HANA에 대한 다음 세 가지 백업 가능성을 중점적으로 다룹니다.

- [Azure Backup 서비스](../../../backup/backup-overview.md)를 통한 HANA 백업 
- Azure Linux Virtual Machine의 파일 시스템에 HANA 백업([파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) 참조)
- Azure Storage Blob 스냅샷 기능을 통해 수동으로 또는 Azure Backup 서비스를 통한 스토리지 스냅샷 기반의 HANA 백업


SAP HANA는 타사 백업 도구에서 SAP HANA와 직접 통합할 수 있게 하는 백업 API를 제공합니다. Azure Backup 서비스나 [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) 등의 제품은 SAP HANA 데이터베이스를 트리거 하거나 로그 백업을 다시 실행하기 위해 이러한 고유 인터페이스를 사용하고 있습니다. 


Azure에서 지원되는 SAP 소프트웨어를 찾는 방법에 대한 정보는 [Azure 배포를 위해 지원하는 SAP 소프트웨어](./sap-supported-product-on-azure.md) 문서에서 찾을 수 있습니다.

## <a name="azure-backup-service"></a>Azure Backup 서비스

표시되는 첫 번째 시나리오는 Azure Backup 서비스가 SAP HANA 데이터베이스로부터 스트리밍 백업을 실행하기 위해 SAP HANA `backint` 인터페이스를 사용하는 시나리오입니다. 아니면 Azure Backup 서비스의 보다 일반적인 기능을 사용하여 애플리케이션 일관성 디스크 스냅샷을 만들고 Azure Backup 서비스에 전송합니다.

Azure Backup은 [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)라고 불리는 고유의 SAP HANA 인터페이스를 사용해 SAP HANA용 백업 솔루션으로 통합되며 인증됩니다. 솔루션과 그 기능 및 이를 활용할 수 있는 Azure 지역에 대한 추가적인 세부 정보는 [Azure VM의 SAP HANA 데이터베이스 백업용 지원 매트릭스](../../../backup/sap-hana-backup-support-matrix.md#scenario-support) 문서를 참조하세요. HANA에 대한 Azure Backup 서비스 관련 세부 정보 및 원칙은 [Azure VM 내의 SAP HANA 데이터베이스 백업 관련](../../../backup/sap-hana-db-about.md) 문서를 참조하세요. 

Azure Backup 서비스를 활용하는 두 번째 가능성은 Azure Premium Storage의 디스크 스냅샷을 사용해 애플리케이션 일관성 백업을 생성하는 것입니다. [Azure Ultra Disk](../../disks-enable-ultra-ssd.md) 및 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 등의 기타 HANA 인증 Azure 스토리지는 Azure Backup 서비스를 통해 이러한 종류의 스냅샷을 지원하지 않습니다. 다음 문서를 읽습니다.

- [Azure에서 VM 백업 인프라 계획](../../../backup/backup-azure-vms-introduction.md)
- [Azure Linux VM의 애플리케이션 일치 백업](../../../backup/backup-azure-linux-app-consistent.md) 

해당 작업 시퀀스는 다음과 같습니다.

- Azure Backup은 일치 상태의 애플리케이션(이번 경우에는 SAP HANA)을 배치하는 스냅샷 전 스크립트를 실행해야 합니다.
- 일치 상태가 확인되면 Azure Backup이 디스크 스냅샷을 실행합니다.
- 스냅샷 완료 후, Azure Backup은 스냅샷 전 스크립트에서 수행한 작업을 실행 취소합니다.
- 성공적으로 실행을 마치면 Azure Backup은 Backup 자격 증명 모음으로 데이터를 스트림합니다.

SAP HANA의 경우 대부분의 고객은 SAP HANA 다시 실행 로그를 포함하는 볼륨에 대해 Azure 쓰기 가속기를 사용합니다. Azure Backup 서비스는 자동으로 이러한 볼륨을 스냅샷에서 제외합니다. 이러한 제외가 HANA의 복원 기능에 피해를 주는 것은 아닙니다. 하지만 거의 모든 다른 SAP 지원 DBMS의 복원 기능은 차단합니다.

해당 가능성의 단점은 스냅샷 전 스크립트와 스냅샷 후 스크립트를 직접 개발해야 한다는 사실입니다. 스냅샷 전 스크립트에는 HANA 스냅샷 생성 및 최종 예외 사례 처리가 필요합니다. 반면, 스냅샷 후 스크립트에서 HANA 스냅샷을 다시 삭제해야 합니다. 필요한 논리에 대한 세부 정보는 [SAP 지원 메모 #2039883](https://launchpad.support.sap.com/#/notes/2039883)에서 참조하세요. 해당 문서 내용 중 '스토리지 스냅샷 작성 시 SAP HANA 데이터 일치' 섹션에 대한 고려 사항은 이러한 백업 유형에 완전히 적용됩니다.

> [!NOTE]
> 여러 데이터베이스 컨테이너를 사용한 배포의 SAP HANA를 위한 디스크 스냅샷 기반 백업에는 최소 HANA 2.0 SP04 릴리스가 필요합니다.
> 

본 문서의 뒷부분에서 스토리지 스냅샷 관련 세부 정보를 확인하세요.

![그림: 현재 VM 상태를 저장하는 두 가지 방법](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>기타 HANA 백업 방법
기타 백업 방법 또는 경로 중 고려할 수 있는 것은 다음 세 가지가 있습니다.

- ANF(Azure NetApp Files) 기반 NFS 공유에 대한 백업 ANF에는 백업을 저장하는 볼륨의 스냅샷을 만들 수 있는 기능이 있습니다. 해당 백업을 작성하는 데 결국 필요한 처리량을 고려하면 이 솔루션은 비용이 많이 드는 방법일 수 있습니다. HANA가 Azure 네이티브 NFS 공유에 바로 백업을 작성할 수 있으므로 설정하기는 쉽습니다.
- 표준 SSD 또는 Azure Premium Storage의 VM에 연결된 디스크에 대해 HANA 백업 실행 다음 단계로 해당 백업 파일을 Azure Blob Storage에 대해 복사할 수 있습니다. 이러한 전략은 비용면에서 매력이 있습니다.
- 표준 SSD 또는 Azure Premium Storage의 VM에 연결된 디스크에 대해 HANA 백업 실행 다음 단계로 디스크는 정기적으로 스냅샷 됩니다. 첫 번째 스냅샷 이후에는 점점 많은 스냅샷을 이용해 비용을 줄일 수 있습니다.

![그림: VM 내부에서 SAP HANA 파일 백업을 수행하는 옵션](media/sap-hana-backup-guide/other-hana-backup-paths.png)

이 그림에서는 VM 내부에서 SAP HANA 파일 백업을 수행한 다음 다양한 도구를 사용하여 다른 위치에 HANA 백업 파일을 저장하는 옵션을 보여 줍니다. 그러나 타사 백업 서비스나 Azure Backup 서비스와 관련이 없는 모든 솔루션에는 공통적으로 몇 가지 문제점이 있습니다. 여기에는 보존 관리, 자동 복원 프로세스 및 Azure Backup 서비스나 기타 특수한 타사 백업 제품군 및 서비스가 제공하는 것과 같은 자동 지정 시간 복구 같은 것이 있습니다. 이러한 타사 서비스 중 대부분은 Azure에서 실행할 수 있습니다. 


## <a name="sap-resources-for-hana-backup"></a>HANA 백업을 위한 SAP 리소스

### <a name="sap-hana-backup-documentation"></a>SAP HANA 백업 설명서

- [SAP HANA 관리 소개](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)(영문)
- [Backup 및 복구 전략 계획](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)(영문)
- [ABAP DBACOCKPIT을 사용하여 HANA Backup 예약](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)(영문)
- [데이터 Backup 예약(SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)(영문)
- [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148): SAP HANA 백업에 대한 FAQ
- [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883): SAP HANA 데이터베이스 및 스토리지 스냅샷에 대한 FAQ
- [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529): 백업 및 복구에 부적합한 네트워크 파일 시스템

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA 백업의 정확성을 확인하는 방법
백업 방법과는 무관하게 각각의 시스템에 대해 테스트 복원은 반드시 실행해야 합니다. 이 방법은 백업이 올바른지, 백업 및 복원을 위한 내부 프로세스가 예상대로 작동하는지 확인하는 방법을 제공합니다. 인프라 요구 사항으로 인해 백업을 복원하는 것이 온-프레미스로 장애가 될 수 있지만, 목적을 위해 필요한 리소스를 일시적으로 제공함으로써 클라우드에서 완료하기가 더욱 쉬워집니다. 복원 기능에 대해 백업 파일을 확인할 수 있도록 HANA와 함께 제공되는 도구가 있습니다. 하지만 복원 연습을 자주 수행하는 것은 데이터베이스 복원 프로세스를 테스트하고 해당 프로세스를 운영 담당자가 훈련할 수 있도록 하는 것이 목적입니다.

간단한 복원을 수행하고 HANA가 가동 중인지 확인하는 것만으로는 충분하지 않다는 것을 명심해야 합니다. 테이블 일관성 확인을 실행하여 복원된 데이터베이스가 정상인지 확인해야 합니다. SAP HANA는 [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584)에서 설명하는 여러 종류의 일관성 검사를 제공합니다.

테이블 일관성 검사에 대한 내용은 SAP 웹 사이트의 [테이블 및 카탈로그 일관성 검사](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)(영문)에서 찾을 수도 있습니다.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA 백업 및 스토리지 스냅샷의 장단점

SAP에서는 HANA 백업과 스토리지 스냅샷을 비교하여 어느 한 쪽을 선택하지 않습니다. 장단점을 나열하여 상황과 사용 가능한 스토리지 기술에 따라 사용할 대상을 결정할 수 있습니다([Backup 및 복구 전략 계획](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/ef085cd5949c40b788bba8fd3c65743e.html)(영문) 참조).

Azure에서 Azure Blob 스냅샷 기능이 디스크 여러 개에 걸쳐 파일 시스템 일관성을 제공하지 않는다는 사실을 알고 있어야 합니다([PowerShell과 함께 Blob 스냅샷 사용](/archive/blogs/cie/using-blob-snapshots-with-powershell) 참조). 

또한 [스냅샷 요금 청구 방법 이해](/rest/api/storageservices/understanding-how-snapshots-accrue-charges) 문서에서 설명한 대로 Blob 스냅샷을 자주 사용하는 경우 청구의 함축적 의미를 이해해야 합니다. 이는 Azure 가상 디스크를 사용하는 것만큼 명확하지 않기 때문입니다.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>스토리지 스냅샷을 만들 때의 SAP HANA 데이터 일관성

앞부분에 설명된 것처럼, 스토리지 스냅샷 작성 시에는 Azure Backup의 스냅샷 백업 기능, 파일 시스템 및 애플리케이션 일관성을 반드시 설명해야 합니다. 문제를 방지하는 가장 쉬운 방법은 SAP HANA, 심지어는 전체 가상 컴퓨터를 종료하는 것입니다. 제작 인스턴스에 적합하지 않습니다.

> [!NOTE]
> 여러 데이터베이스 컨테이너를 사용한 배포의 SAP HANA를 위한 디스크 스냅샷 기반 백업에는 최소 HANA 2.0 SP04 릴리스가 필요합니다.
> 

Azure Storage는 스냅샷 프로세스 중에 VM에 연결된 여러 개의 디스크나 볼륨 간에 파일 시스템 또는 볼륨에서 파일 시스템 일관성을 제공하지 않습니다. 즉, 스냅샷 중의 애플리케이션 일관성이 해당 애플리케이션에 의해, 이번 경우에는 SAP HANA 자체적으로 제공되어야 합니다. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)에는 스토리지 스냅샷을 통한 SAP HANA 백업에 대한 중요한 정보가 있습니다. 예를 들어 XFS 파일 시스템을 사용하면 애플리케이션 일관성을 제공하기 위해 스토리지 스냅샷을 시작하기 전에 **xfs\_freeze** 를 실행해야 합니다(**xfs\_freeze** 에 대한 자세한 내용은 [xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze)(xfs_freeze(8) - Linux 기본 페이지) 참조).

다음 단계에서는 네 개의 Azure 가상 디스크를 확장하는 XFS 파일 시스템이 있다고 가정하여 HANA 데이터 영역을 나타내는 일관된 스냅샷을 제공합니다.

1. HANA 데이터 스냅샷 만들기 준비
1. 모든 디스크/볼륨의 파일 시스템을 동결합니다(예: **xfs \_ freeze** 사용).
1. Azure에서 필요한 모든 Blob 스냅샷 생성
1. 파일 시스템 고정 취소(unfreeze)
1. HANA 데이터 스냅샷 확인(해당 스냅샷 삭제)

Azure Backup의 기능을 통해 애플리케이션 일관성 스냅샷 백업을 수행하는 경우, 첫 번째 단계에서는 스냅샷 전 스크립트에서 직접 코딩/스크립팅해야 합니다. Azure Backup 서비스는 두 번째와 세 번째 단계를 실행합니다. 네 번째와 다섯 번째 단계에서도 다시 스냅샷 후 스크립트에서 직접 코딩해야 합니다. Azure Backup 서비스를 사용하지 않는 경우에도 직접 두 번째와 세 번째 단계를 코딩/스크립팅해야 합니다.
HANA 데이터 스냅샷 만들기에 대한 자세한 내용은 다음 문서를 통해 찾을 수 있습니다.

- [HANA 데이터 스냅샷](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- 첫 번째 단계를 수행하는 데 대한 자세한 내용은 [데이터 스냅샷 만들기(네이티브 SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 문서에서 찾을 수 있습니다. 
- 다섯 번째 단계에서 필요한 HANA 데이터 스냅샷 확인 및 삭제 관련 세부 정보는 [데이터 스냅샷 만들기(네이티브 SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)문서에서 찾을 수 있습니다. 

HANA 스냅샷을 확인하는 것이 중요합니다. &quot;기록 중 복사&quot;(Copy-on-Write)로 인해 스냅샷 준비 모드에 있는 동안 SAP HANA에 추가 디스크 공간이 필요하지 않을 수 있습니다. 또한 먼저 SAP HANA 스냅샷이 확인되어야 새 백업을 시작할 수 있습니다.


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 백업 일정 전략

SAP HANA 문서 [백업 및 복구 전략 계획하기](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)에는 백업 실행에 대한 기본적인 계획을 설명합니다. SAP HANA의 백업 및 복원 전략 및 프로세스를 정의할 때는 HANA 관련 SAP 문서와 기타 DBMS에 대해 가진 경험을 활용합니다. 여러가지 유형의 백업 및 보존 기간에 대한 시퀀스는 제공해야 하는 SLA에 따라 달라집니다.


### <a name="sap-hana-backup-encryption"></a>SAP HANA 백업 암호화

SAP HANA에서는 데이터 및 로그의 암호화를 제공합니다. SAP HANA 데이터 및 로그가 암호화되지 않으면 백업도 기본값으로 암호화되지 않습니다. 그러나, SAP HANA는 [SAP HANA 백업 암호화](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html) 문서의 내용과 같이 별도의 백업 암호화를 제공합니다. 이전 버전의 SAP HANA를 실행 중인 경우에는 백업 암호화가 이미 제공된 기능의 일부인지 확인해야 할 수도 있습니다.  


## <a name="next-steps"></a>다음 단계
* [파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) - 파일 기반 백업 옵션을 설명합니다.
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
