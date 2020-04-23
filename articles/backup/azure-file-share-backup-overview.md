---
title: Azure 파일 공유 백업 정보
description: Recovery Services 자격 증명 모음에서 Azure 파일 공유를 백업 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: bea5035ff50eaf4047f56cde3bd671d81e21f5d3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101429"
---
# <a name="about-azure-file-share-backup"></a>Azure 파일 공유 백업 정보

Azure 파일 공유 백업은 클라우드에서 데이터를 보호 하 고 온-프레미스 백업 솔루션과 관련 된 추가 유지 관리 오버 헤드를 제거 하는 클라우드 기반 기본 백업 솔루션입니다. Azure Backup 서비스는 Azure file sync와 원활 하 게 통합 되며 백업 뿐만 아니라 파일 공유 데이터를 중앙 집중화할 수 있습니다. 이 간단 하 고 안정적 이며 안전한 솔루션을 사용 하면 재해 시나리오의 경우 데이터를 복구할 수 있는 몇 가지 간단한 단계를 통해 엔터프라이즈 파일 공유에 대 한 보호를 구성할 수 있습니다.

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 파일 공유 백업의 주요 이점

* 제로 인프라: 파일 공유에 대 한 보호를 구성 하는 데는 배포가 필요 하지 않습니다.
* 사용자 지정 된 보존: 요구 사항에 따라 일별/주별/월별/매년 보존을 사용 하 여 백업을 구성할 수 있습니다.
* 기본 제공 관리 기능: 백업을 예약 하 고 데이터 정리의 추가 오버 헤드 없이 원하는 보존 기간을 지정할 수 있습니다.
* 즉시 복원: Azure 파일 공유 백업은 파일 공유 스냅숏을 사용 하므로 즉시 복원 하려는 파일만 선택할 수 있습니다.
* 경고 및 보고: 백업 및 복원 오류에 대 한 경고를 구성 하 고, Azure Backup에서 제공 하는 보고 솔루션을 사용 하 여 파일 공유에서 백업에 대 한 정보를 얻을 수 있습니다.

## <a name="architecture"></a>Architecture

![Azure 파일 공유 백업 아키텍처](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>백업 프로세스의 작동 방식

1. Azure 파일 공유에 대 한 백업을 구성 하는 첫 번째 단계는 recovery services 자격 증명 모음을 만드는 것입니다. 자격 증명 모음은 서로 다른 작업을 통해 구성 된 백업에 대 한 통합 보기를 제공 합니다.

2. 자격 증명 모음을 만들면 Azure Backup 서비스에서 자격 증명 모음에 등록할 수 있는 저장소 계정을 검색 합니다. 보호 하려는 파일 공유를 호스트 하는 저장소 계정을 선택할 수 있습니다.

3. 저장소 계정을 선택 하면 Azure Backup 서비스는 저장소 계정에 있는 파일 공유 집합을 나열 하 고 해당 이름을 관리 계층 카탈로그에 저장 합니다.

4. 그런 다음 요구 사항에 따라 백업 정책 (일정 및 보존)을 구성 하 고 백업할 파일 공유를 선택 합니다. Azure Backup 서비스는 예약 된 백업을 수행 하는 일정을 제어 평면에 등록 합니다.

5. 지정 된 정책에 따라 Azure Backup 스케줄러는 예약 된 시간에 백업을 트리거합니다. 이 작업의 일부로 파일 공유 스냅숏은 파일 공유 API를 사용 하 여 생성 됩니다. 스냅숏 URL만 메타 데이터 저장소에 저장 됩니다.

    >[!NOTE]
    >백업 서비스는 저장소 계정에 포함 되는 스냅숏을 생성 하 고 관리 하며, 백업이 자격 증명 모음에 전송 되지 않으므로 파일 공유 데이터는 백업 서비스로 전송 되지 않습니다.

6. 원본 파일 공유에서 사용할 수 있는 스냅숏에서 Azure 파일 공유 콘텐츠 (개별 파일 또는 전체 공유)를 복원할 수 있습니다. 작업이 트리거되면 스냅숏 URL은 메타 데이터 저장소에서 검색 되 고 데이터는 원본 스냅숏에서 선택한 대상 파일 공유로 나열 되 고 전송 됩니다.

7. 백업 및 복원 작업 모니터링 데이터는 Azure Backup 모니터링 서비스로 푸시됩니다. 이렇게 하면 단일 대시보드에서 파일 공유에 대 한 클라우드 백업을 모니터링할 수 있습니다. 또한 백업 상태가 영향을 받는 경우 경고 또는 전자 메일 알림을 구성할 수도 있습니다. 전자 메일은 Azure 전자 메일 서비스를 통해 전송 됩니다.

## <a name="backup-costs"></a>백업 비용

Azure 파일 공유 백업은 스냅숏 기반 솔루션 이며, 스냅숏에 대해 발생 하는 저장소 요금은 [여기](https://azure.microsoft.com/pricing/details/storage/files/)에 언급 된 가격 책정 정보에 따라 Azure Files 사용량과 함께 청구 됩니다.

그러나 백업 솔루션 활용에 대 한 보호 된 인스턴스 요금은 [Azure Files 백업](https://azure.microsoft.com/pricing/details/backup/) 섹션에 설명 된 가격 책정 모델에 따라 결정 됩니다. 현재 미국 서 부에 대 한 실제 가격은 업데이트 되었습니다. 다른 지역의 경우 정확한 가격은 일부 지역 변형으로 곧 업데이트 되지만 동일한 가격 책정 모델을 사용 합니다.

>[!NOTE]
>이제 "보호 된 인스턴스 요금"이 없으며 [여기](https://azure.microsoft.com/pricing/details/storage/files/)에 언급 된 가격 책정에 따라 스냅숏에 대해서만 요금이 청구 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure 파일 공유를 백업](backup-afs.md) 하는 방법 알아보기
* [백업에 대 한 질문에 대 한](backup-azure-files-faq.md) 답변 찾기 Azure Files
