---
title: SQL Server VM에 대한 스토리지 구성 | Microsoft Docs
description: 이 항목에서는 Azure가 프로비전하는 동안 SQL Server VM에 대한 스토리지를 구성하는 방법을 설명합니다(Resource Manager 배포 모델). 또한 기존 SQL Server VM에 대한 스토리지를 구성하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 93f01b3c23e08e7f432841d8a77cbe3602bff1c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482149"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server VM에 대한 스토리지 구성

Azure에서 SQL Server 가상 머신 이미지를 구성하는 경우 포털에서는 스토리지 구성을 자동화할 수 있습니다. 스토리지를 VM에 연결하고 해당 스토리지를 SQL Server에 액세스할 수 있도록 하고 구성하여 특정 성능 요구 사항에 최적화하는 작업을 포함합니다.

이 항목에서는 Azure에서 프로비전 중 스토리지 SQL Server VM 및 기존 VM에 대한 스토리지를 구성하는 방법을 설명합니다. 이 구성은 SQL Server를 실행하는 Azure VM의 [성능 모범 사례](virtual-machines-windows-sql-performance.md) 에 기반합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>사전 요구 사항

자동화된 스토리지 구성 설정을 사용하려면 가상 머신에는 다음과 같은 특성이 필요합니다.

* [SQL Server 갤러리 이미지](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)로 프로비전합니다.
* [Resource Manager 배포 모델](../../../azure-resource-manager/management/deployment-models.md)을 사용합니다.
* [프리미엄 SSD](../disks-types.md)를 사용합니다.

## <a name="new-vms"></a>새 VM

다음 섹션에서는 새 SQL Server 가상 머신에 대한 스토리지를 구성하는 방법을 설명합니다.

### <a name="azure-portal"></a>Azure portal

SQL Server 갤러리 이미지를 사용하여 Azure VM을 프로비전할 때 **SQL Server 설정** 탭에서 구성 **변경을** 선택하여 성능 최적화된 저장소 구성 페이지를 엽니다. 값을 기본값으로 두거나 워크로드에 따라 필요에 가장 적합한 디스크 구성 유형을 수정할 수 있습니다. 

![프로비전하는 동안 SQL Server VM 스토리지 구성](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

**스토리지 최적화에서**SQL Server를 배포할 워크로드 유형을 선택합니다. **일반** 최적화 옵션을 사용하면 기본적으로 최대 IOPS가 5000개인 하나의 데이터 디스크가 있으며 데이터, 트랜잭션 로그 및 TempDB 저장소에 이 드라이브를 사용합니다. **OLTP(트랜잭션 처리)** 또는 **데이터 웨어하우징을** 선택하면 데이터에 대한 별도의 디스크, 트랜잭션 로그에 대한 별도의 디스크가 생성되고 TempDB에 로컬 SSD를 사용합니다. **트랜잭션 처리와** **데이터 웨어하우징**간에는 저장소 차이가 없지만 [스트라이프 구성및 추적 플래그를](#workload-optimization-settings)변경합니다. 프리미엄 저장소를 선택하면 데이터 드라이브에 대해 *ReadOnly로* 캐싱이 설정되고 [SQL Server VM 성능 모범 사례에](virtual-machines-windows-sql-performance.md)따라 로그 드라이브에 대한 *없음이* 설정됩니다. 

![프로비전하는 동안 SQL Server VM 스토리지 구성](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

디스크 구성은 완전히 사용자 지정할 수 있으므로 SQL Server VM 워크로드에 필요한 스토리지 토폴로지, 디스크 유형 및 IoP를 구성할 수 있습니다. 또한 SQL Server VM이 지원되는 지역(미국 동부 2, 동남 아시아 및 북유럽) 중 하나에 있고 [구독에 울트라 디스크를](/azure/virtual-machines/windows/disks-enable-ultra-ssd)사용하도록 설정한 경우 UltraSSD(미리 보기)를 **디스크 유형에** 대한 옵션으로 사용할 수 있습니다.  

또한 디스크에 대한 캐싱을 설정할 수 있습니다. Azure VM에는 [프리미엄 디스크와](/azure/virtual-machines/windows/disks-types#premium-ssd)함께 사용할 때 [Blob Cache라는](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) 다중 계층 캐싱 기술이 있습니다. Blob 캐시는 캐싱을 위해 가상 시스템 RAM과 로컬 SSD의 조합을 사용합니다. 

프리미엄 SSD에 대한 디스크 캐싱은 *ReadOnly,* *ReadWrite* 또는 없음일 수 *있습니다.* 

- *ReadOnly* 캐싱은 프리미엄 저장소에 저장된 SQL Server 데이터 파일에 매우 유용합니다. *ReadOnly* 캐싱은 낮은 읽기 대기 시간, 높은 읽기 IOPS 및 처리량을 VM 메모리 및 로컬 SSD 내에 있는 캐시에서 수행됩니다. 이러한 읽기는 Azure Blob 저장소의 데이터 디스크에서 읽는 것보다 훨씬 빠릅니다. 프리미엄 저장소는 캐시에서 제공된 읽기를 디스크 IOPS 및 처리량으로 계산하지 않습니다. 따라서 적용 가능한 총 IOPS 및 처리량을 더 높게 달성할 수 있습니다. 
- 로그 파일이 순차적으로 작성되고 *ReadOnly* 캐싱의 이점을 얻지 않으므로 SQL Server Log 파일을 호스팅하는 디스크에는 *캐시* 구성을 사용할 수 없습니다. 
- *ReadWrite* 캐싱은 SQL Server가 *ReadWrite* 캐시와의 데이터 일관성을 지원하지 않으므로 SQL Server 파일을 호스트하는 데 사용해서는 안 됩니다. *ReadOnly* Blob 캐시의 낭비 용량을 쓰고 쓰기가 *ReadOnly* Blob 캐시 계층을 통과하는 경우 대기 가 약간 증가합니다. 


   > [!TIP]
   > 저장소 구성이 선택한 VM 크기에 의해 부과된 제한 사항과 일치하는지 확인합니다. VM 크기의 성능 한도를 초과하는 저장소 매개 변수를 `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`선택하면 오류가 발생합니다. 디스크 유형을 변경하여 IoP를 줄이거나 VM 크기를 늘려 성능 제한을 증가시면 됩니다. 


선택을 기반으로 Azure는 VM을 만든 후에 다음과 같은 스토리지 구성 작업을 수행합니다.

* 프리미엄 SSD를 만들고 가상 머신에 연결합니다.
* SQL Server에 액세스할 수 있도록 데이터 디스크를 구성합니다.
* 지정된 크기와 성능(IOPS 및 처리량) 요구 사항에 따라 스토리지 풀에 데이터 디스크를 구성합니다.
* 가상 컴퓨터에 새 드라이브와 스토리지 풀을 연결합니다.
* 지정한 워크로드 유형(데이터 웨어하우징, 트랜잭션 처리 또는 일반)에 따라 새 드라이브를 최적화합니다.

Azure에서 스토리지 설정을 구성하는 방법에 대한 자세한 내용은 [스토리지 구성 섹션](#storage-configuration)을 참조하세요. Azure 포털에서 SQL Server VM을 만드는 방법에 대한 전체 연습은 [프로비저닝 자습서를](virtual-machines-windows-portal-sql-server-provision.md)참조하십시오.

### <a name="resource-manage-templates"></a>Resource Manager 템플릿

다음 Resource Manager 템플릿을 사용하는 경우 두 개의 프리미엄 데이터 디스크는 스토리지 풀 구성 없이 기본적으로 연결됩니다. 그러나 이러한 템플릿을 사용자 지정하여 가상 머신에 연결된 프리미엄 데이터 디스크의 수를 변경할 수 있습니다.

* [자동화된 Backup을 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [자동화된 패치를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [AKV 통합을 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>빠른 시작 템플릿

다음 빠른 시작 템플릿을 사용하여 저장소 최적화를 사용하여 SQL Server VM을 배포할 수 있습니다. 

* [스토리지 최적화를 통해 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [울트라SSD를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>기존 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

기존 SQL Server VM의 경우 Azure 포털에서 일부 스토리지 설정을 수정할 수 있습니다. SQL [가상 컴퓨터 리소스를](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)열고 **개요를**선택합니다. SQL Server 개요 페이지에는 VM의 현재 저장소 사용량이 표시됩니다. VM에 있는 모든 드라이브는 이 차트에 표시됩니다. 각 드라이브의 경우 스토리지 공간은 네 개의 섹션에 표시됩니다.

* SQL data
* SQL 로그
* 기타(비 SQL 스토리지)
* 사용 가능

저장소 설정을 수정하려면 **설정에서** **구성을** 선택합니다. 

![기존 SQL Server VM에 대한 스토리지 구성](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

SQL Server VM 생성 프로세스 중에 구성된 드라이브의 디스크 설정을 수정할 수 있습니다. **드라이브 확장을** 선택하면 드라이브 수정 페이지가 열리므로 디스크 유형을 변경하고 디스크를 추가할 수 있습니다. 

![기존 SQL Server VM에 대한 스토리지 구성](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>스토리지 구성

이 섹션에서는 Azure 포털에서 SQL VM 프로비저닝 또는 구성 중에 Azure가 자동으로 수행하는 저장소 구성 변경 사항에 대한 참조를 제공합니다.

* Azure는 VM에서 선택한 저장소에서 저장소 풀을 구성합니다. 이 항목의 다음 섹션에서는 저장소 풀 구성에 대한 세부 정보를 제공합니다.
* 자동 스토리지 구성은 항상 [프리미엄 SSD](../disks-types.md) P30 데이터 디스크를 사용합니다. 따라서 선택한 테라바이트 수와 VM에 연결된 데이터 디스크의 수 간에 1:1 매핑이 됩니다.

가격 책정 정보는 [디스크 스토리지](https://azure.microsoft.com/pricing/details/storage) 탭의 **스토리지 가격 책정** 페이지를 참조하세요.

### <a name="creation-of-the-storage-pool"></a>스토리지 풀 만들기

Azure는 다음 설정을 사용하여 SQL Server VM에 스토리지 풀을 만듭니다.

| 설정 | 값 |
| --- | --- |
| 스트라이프 크기 |256KB(데이터 웨어하우징); 64KB(트랜잭션) |
| 디스크 크기 |각각 1TB |
| 캐시 |읽기 |
| 할당 크기 |64KB NTFS 할당 단위 크기 |
| 복구 | 단순 복구(복원력 없음) |
| 열 수 |최대 8개의 데이터 디스크 수<sup>1개</sup> |


<sup>1</sup> 스토리지 풀을 만든 후에 스토리지 풀에서 열 수를 변경할 수 없습니다.


## <a name="workload-optimization-settings"></a>워크로드 최적화 설정

다음 테이블에서는 사용할 수 있는 세 가지 워크로드 유형 옵션 및 이에 해당하는 최적화를 설명합니다.

| 워크로드 유형 | Description | 최적화 |
| --- | --- | --- |
| **일반** |대부분의 워크로드를 지원하는 기본 설정 |None |
| **트랜잭션 처리** |기존의 데이터베이스 OLTP 워크로드용으로 스토리지를 최적화합니다. |추적 플래그 1117<br/>추적 플래그 1118 |
| **데이터 웨어하우징** |분석 및 보고 워크로드용으로 스토리지를 최적화합니다. |추적 플래그 610<br/>추적 플래그 1117 |

> [!NOTE]
> SQL 가상 컴퓨터를 프로비전할 때 스토리지 구성 단계에서 워크로드 유형을 선택하여 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure VM에서의 SQL Server 실행에 관한 다른 항목은 [Azure Virtual Machines의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.
