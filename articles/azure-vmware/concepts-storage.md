---
title: 개념 - 스토리지
description: Azure VMware 솔루션 프라이빗 클라우드의 주요 스토리지 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: 83a4a52f8414e656b5bc688796db6e93a53d4d76
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801624"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Azure VMware 솔루션 스토리지 개념

Azure VMware 솔루션 프라이빗 클라우드는 VMware vSAN을 사용하는 클러스터 차원의 네이티브 스토리지를 제공합니다. 클러스터의 각 호스트에 있는 모든 로컬 스토리지는 vSAN 데이터 저장소에서 사용되며, 미사용 데이터 암호화를 사용할 수 있으며 기본적으로 사용하도록 설정됩니다. Azure Storage 리소스를 사용하여 프라이빗 클라우드의 스토리지 기능을 확장할 수 있습니다.

## <a name="vsan-clusters"></a>vSAN 클러스터

각 클러스터 호스트의 로컬 스토리지는 vSAN 데이터 저장소의 일부로 사용됩니다. 모든 디스크 그룹은 호스트당 원시 SSD 기반 용량 15.4TB가 있는 1.6TB의 NVMe 캐시 계층을 사용합니다. 클러스터의 원시 용량 계층 크기는 호스트당 용량에 호스트 수를 곱한 값입니다. 예를 들어, 4개의 호스트 클러스터는 vSAN 용량 계층에서 61.6TB 원시 용량을 제공합니다.

클러스터 호스트의 로컬 스토리지는 클러스터 차원의 vSAN 데이터 저장소에 사용됩니다. 모든 데이터 저장소는 프라이빗 클라우드 배포의 일부로 생성되며 즉시 사용할 수 있습니다. cloudadmin 사용자 및 CloudAdmin 그룹의 모든 사용자는 다음과 같은 vSAN 권한으로 데이터 저장소를 관리할 수 있습니다.

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>미사용 데이터 암호화

vSAN 데이터 저장소는 기본적으로 미사용 데이터 암호화를 사용합니다. 암호화 솔루션은 KMS 기반이며 키 관리를 위한 vCenter 작업을 지원합니다. 키는 암호화되고 Azure Key Vault 마스터 키로 래핑됩니다. 클러스터에서 호스트를 제거하면 SSD의 데이터가 즉시 무효화됩니다.

## <a name="scaling"></a>크기 조정

클러스터에 호스트를 추가하여 네이티브 클러스터 스토리지 용량 크기를 스케일링합니다. AVS36 호스트를 사용하는 클러스터의 경우 각 호스트가 추가되면서 원시 클러스터 전체 용량이 15.4TB씩 증가합니다. 호스트를 클러스터에 추가하는 데 10분 정도 걸립니다.  클러스터 스케일링에 대한 지침은 [프라이빗 클라우드 스케일링 자습서][tutorial-scale-private-cloud]를 참조하세요.

## <a name="azure-storage-integration"></a>Azure Storage 통합

프라이빗 클라우드에서 실행되는 워크로드에서 Azure Storage 서비스를 사용할 수 있습니다. Azure Storage 서비스에는 스토리지 계정, Table Storage 및 Blob Storage가 포함됩니다. Azure Storage 서비스에 대한 워크로드 연결은 인터넷을 트래버스하지 않습니다. 이 연결을 통해 보안을 강화하고 프라이빗 클라우드 워크로드에서 SLA 기반 Azure Storage 서비스를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution 스토리지 개념을 살펴보았으므로 다음에 대해 알아볼 수 있습니다.

- [프라이빗 클라우드 ID 개념](concepts-identity.md).
- [Azure VMware 솔루션에 대한 vSphere 역할 기반 액세스 제어](concepts-identity.md).
- [Azure VMware Solution 리소스를 사용하는 방법](enable-azure-vmware-solution.md)
- [Azure VMware를 포함하는 Azure NetApp Files으로 Azure NetApp Files 솔루션](netapp-files-with-azure-vmware-solution.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
