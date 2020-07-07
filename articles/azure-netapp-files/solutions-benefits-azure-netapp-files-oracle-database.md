---
title: Oracle Database에서 Azure NetApp Files 사용의 이점 | Microsoft Docs
description: 기술에 대해 설명 하 고 dNFS (Oracle Direct NFS)와 기존 NFS 클라이언트 간의 성능 비교를 제공 합니다. Azure NetApp Files에서 dNFS를 사용 하는 경우의 장점을 보여 줍니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 56322dc8def288ed388713e143f6b77816360ba3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82117049"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Oracle Database에서 Azure NetApp Files를 사용할 경우의 이점

Oracle Direct NFS (dNFS)를 사용 하면 운영 체제의 자체 NFS 드라이버 보다 높은 성능을 높일 수 있습니다. 이 문서에서는 기술에 대해 설명 하 고 dNFS와 기존 NFS 클라이언트 (커널 NFS) 간의 성능 비교를 제공 합니다. 또한 Azure NetApp Files와 함께 dNFS를 사용 하는 경우의 장점과 편의성을 보여 줍니다.  

## <a name="how-oracle-direct-nfs-works"></a>Oracle Direct NFS 작동 방법

다음 요약에서는 Oracle Direct NFS가 높은 수준에서 작동 하는 방식을 설명 합니다.

* Oracle Direct NFS는 운영 체제 버퍼 캐시를 무시 합니다. 데이터는 사용자 공간에서 한 번만 캐시 되므로 메모리 복사본의 오버 헤드를 없앨 수 있습니다.  

* 기존 NFS 클라이언트는 아래와 같이 단일 네트워크 흐름을 사용 합니다.    

    ![단일 네트워크 흐름을 사용 하는 기존 NFS 클라이언트](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle Direct NFS는 여러 네트워크 흐름에서 네트워크 트래픽을 부하 분산 하 여 성능을 향상 시킵니다. 아래와 같이 테스트 되 고 아래와 같이 650 개별 네트워크 연결이 Oracle Database에 의해 동적으로 설정 되었습니다.  

    ![Oracle Direct NFS 성능 향상](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[DIRECT nfs에 대 한 ORACLE FAQ](http://www.orafaq.com/wiki/Direct_NFS) 는 Oracle dnfs가 최적화 된 NFS 클라이언트 임을 보여 줍니다. NAS 저장 장치 (TCP/IP를 통해 액세스 가능)에 있는 NFS 저장소에 대 한 빠르고 확장 가능한 액세스를 제공 합니다. dNFS는 주로 DAS 또는 SAN 저장소에서 사용 되는 ASM과 마찬가지로 데이터베이스 커널에 빌드됩니다. 따라서 *지침은 NAS 저장소를 구현할 때 dNFS를 사용 하 고 SAN storage를 구현할 때 ASM을 사용 하는 것입니다.*

dNFS는 Oracle 18c의 기본 옵션입니다.

dNFS는 Oracle Database 11g부터 사용할 수 있습니다. 아래 다이어그램은 dNFS를 네이티브 NFS와 비교 합니다. DNFS를 사용 하는 경우 Azure 가상 머신에서 실행 되는 Oracle 데이터베이스는 네이티브 NFS 클라이언트 보다 많은 i/o를 구동 합니다.

![Oracle 및 네이티브 NFS와 dNFS 비교 Azure NetApp Files](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

두 명령을 실행 하 고 데이터베이스를 다시 시작 하 여 dNFS를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

사용 하도록 설정 하려면  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

사용 하지 않도록 설정 하려면  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Oracle Direct NFS와 결합 된 Azure NetApp Files

Azure NetApp Files 서비스를 사용 하 여 Oracle dNFS의 성능을 향상 시킬 수 있습니다. 서비스는 응용 프로그램 성능에 대 한 전체 제어를 제공 합니다. 매우 까다로운 응용 프로그램을 충족할 수 있습니다. Oracle dNFS와 Azure NetApp Files를 조합 하면 워크 로드에 매우 유용 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files를 사용하는 솔루션 아키텍처](azure-netapp-files-solution-architectures.md)
- [Azure의 Oracle 애플리케이션 및 솔루션 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)
