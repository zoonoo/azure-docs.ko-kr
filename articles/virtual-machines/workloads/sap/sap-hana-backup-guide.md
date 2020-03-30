---
title: Azure Virtual Machines의 SAP HANA Backup 가이드 | Microsoft Docs
description: Azure 가상 머신의 SAP HANA에 대한 두 가지 주요 백업 방법을 제공합니다
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255254"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA Backup 가이드

## <a name="getting-started"></a>시작

Azure 가상 머신에서 실행되는 SAP HANA에 대한 백업 가이드에서는 Azure 관련 항목만 설명합니다. 일반 SAP HANA 백업 관련 항목은 SAP HANA 설명서를 참조하십시오. 우리는 당신이 원칙 데이터베이스 백업 전략에 익숙할 것으로 예상, 이유, 그리고 동기는 건전하고 유효한 백업 전략을 가지고, 당신의 회사가 백업 절차, 백업 의 보존 기간 및 복원에 대한 요구 사항을 알고 절차.

SAP HANA는 Azure M-Series와 같은 다양한 Azure VM 유형에서 공식적으로 지원됩니다. SAP HANA 인증 Azure VM 및 HANA 대형 인스턴스 단위의 전체 목록은 [인증된 IaaS 플랫폼 찾기를](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)참조하십시오. Microsoft Azure는 SAP HANA가 실제 서버에서 가상화되지 않은 실행되는 여러 단위를 제공합니다. 이 서비스를 [HANA 대형 인스턴스라고](hana-overview-architecture.md)합니다. 이 가이드는 HANA 대형 인스턴스에 대한 백업 프로세스 및 도구를 다루지 않습니다. 그러나 Azure 가상 컴퓨터로 제한될 것입니다. HANA 대형 인스턴스를 사용 하 여 백업/복원 프로세스에 대 한 자세한 내용은 문서를 읽어 [HLI 백업 및 복원](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore).

이 문서의 초점은 Azure 가상 컴퓨터에서 SAP HANA에 대한 세 가지 백업 가능성에 중점을 둡니다.

- Azure 백업 [서비스를](https://docs.microsoft.com/azure/backup/backup-overview) 통한 HANA 백업 
- Azure Linux Virtual Machine의 파일 시스템에 HANA 백업([파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) 참조)
- Azure 저장소 Blob 스냅숏 기능을 수동으로 사용 하거나 Azure 백업 서비스를 사용 하 여 저장소 스냅숏을 기반으로 HANA 백업


SAP HANA는 타사 백업 도구에서 SAP HANA와 직접 통합할 수 있게 하는 백업 API를 제공합니다. Azure Backup 서비스 또는 [Commvault와](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) 같은 제품은 이 독점 인터페이스를 사용하여 SAP HANA 데이터베이스를 트리거하거나 로그 백업을 다시 실행합니다. 


Azure에서 지원되는 SAP 소프트웨어를 찾는 방법에 대한 정보는 [Azure 배포에 대해 지원되는 SAP 소프트웨어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)문서에서 찾을 수 있습니다.

## <a name="azure-backup-service"></a>Azure Backup 서비스

첫 번째 시나리오는 Azure Backup Service가 SAP HANA `backint` 인터페이스를 사용하여 SAP HANA 데이터베이스에서 스트리밍 백업을 수행하는 시나리오입니다. 또는 Azure Backup 서비스의 보다 일반적인 기능을 사용하여 응용 프로그램 일관된 디스크 스냅숏을 만들고 Azure Backup 서비스로 전송합니다.

Azure Backup은 [backint라는](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)독점 SAP HANA 인터페이스를 사용하여 SAP HANA의 백업 솔루션으로 통합되고 인증되었습니다. 솔루션, 해당 기능 및 사용 가능한 Azure 지역에 대한 자세한 내용은 [Azure VM에서 SAP HANA 데이터베이스 백업을 위한 지원 매트릭스](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)문서를 참조하십시오. HANA에 대한 Azure 백업 서비스에 대한 자세한 내용 및 원칙은 [Azure VM의 SAP HANA 데이터베이스 백업에 대한](https://docs.microsoft.com/azure/backup/sap-hana-db-about)도움말을 참조하십시오. 

Azure Backup 서비스를 활용할 수 있는 두 번째 가능성은 Azure Premium 저장소의 디스크 스냅숏을 사용하여 응용 프로그램 일관된 백업을 만드는 것입니다. [Azure Ultra 디스크](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd) 및 Azure [NetApp 파일과](https://azure.microsoft.com/services/netapp/) 같은 다른 HANA 인증 Azure 저장소는 Azure 백업 서비스를 통해 이러한 종류의 스냅숏을 지원하지 않습니다. 이 기사 읽기:

- [Azure에서 VM 백업 인프라 계획](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Azure Linux VM의 애플리케이션 일치 백업](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

이 활동 시퀀스가 나타납니다.

- Azure Backup은 응용 프로그램을 일관된 상태로 SAP HANA를 배치하는 사전 스냅숏 스크립트를 실행해야 합니다.
- 이 일관된 상태가 확인되면 Azure Backup은 디스크 스냅숏을 실행합니다.
- 스냅숏을 완료한 후 Azure Backup은 스냅숏 이전 스크립트에서 수행한 작업을 실행 취소합니다.
- 성공적으로 실행되면 Azure Backup은 데이터를 백업 자격 증명 모음으로 스트리밍합니다.

SAP HANA의 경우 대부분의 고객은 SAP HANA 다시 수행 로그가 포함된 볼륨에 Azure 쓰기 가속기를 사용하고 있습니다. Azure 백업 서비스는 이러한 볼륨을 스냅숏에서 자동으로 제외합니다. 이러한 배제는 HANA의 복원 능력에 해를 끼치지 않습니다. 거의 모든 다른 SAP 지원 DBMS로 복원 하는 기능을 차단 하지만.

이 가능성의 단점은 스냅샷 전후 스크립트를 직접 개발해야 한다는 사실입니다. 사전 스냅숏 스크립트는 HANA 스냅숏을 만들고 최종 예외 사례를 처리해야 합니다. 스냅숏 후 스크립트는 HANA 스냅숏을 다시 삭제해야 합니다. 필요한 논리에 대한 자세한 내용은 [#2039883 SAP 지원 노트로](https://launchpad.support.sap.com/#/notes/2039883)시작합니다. 이 문서에서는 '저장소 스냅숏을 생성할 때 SAP HANA 데이터 일관성' 섹션의 고려 사항은 이러한 종류의 백업에 완전히 적용됩니다.

> [!NOTE]
> 여러 데이터베이스 컨테이너를 사용하는 배포에서 SAP HANA에 대한 디스크 스냅샷 기반 백업은 HANA 2.0 SP04의 최소 릴리스가 필요합니다.
> 

이 문서의 후반부에서 저장소 스냅숏에 대한 자세한 내용을 참조하십시오.

![그림: 현재 VM 상태를 저장하는 두 가지 방법](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>기타 HANA 백업 방법
고려할 수 있는 세 가지 다른 백업 방법 또는 경로가 있습니다.

- Azure NetApp 파일(ANF)을 기반으로 하는 NFS 공유에 대해 백업합니다. ANF는 백업을 저장하는 볼륨의 스냅샷을 다시 만들 수 있습니다. 결국 백업을 작성하는 데 필요한 처리량을 감안할 때 이 솔루션은 비용이 많이 드는 방법이 될 수 있습니다. HANA가 Azure 네이티브 NFS 공유에 직접 백업을 작성할 수 있기 때문에 설정하기는 쉽지만
- 표준 SSD 또는 Azure 프리미엄 저장소의 VM 연결 디스크에 대해 HANA 백업을 실행합니다. 다음 단계에서 Azure Blob 저장소에 대해 해당 백업 파일을 복사할 수 있습니다. 이 전략은 가격 현명한 매력이 될 수 있습니다
- 표준 SSD 또는 Azure 프리미엄 저장소의 VM 연결 디스크에 대해 HANA 백업을 실행합니다. 다음 단계로 디스크는 정기적으로 스냅숏을 가져옵니다. 첫 번째 스냅숏 후 증분 스냅숏을 사용하여 비용을 절감할 수 있습니다.

![그림: VM 내부에서 SAP HANA 파일 백업을 수행하는 옵션](media/sap-hana-backup-guide/other-hana-backup-paths.png)

이 그림에서는 VM 내부에서 SAP HANA 파일 백업을 수행한 다음 다양한 도구를 사용하여 다른 위치에 HANA 백업 파일을 저장하는 옵션을 보여 줍니다. 그러나 타사 백업 서비스 또는 Azure Backup 서비스와 관련되지 않은 모든 솔루션에는 몇 가지 공통점이 있습니다. 보존 관리, 자동 복원 프로세스 및 Azure Backup 서비스 또는 기타 특수한 타사 백업 제품군 및 서비스가 제공하는 자동 시점 간 복구 제공과 같은 일부 를 나열할 수 있습니다. 이러한 타사 서비스의 대부분은 Azure에서 실행할 수 있습니다. 


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
백업 방법과 는 별개로 다른 시스템에 대해 테스트 복원을 실행하는 것은 절대적으로 필요합니다. 이 방법은 백업이 올바른지, 백업 및 복원을 위한 내부 프로세스가 예상대로 작동하는지 확인하는 방법을 제공합니다. 백업을 복원하는 것은 인프라 요구 사항으로 인해 온-프레미스에서 장애물이 될 수 있지만 이러한 목적을 위해 필요한 리소스를 일시적으로 제공함으로써 클라우드에서 수행하는 것이 훨씬 쉽습니다. 복원 할 수있는 능력에 백업 파일을 확인할 수있는 HANA와 함께 제공되는 도구가 있는 것이 맞습니다. 그러나 자주 복원 연습의 목적은 데이터베이스 복원 프로세스를 테스트 하 고 운영 직원과 함께 해당 프로세스를 학습 하는 것입니다.

간단한 복원을 수행하고 HANA가 가동 중인지 확인하는 것만으로는 충분하지 않다는 것을 명심해야 합니다. 복원된 데이터베이스가 잘 되었는지 확인하기 위해 테이블 일관성 검사를 실행해야 합니다. SAP HANA는 [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584)에서 설명하는 여러 종류의 일관성 검사를 제공합니다.

테이블 일관성 검사에 대한 내용은 SAP 웹 사이트의 [테이블 및 카탈로그 일관성 검사](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)(영문)에서 찾을 수도 있습니다.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA 백업 및 스토리지 스냅샷의 장단점

SAP에서는 HANA 백업과 스토리지 스냅샷을 비교하여 어느 한 쪽을 선택하지 않습니다. 장단점을 나열하여 상황과 사용 가능한 스토리지 기술에 따라 사용할 대상을 결정할 수 있습니다([Backup 및 복구 전략 계획](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)(영문) 참조).

Azure에서 Azure Blob 스냅숏 기능은 여러 디스크에서 파일 시스템 일관성을 제공하지&#39;[합니다(PowerShell을 사용하는 Blob 스냅숏 사용](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)참조). 

또한 [스냅샷 요금 청구 방법 이해](/rest/api/storageservices/understanding-how-snapshots-accrue-charges) 문서에서 설명한 대로 Blob 스냅샷을 자주 사용하는 경우 청구의 함축적 의미를 이해해야 합니다. 이는 Azure 가상 디스크를 사용하는 것만큼 명확하지 않기 때문입니다.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>스토리지 스냅샷을 만들 때의 SAP HANA 데이터 일관성

앞에서 설명한 것처럼 저장소 스냅숏을 생성할 때 Azure Backup, 파일 시스템 및 응용 프로그램 일관성의 스냅숏 백업 기능을 설명하는 것이 필수적입니다. 문제를 방지하는 가장 쉬운 방법은 SAP HANA, 심지어는 전체 가상 컴퓨터를 종료하는 것입니다. 프로덕션 인스턴스에 대해 불가능한 것입니다.

> [!NOTE]
> 여러 데이터베이스 컨테이너를 사용하는 배포에서 SAP HANA에 대한 디스크 스냅샷 기반 백업은 HANA 2.0 SP04의 최소 릴리스가 필요합니다.
> 

Azure 저장소는 스냅숏 프로세스 중에 VM에 연결된 여러 디스크 또는 볼륨에서 파일 시스템 일관성을 제공하지 않습니다. 즉, 스냅숏 동안 응용 프로그램 일관성을 응용 프로그램에서 제공 해야 합니다.,이 경우 SAP HANA 자체. [SAP Note 2039883에는](https://launchpad.support.sap.com/#/notes/2039883) 스토리지 스냅샷별 SAP HANA 백업에 대한 중요한 정보가 있습니다. 예를 들어 XFS 파일 시스템에서는 응용 프로그램 일관성을 제공하기 위해 저장소 **\_스냅숏을** 시작하기 전에 xfs 동결을 실행해야 합니다(xfs [\_freeze(8) - Linux 맨 페이지에서](https://linux.die.net/man/8/xfs_freeze) **\_xfs 동결에**대한 자세한 내용을 참조하세요).

다음 단계에서는 네 개의 Azure 가상 디스크를 확장하는 XFS 파일 시스템이 있다고 가정하여 HANA 데이터 영역을 나타내는 일관된 스냅샷을 제공합니다.

1. HANA 데이터 스냅샷 준비 만들기
1. 모든 디스크/볼륨의 파일 시스템 고정(예: **xfs\_고정**사용)
1. Azure에서 필요한 모든 Blob 스냅샷 만들기
1. 파일 시스템 고정 취소(unfreeze)
1. HANA 데이터 스냅샷 확인(스냅샷 삭제)

Azure Backup의 기능을 사용하여 응용 프로그램 일관된 스냅숏 백업을 수행하는 경우 사전 스냅숏 스크립트에 대해 #1 코딩/스크립팅해야 합니다. Azure 백업 서비스는 #2 단계와 #3 실행합니다. #4 단계 및 #5 스냅숏 후 스크립트에서 코드에서 다시 제공해야 합니다. Azure 백업 서비스를 사용하지 않는 경우 #2 코딩/스크립트 단계와 직접 #3 합니다.
HANA 데이터 스냅숏 생성에 대한 자세한 내용은 다음 문서에서 확인할 수 있습니다.

- [HANA 데이터 스냅샷] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- 단계 #1 수행하기 위한 자세한 내용은 [데이터 스냅숏 만들기(네이티브 SQL)에서](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 찾을 수 있습니다. 
- 단계 #5 필요에 따라 HANA 데이터 스냅숏을 확인/삭제하는 세부 정보는 데이터 [스냅숏 만들기(네이티브 SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 문서에서 확인할 수 있습니다. 

HANA 스냅샷을 확인하는 것이 중요합니다. &quot;기록 중 복사&quot;(Copy-on-Write)로 인해 스냅샷 준비 모드에 있는 동안 SAP HANA에 추가 디스크 공간이 필요하지 않을 수 있습니다. 또한 먼저 SAP HANA 스냅샷이 확인되어야 새 백업을 시작할 수 있습니다.


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 백업 일정 전략

백업 및 복구 전략 계획 SAP HANA 문서에는 [백업을](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) 수행하는 기본 계획이 명시되어 있습니다. HanA와 다른 DBMS에 대한 경험에 대한 SAP 설명서를 사용하여 SAP HANA의 백업/복원 전략 및 프로세스를 정의합니다. 다양한 유형의 백업 순서와 보존 기간은 제공해야 하는 SLA에 크게 의존합니다.


### <a name="sap-hana-backup-encryption"></a>SAP HANA 백업 암호화

SAP HANA에서는 데이터 및 로그의 암호화를 제공합니다. SAP HANA 데이터 및 로그가 암호화되지 않은 경우 백업은 기본적으로 암호화되지 않습니다. 그러나 SAP HANA는 [SAP HANA 백업](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)암호화에 설명된 별도의 백업 암호화를 제공합니다. SAP HANA의 이전 릴리스를 실행하는 경우 백업 암호화가 이미 제공된 기능의 일부인지 확인해야 할 수 있습니다.  


## <a name="next-steps"></a>다음 단계
* [파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) - 파일 기반 백업 옵션을 설명합니다.
* Azure에서 SAP HANA의 재해 복구를 위한 고가용성 및 계획을 수립하는 방법을 알아보려면 [Azure에서 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구를](hana-overview-high-availability-disaster-recovery.md)참조하십시오.
