---
title: StorSimple 8000 시리즈 장치의 데이터 마이그레이션 옵션
description: StorSimple 8000 계열에서 데이터를 마이그레이션하는 옵션에 대한 개요를 제공합니다.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: f712486c8035bbc913fbd229759f3415a1005449
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438321"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>StorSimple 8000 시리즈에서 데이터를 마이그레이션하는 옵션

> [!IMPORTANT]
> 2022년 12월 31일에 StorSimple 8000 시리즈는 지원(EOS) 상태가 종료됩니다. StorSimple 8000 시리즈 고객은 문서에 설명된 대안 중 하나로 마이그레이션하는 것이 좋습니다.

StorSimple 8000 시리즈는 2022년 12월에 [지원이 종료됩니다.](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) StorSimple 8000 시리즈를 실행하는 고객은 다른 Azure 자사 하이브리드 서비스로 업그레이드할 수 있습니다. 이 문서에서는 데이터를 마이그레이션하는 데 사용할 수 있는 Azure 하이브리드 옵션에 대해 설명합니다.

## <a name="migration-options"></a>마이그레이션 옵션

StorSimple 8000 시리즈를 사용하는 고객은 Azure 또는 타사 옵션을 사용할 수 있습니다.

### <a name="azure-options"></a>Azure 옵션

#### <a name="migrate-to-azure-file-sync"></a>Azure 파일 동기화로 마이그레이션

이 새로운 마이그레이션 옵션을 사용하면 고객이 조직의 파일 공유를 Azure 파일에 저장할 수 있습니다. 그러면 이러한 파일 공유에서 AFS(Azure 파일 동기화)를 사용하여 온-프레미스 액세스를 위해 중앙 집중화됩니다. AFS는 Windows Server 호스트에 배포할 수 있습니다. 그러면 실제 데이터 마이그레이션이 호스트 복사본으로 수행되거나 마이그레이션 도구를 사용하여 수행됩니다.

Azure 파일 동기화로 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [StorSimple 8100 및 8600 을 Azure File Sync로 이동합니다.](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)

<!-- 03/25/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
### Third-party options

#### Migrate to Panzura Freedom NAS

StorSimple 5000-7000 customers can choose to migrate to Panzura Freedom NAS to keep their data in Azure. Panzura Freedom solution provides a NAS solution that spans datacenters, offices, public and private clouds. The solution enables local, hybrid, and in-cloud data workflows for NFS, SMB, and mobile clients. 

This migration is supported by Panzura and customers can get started by requesting migration support from the [Panzura website](https://panzura.com/storsimple-migration/).

#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>마이그레이션 - 질문과 대답

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>17. StorSimple 8000 시리즈 장치는 언제 서비스 종료에 도달합니까?

A. StorSimple 8000 시리즈는 2022년 12월에 [지원이 종료됩니다.](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) 지원이 끝나면 Microsoft는 2022년 12월 이후에 이러한 장치의 하드웨어와 소프트웨어모두에 대한 지원을 더 이상 제공할 수 없게 됩니다. 이제는 디바이스에서 데이터를 마이그레이션하도록 계획하는 것이 좋습니다.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>17. Azure에 저장된 데이터는 어떻게 되나요?  

A. 새 서비스로 마이그레이션한 후에도 Azure에서 데이터를 계속 사용할 수 있습니다.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>17. StorSimple 디바이스에 로컬로 저장된 데이터는 어떻게 되나요?

A. 마이그레이션 문서에서 설명한 대로 로컬 디바이스에 있는 데이터는 새 서비스에 복사할 수 있습니다.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>17. StorSimple 8000 시리즈 어플라이언스를 보관하려면 어떻게 되나요?

A. 서비스는 계속 작동하지만 Microsoft는 더 이상 하드웨어 및 소프트웨어 지원을 제공하지 않습니다. 비즈니스 연속성을 위해 마이그레이션을 사용하는 것이 좋습니다.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>17. StorSimple 8000 시리즈 장치에서 데이터를 마이그레이션하는 데 사용할 수 있는 옵션은 무엇입니까?

A. 시나리오에 따라 StorSimple 8000 시리즈 사용자는 다음과 같은 마이그레이션 옵션을 가지고 있습니다.

* **Azure 파일 동기화로 마이그레이션**: Azure 원시 형식으로 전환하려면 이 옵션을 사용합니다. Azure 파일 동기화는 파일 공유를 중앙 집중식으로 관리하는 데 사용할 수 있습니다.

* **기타 옵션:** Microsoft 지원에 문의하여 여기에 나열되지 않은 마이그레이션 옵션에 대해 논의할 수 있습니다.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>17. 다른 스토리지 솔루션으로 마이그레이션할 수 있나요?

A. 예. 데이터의 호스트 복사본을 사용하여 다른 스토리지 솔루션으로 마이그레이션할 수 있습니다.

### <a name="q-is-migration-supported-by-microsoft"></a>17. Microsoft에서 마이그레이션을 지원하나요?

A. 8000 시리즈에서 마이그레이션하는 것은 완전히 지원되는 작업입니다. 실제로는 먼저 고객 지원팀에 문의한 후에 마이그레이션을 시작하는 것이 좋습니다. 마이그레이션은 현재 지원형 작업입니다. StorSimple 8000 시리즈 장치에서 데이터를 마이그레이션하려는 경우 [StorSimple 지원에 문의하십시오.](mailto:storsimp@microsoft.com)

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>17. Azure 파일 동기화로 마이그레이션하기 위한 가격 책정 모델은 무엇입니까?

A. Azure 파일 동기화를 사용하는 경우 서비스에 대한 구독 요금이 적용될 수 있습니다. 고객은 또한 지속적인 스토리지 비용을 지불해야 합니다. 견적은 [AFS 가격을]( https://azure.microsoft.com/pricing/details/storage/files/) 참조하십시오.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>17. 마이그레이션을 완료하는 데 얼마나 오래 걸리나요?

A. 데이터 마이그레이션을 완료하는 데 걸리는 시간은 선택한 데이터의 양과 선택한 업그레이드 옵션에 따라 다릅니다.

## <a name="next-steps"></a>다음 단계

* [StorSimple 8000 시리즈에서 Azure 파일 동기화로 데이터 마이그레이션](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
