---
title: Azure NetApp Files의 새로운 기능 | Microsoft Docs
description: Azure NetApp Files의 새로운 최신 기능 및 향상된 기능에 대한 요약을 제공합니다.
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
ms.topic: overview
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 56ae3ac1331ed054a1fb808ae06e990b67c06bae
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932416"
---
# <a name="whats-new-in-azure-netapp-files"></a>Azure NetApp Files의 새로운 기능

Azure NetApp Files는 정기적으로 업데이트됩니다. 이 문서에서는 새로운 최신 기능과 향상된 기능에 대한 요약을 제공합니다. 

## <a name="september-2020"></a>2020년 9월

* [Azure NetApp Files 지역 간 복제](cross-region-replication-introduction.md)(공개 미리 보기)

  Azure NetApp Files는 이제 지역 간 복제를 지원합니다. 이 새로운 재해 복구 기능을 사용하면 Azure NetApp Files 볼륨을 Azure 지역 간에 빠르고 비용 효율적인 방식으로 복제하여 예측할 수 없는 지역 오류로부터 데이터를 보호할 수 있습니다. Azure NetApp Files 지역 간 복제는 NetApp SnapMirror® 기술을 활용하며, 변경된 블록만 압축되고 효율적인 형식으로 네트워크를 통해 전송됩니다. 이 독점적 기술은 지역 간에 복제하는 데 필요한 데이터의 양을 최소화하여 데이터 전송 비용을 절감합니다. 또한 복제 시간이 단축되므로 더 작은 RPO(복원 지점 목표)를 달성할 수 있습니다.

* [수동 QoS 용량 풀](manual-qos-capacity-pool-introduction.md)(미리 보기)  

    수동 QoS 용량 풀에서는 볼륨의 용량과 처리량을 독립적으로 할당할 수 있습니다. 수동 QoS 용량 풀로 만든 모든 볼륨의 총 처리량은 풀의 총 처리량으로 제한됩니다. 이는 풀 크기와 서비스 수준 처리량의 조합에 따라 결정됩니다. 또는 용량 풀의 [QoS 유형](azure-netapp-files-understand-storage-hierarchy.md#qos_types)이 기본값인 auto(자동)가 될 수 있습니다. 자동 QoS 용량 풀에서 처리량은 볼륨에 할당된 크기 할당량에 비례하여 풀의 볼륨에 자동으로 할당됩니다.

* [LDAP 서명](azure-netapp-files-create-volumes-smb.md)(미리 보기)   

    Azure NetApp Files는 이제 Azure NetApp Files 서비스와 사용자 지정 Active Directory Domain Services 도메인 컨트롤러 간의 보안 LDAP 조회를 위해 LDAP 서명을 지원합니다. 이 기능은 현재 미리 보기로 제공됩니다.

* [AD 인증용 AES 암호화](azure-netapp-files-create-volumes-smb.md)(미리 보기)

    이제 Azure NetApp Files는 SMB 볼륨에 대한 AES 암호화를 사용하도록 설정하기 위해 DC에 대한 LDAP 연결에서 AES 암호화를 지원합니다. 이 기능은 현재 미리 보기로 제공됩니다. 

* 새 [메트릭](azure-netapp-files-metrics.md):   

    * 새 볼륨 메트릭: 
        * *할당된 볼륨 크기*: 프로비저닝된 볼륨 크기
    * 새 풀 메트릭: 
        * *할당된 풀 크기*: 프로비저닝된 풀 크기 
        * *풀의 총 스냅샷 크기*: 풀의 모든 볼륨에 있는 스냅샷 크기의 합계

## <a name="july-2020"></a>2020년 7월

* [이중 프로토콜(NFSv3 및 SMB) 볼륨](create-volumes-dual-protocol.md)

    이제 LDAP 사용자 매핑을 지원하여 동시 이중 프로토콜(NFS v3 및 SMB) 액세스를 허용하는 Azure NetApp Files 볼륨을 만들 수 있습니다. 이 기능을 사용하면 Azure NetApp Files 볼륨에서 데이터를 생성하고 저장하는 Linux 기반 워크로드가 있을 수 있는 사용 사례를 사용할 수 있습니다. 이와 동시에 직원은 Windows 기반 클라이언트와 소프트웨어를 사용하여 동일한 Azure NetApp Files 볼륨에서 새로 생성된 데이터를 분석해야 합니다. 동시 이중 프로토콜 액세스 기능을 사용하면 다른 프로토콜을 사후 분석에 사용하여 워크로드 생성 데이터를 별도의 볼륨에 복사할 필요가 없으므로 스토리지 비용과 운영 시간을 절약할 수 있습니다. 이 기능은 무료이며(일반 [Azure NetApp Files 스토리지 비용](https://azure.microsoft.com/pricing/details/netapp/)은 계속 적용됨) 일반 공급됩니다. [동시 이중 프로토콜 액세스 설명서](create-volumes-dual-protocol.MD)에서 자세히 알아보세요.

* [전송 중 NFS v4.1 Kerberos 암호화](configure-kerberos-encryption.MD)

    Azure NetApp Files는 이제 AES-256 암호화를 사용하는 Kerberos 모드(krb5, krb5i 및 krb5p)에서 NFS 클라이언트 암호화를 지원하여 추가 데이터 보안을 제공합니다. 이 기능은 무료이며(일반 [Azure NetApp Files 스토리지 비용](https://azure.microsoft.com/pricing/details/netapp/)은 계속 적용됨) 일반 공급됩니다. [NFS v4.1 Kerberos 암호화 설명서](configure-kerberos-encryption.MD)에서 자세히 알아보세요.

* [동적 볼륨 서비스 수준 변경](dynamic-change-volume-service-level.MD)

    클라우드는 IT 지출의 유연성을 보장합니다. 이제 볼륨에 대해 원하는 서비스 수준을 사용하는 다른 용량 풀로 볼륨을 이동하여 기존 Azure NetApp Files 볼륨의 서비스 수준을 변경할 수 있습니다. 볼륨에 대한 이러한 내부 서비스 수준 변경에서는 데이터를 마이그레이션할 필요가 없습니다. 또한 볼륨에 대한 데이터 평면 액세스에도 영향을 주지 않습니다. 더 높은 서비스 수준을 사용하여 성능을 향상시키거나 더 낮은 서비스 수준을 사용하여 비용을 최적화하도록 기존 볼륨을 변경할 수 있습니다. 이 기능은 무료이며(일반 [Azure NetApp Files 스토리지 비용](https://azure.microsoft.com/pricing/details/netapp/)은 계속 적용됨) 현재 공개 미리 보기 상태입니다. [동적 볼륨 서비스 수준 변경 설명서](dynamic-change-volume-service-level.md)에 따라 기능 미리 보기에 등록할 수 있습니다.

* [볼륨 스냅샷 정책](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)(미리 보기) 

    Azure NetApp Files를 사용하면 볼륨의 특정 시점 스냅샷을 만들 수 있습니다. 이제 Azure NetApp Files에서 볼륨 스냅샷을 선택한 빈도로 자동으로 만들도록 하는 스냅샷 정책을 만들 수 있습니다. 스냅샷을 매시간, 매일, 매주 또는 매월 주기로 수행하도록 예약할 수 있습니다. 또한 스냅샷 정책의 일부로 유지할 최대 스냅샷 수를 지정할 수도 있습니다. 이 기능은 무료이며(일반 [Azure NetApp Files 스토리지 비용](https://azure.microsoft.com/pricing/details/netapp/)은 계속 적용됨) 현재 미리 보기 상태입니다. [볼륨 스냅샷 정책 설명서](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)에 따라 기능 미리 보기에 등록할 수 있습니다.

* [NFS 루트 액세스 내보내기 정책](azure-netapp-files-configure-export-policy.md)

    이제 Azure NetApp Files를 사용하면 루트 계정에서 볼륨에 액세스할 수 있는지 여부를 지정할 수 있습니다. 

* [스냅샷 경로 숨기기](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    이제 Azure NetApp Files를 사용하면 사용자가 탑재된 볼륨에서 `.snapshot` 디렉터리(NFS 클라이언트) 또는 `~snapshot` 폴더(SMB 클라이언트)를 보고 액세스할 수 있는지 여부를 지정할 수 있습니다.

## <a name="may-2020"></a>2020년 5월

* [백업 정책 사용자](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)(미리 보기)

    Azure NetApp Files를 사용하면 Azure NetApp Files에서 사용하기 위해 만든 컴퓨터 계정에 대한 상승된 권한이 필요한 추가 계정을 포함할 수 있습니다. 지정된 계정은 파일 또는 폴더 수준에서 NTFS 권한을 변경할 수 있습니다. 예를 들어 Azure NetApp Files에서 SMB 파일 공유로 데이터를 마이그레이션하는 데 사용되는 권한 없는 서비스 계정을 지정할 수 있습니다. 백업 정책 사용자 기능은 현재 미리 보기 상태입니다.

## <a name="next-steps"></a>다음 단계
* [Azure NetApp Files란?](azure-netapp-files-introduction.md)
* [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md) 
