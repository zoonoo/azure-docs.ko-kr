---
title: 오라클 데이터베이스와 함께 Azure NetApp 파일 사용의 이점 | 마이크로 소프트 문서
description: 이 기술을 설명하고 오라클 다이렉트 NFS(dNFS)와 기존 NFS 클라이언트 간의 성능 비교를 제공합니다. Azure NetApp 파일에서 dNFS를 사용하는 이점을 보여 주십습니다.
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
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: a73da39dafcc8be78fbe1c023693ffa4a19aa1d3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085011"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Oracle Database에서 Azure NetApp Files를 사용할 경우의 이점

오라클 다이렉트 NFS(dNFS)를 사용하면 운영 체제의 자체 NFS 드라이버보다 더 높은 성능을 구동할 수 있습니다. 이 문서에서는 기술을 설명하고 dNFS와 기존 NFS 클라이언트(커널 NFS) 간의 성능 비교를 제공합니다. 또한 Azure NetApp 파일에서 dNFS를 사용하는 이점과 용이성을 보여 준다.  

## <a name="how-oracle-direct-nfs-works"></a>오라클 다이렉트 NFS 작동 방식

오라클 다이렉트 NFS(dNFS)는 운영 체제의 버퍼 캐시를 무시합니다. 데이터는 사용자 공간에 한 번만 캐시되므로 메모리 복사본의 오버헤드가 제거됩니다.  

기존 NFS 클라이언트는 다음 예제와 같이 단일 네트워크 흐름을 사용합니다. 

![단일 네트워크 흐름을 사용하는 기존 NFS 클라이언트](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

반면 Oracle dNFS는 여러 네트워크 흐름에서 네트워크 트래픽을 부하 분산하여 성능을 향상시킵니다. 이 기능을 통해 Oracle 데이터베이스는 아래 예제와 같이 상당수의 고유한 네트워크 연결을 동적으로 설정할 수 있습니다.  

![오라클 다이렉트 NFS 성능 향상](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[직접 NFS용 오라클 FAQ는](http://www.orafaq.com/wiki/Direct_NFS) 오라클 dNFS가 최적화된 NFS 클라이언트임을 보여줍니다. NAS 스토리지 장치에 있는 NFS 스토리지에 빠르고 확장 가능한 액세스를 제공합니다(TCP/IP를 통해 액세스할 수 있음). dNFS는 주로 DAS 또는 SAN 저장소와 함께 사용되는 ASM과 같은 데이터베이스 커널에 내장되어 있습니다. 따라서 *NAS 저장소를 구현할 때 dNFS를 사용하고 SAN 저장소를 구현할 때 ASM을 사용하는 것이 지침입니다.*

dNFS는 오라클 18c의 기본 옵션입니다.

dNFS는 오라클 데이터베이스 11g부터 사용할 수 있습니다. 아래 다이어그램은 dNFS와 기본 NFS를 비교합니다. dNFS를 사용하는 경우 Azure 가상 컴퓨터에서 실행되는 Oracle 데이터베이스는 기본 NFS 클라이언트보다 더 많은 I/O를 구동할 수 있습니다.

![오라클 과 Azure NetApp 파일 네이티브 NFS와 dNFS의 비교](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

두 개의 명령을 실행하고 데이터베이스를 다시 시작하여 dNFS를 활성화하거나 비활성화할 수 있습니다.

사용하려면 다음을 수행하십시오.  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

비활성화하려면 다음을 수행하십시오.  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>오라클 다이렉트 NFS와 결합된 Azure 넷앱 파일

Azure NetApp 파일 서비스를 통해 Oracle dNFS의 성능을 향상시킬 수 있습니다. 이 서비스를 통해 응용 프로그램 성능을 완전히 제어할 수 있습니다. 그것은 매우 까다로운 응용 프로그램을 충족 할 수 있습니다. Oracle dNFS와 Azure NetApp 파일의 조합은 워크로드에 큰 이점을 제공합니다.

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files를 사용하는 솔루션 아키텍처](azure-netapp-files-solution-architectures.md)
- [Azure의 Oracle 응용 프로그램 및 솔루션 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)
