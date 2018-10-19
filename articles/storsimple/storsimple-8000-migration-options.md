---
title: StorSimple 5000-7000 시리즈에서 데이터를 마이그레이션하는 옵션 평가 | Microsoft Docs
description: StorSimple 5000-7000 시리즈에서 데이터를 마이그레이션하는 옵션에 대해 간략히 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2018
ms.author: alkohli
ms.openlocfilehash: 5da67b5141eb61823d3e376b6f0e6b0b3895ca68
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498286"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>StorSimple 5000-7000 시리즈에서 데이터를 마이그레이션하는 옵션 

> [!IMPORTANT]
> 2019년 7월 31일부로, StorSimple 5000/7000 시리즈는 지원 종료(EOS) 상태에 도달합니다. StorSimple 5000/7000 시리즈 고객은 이 문서에 설명된 대안 제품 중 하나로 마이그레이션하는 것이 좋습니다.

StorSimple 5000-7000 시리즈에 대한 지원은 2019년 7월에 [중단](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)될 예정입니다. StorSimple 5000-7000 시리즈를 실행하고 있는 고객에게는 다른 Azure 자체 하이브리드 서비스로 업그레이드할 수 있는 옵션이 있습니다. 이 문서에서는 데이터를 마이그레이션하는 데 사용할 수 있는 Azure 하이브리드 옵션에 대해 설명합니다. 

## <a name="migration-options"></a>마이그레이션 옵션

StorSimple 5000-7000 시리즈를 사용하는 고객에게는 다음 두 가지 주요 옵션이 있습니다.

- **StorSimple 8000 시리즈로 업그레이드** – StorSimple 8000 시리즈로 업그레이드하여 StorSimple 플랫폼에서 계속 사용합니다.  이 업그레이드 경로를 사용하려면 고객이 5000-7000 시리즈 장치를 8000 시리즈로 교체해야 합니다. 데이터는 마이그레이션 도구를 사용하여 5000-7000 시리즈 장치에서 마이그레이션됩니다. 마이그레이션이 성공적으로 완료되면 StorSimple 8000 시리즈 장치에서 데이터를 Azure Blob Storage에 계속 계층화합니다. 

    StorSimple 8000 시리즈를 사용하여 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [StorSimple 5000-7000 시리즈에서 8000 시리즈 장치로 데이터 마이그레이션](storsimple-8000-migrate-from-5000-7000.md)을 참조하세요.

- **Azure 파일 동기화로 마이그레이션** - 이 새 마이그레이션 옵션을 사용하면 고객이 조직의 파일 공유를 Azure Files에 저장할 수 있습니다. 그러면 이러한 파일 공유에서 AFS(Azure 파일 동기화)를 사용하여 온-프레미스 액세스를 위해 중앙 집중화됩니다. AFS는 Windows Server 호스트에 배포할 수 있습니다. 그러면 실제 데이터 마이그레이션이 호스트 복사본으로 수행되거나 마이그레이션 도구를 사용하여 수행됩니다.

    데이터를 Azure 파일 동기화로 마이그레이션하는 방법에 대한 자세한 내용은 [StorSimple 5000-7000 시리즈에서 Azure 파일 동기화로 데이터 마이그레이션](https://aka.ms/StorSimpleMigrationAFS)을 참조하세요.

## <a name="migration---frequently-asked-questions"></a>마이그레이션 - 질문과 대답

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Q. StorSimple 5000 및 7000 시리즈 장치에 대한 서비스는 언제 종료되나요? 

a. StorSimple 5000-7000 시리즈에 대한 서비스는 2019년 7월에 [종료](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)될 예정입니다. 2019년 7월 이후 서비스가 종료되면 Microsoft는 더 이상 이러한 장치의 하드웨어 및 소프트웨어 모두에 대한 지원을 제공하지 않습니다. 이제는 장치에서 데이터를 마이그레이션하도록 계획하는 것이 좋습니다.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Azure에 저장된 데이터는 어떻게 되나요?  

a. 새 서비스로 마이그레이션한 후에도 Azure에서 데이터를 계속 사용할 수 있습니다. 


### <a name="q--what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q.  StorSimple 장치에 로컬로 저장된 데이터는 어떻게 되나요? 

a. 마이그레이션 문서에서 설명한 대로 로컬 장치에 있는 데이터는 새 서비스에 복사할 수 있습니다.

### <a name="what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>StorSimple 5000/7000 시리즈 어플라이언스를 유지하려면 어떻게 되나요? 

a. 서비스는 계속 작동하지만 Microsoft는 더 이상 하드웨어 및 소프트웨어 지원을 제공하지 않습니다. 비즈니스 연속성을 위해 마이그레이션을 사용하는 것이 좋습니다.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Q. StorSimple 5000-7000 시리즈 장치에서 데이터를 마이그레이션하는 데 사용할 수 있는 옵션은 무엇인가요? 

a. StorSimple 5000-7000 시리즈 사용자는 시나리오에 따라 다음과 같은 마이그레이션 옵션을 사용할 수 있습니다. 

 - **8000 시리즈로 업그레이드**: StorSimple 플랫폼에서 계속하려면 이 옵션을 사용합니다. 
 - **Azure 파일 동기화로 마이그레이션**: Azure 원시 형식으로 전환하려면 이 옵션을 사용합니다. Azure 파일 동기화는 파일 공유를 중앙 집중식으로 관리하는 데 사용할 수 있습니다. 

Microsoft 지원에 문의하여 여기서 설명하지 않은 마이그레이션 옵션에 대해 논의할 수 있습니다.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. 다른 저장소 솔루션으로 마이그레이션할 수 있나요?

a. 예. 데이터의 호스트 복사본을 사용하여 다른 저장소 솔루션으로 마이그레이션할 수 있습니다.

### <a name="q-is-migration-supported-by-microsoft"></a>Q. Microsoft에서 마이그레이션을 지원하나요? 

a. 5000 또는 7000 시리즈에서 마이그레이션하는 작업은 완벽하게 지원됩니다. 실제로는 먼저 고객 지원팀에 문의한 후에 마이그레이션을 시작하는 것이 좋습니다. 마이그레이션은 현재 지원형 작업입니다. StorSimple 5000-7000 시리즈 장치에서 데이터를 마이그레이션하려면 [지원 티켓을 여세요](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>Q. 두 가지 마이그레이션 옵션 모두에 대한 가격 책정 모델은 무엇인가요?

a. 마이그레이션 비용은 선택한 옵션에 따라 다릅니다. 마이그레이션 자체에는 추가 비용이 들지 않지만, StorSimple 8000 시리즈로 업그레이드하려는 경우 하드웨어 장치 비용이 발생하게 됩니다. 

마찬가지로 Azure 파일 동기화를 사용하는 경우 서비스 독 요금이 적용될 수 있습니다. 고객은 각각의 경우에서 진행 중인 저장소 비용을 지불해야 합니다. 예상 비용은 다음을 참조하세요. 
- [StorSimple 가격 책정](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS 가격 책정]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Q.  마이그레이션을 완료하는 데 얼마나 오래 걸리나요?

a. 데이터 마이그레이션을 완료하는 데 걸리는 시간은 선택한 데이터의 양과 선택한 업그레이드 옵션에 따라 다릅니다. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>Q. StorSimple 8000 시리즈의 지원 종료 날짜는 언제인가요?

a. StorSimple 8000 시리즈의 지원 종료 날짜는 [여기](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)에 게시됩니다.


## <a name="next-steps"></a>다음 단계
 - [StorSimple 5000-7000 시리즈에서 8000 시리즈 장치로 데이터 마이그레이션](storsimple-8000-migrate-from-5000-7000.md)
 - [StorSimple 5000-7000 시리즈에서 Azure 파일 동기화로 데이터 마이그레이션](storsimple-5000-7000-afs-migration.md)
