---
title: Azure 파일 공유 백업
description: Recovery Services 자격 증명 모음에서 Azure 파일 공유를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c4f9dd816ace2c9aec8f48207fbce88acf34e24a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516818"
---
# <a name="about-azure-file-share-backup"></a>Azure 파일 공유 백업

Azure 파일 공유 백업은 클라우드의 데이터를 보호하고 온-프레미스 백업 솔루션과 관련된 추가 유지 관리 오버 헤드를 제거하는 기본 클라우드 기반 백업 솔루션입니다. Azure Backup 서비스는 Azure 파일 동기화와 원활하게 통합되며 백업뿐만 아니라 파일 공유 데이터를 중앙 집중화 할 수 있습니다. 이 간단하고 안정적이며 안전한 솔루션을 사용하면 모든 재해 시나리오에서 데이터를 복구할 수 있는 몇 가지 간단한 단계를 통해 엔터프라이즈 파일 공유에 대한 보호를 구성할 수 있습니다.

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 파일 공유 백업의 주요 이점

* **제로 인프라**: 파일 공유에 대한 보호를 구성하기 위해 배포할 필요가 없습니다.
* **사용자 지정 보존**: 요구 사항에 따라 일별/주별/월별/연간 보존으로 백업을 구성할 수 있습니다.
* **기본 제공 관리 기능**: 데이터 정리의 추가 오버 헤드 없이 백업을 예약하고 원하는 보존 기간을 지정할 수 있습니다.
* **즉시 복원**: Azure 파일 공유 백업은 파일 공유 스냅샷을 사용하므로 즉시 복원하려는 파일만 선택할 수 있습니다.
* **경고 및 보고**: 백업 및 복원 실패에 대한 경고를 구성하고 Azure Backup에서 제공하는 보고 솔루션을 사용하여 파일 공유에서 백업에 대한 인사이트를 얻을 수 있습니다.
* **실수로 인한 파일 공유 삭제 방지**: Azure Backup은 14일의 보존 기간으로 스토리지 계정 수준에서 [일시 삭제 기능](../storage/files/storage-files-prevent-file-share-deletion.md)을 사용하도록 설정합니다. 악의적인 작업자가 파일 공유를 삭제하더라도 파일 공유의 내용과 복구 지점(스냅샷)은 구성 가능한 보존 기간 동안 유지되므로 데이터 손실없이 원본 콘텐츠 및 스냅샷을 성공적으로 완벽하게 복구할 수 있습니다.

## <a name="architecture"></a>Architecture

![Azure 파일 공유 백업 아키텍처](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>백업 프로세스의 작동 방식

1. Azure 파일 공유에 대한 백업을 구성하는 첫 번째 단계는 Recovery Services 자격 증명 모음을 만드는 것입니다. 자격 증명 모음은 서로 다른 워크로드를 통해 구성된 백업에 대한 통합 보기를 제공합니다.

2. 자격 증명 모음을 만들면 Azure Backup 서비스는 자격 증명 모음에 등록할 수 있는 스토리지 계정을 검색합니다. 보호하려는 파일 공유를 호스팅하는 스토리지 계정을 선택할 수 있습니다.

3. 스토리지 계정을 선택하면 Azure Backup 서비스는 스토리지 계정에 있는 파일 공유 집합을 나열하고 해당 이름을 관리 레이어 카탈로그에 저장합니다.

4. 그런 다음 요구 사항에 따라 백업 정책(일정 및 보존)을 구성하고 백업할 파일 공유를 선택합니다. Azure Backup 서비스는 예약된 백업을 수행하기 위해 제어 평면에 일정을 등록합니다.

5. 지정된 정책에 따라 Azure Backup 스케줄러는 예약된 시간에 백업을 트리거합니다. 해당 작업의 일부로 파일 공유 API를 사용하여 파일 공유 스냅샷이 생성됩니다. 스냅샷 URL만 메타데이터 저장소에 저장됩니다.

    >[!NOTE]
    >백업 서비스가 스토리지 계정의 일부인 스냅샷을 생성하고 관리하며 백업이 자격 증명 모음으로 전송되지 않기 때문에 파일 공유 데이터가 백업 서비스로 전송되지 않습니다.

6. 원본 파일 공유에서 사용할 수 있는 스냅샷에서 Azure 파일 공유 콘텐츠(개별 파일 또는 전체 공유)를 복원할 수 있습니다. 작업이 트리거되면 메타데이터 저장소에서 스냅샷 URL이 검색되고 데이터가 나열되며 원본 스냅샷에서 선택한 대상 파일 공유로 전송됩니다.

7. Azure 파일 동기화를 사용하는 경우 백업 서비스는 복원중인 파일 경로를 Azure 파일 동기화 서비스에 표시하고 해당 파일에 대한 백그라운드 변경 감지 프로세스를 트리거합니다. 변경된 모든 파일은 서버 엔드포인트로 동기화됩니다. 이 프로세스는 Azure 파일 공유에 대한 원래 복원과 동시에 발생합니다.

8. 백업 및 복원 작업 모니터링 데이터는 Azure Backup Monitoring 서비스로 푸시됩니다. 이렇게 하면 단일 대시보드에서 파일 공유에 대한 클라우드 백업을 모니터링할 수 있습니다. 또한 백업 상태가 영향을 받는 경우 경고 또는 이메일 알림을 구성할 수도 있습니다. 이메일은 Azure 이메일 서비스를 통해 전송됩니다.

## <a name="backup-costs"></a>백업 비용

Azure 파일 공유 백업 솔루션과 관련된 비용에는 두 가지가 있습니다:

1. **스냅샷 스토리지 비용**: 스냅샷에 대해 발생하는 스토리지 요금은 [여기](https://azure.microsoft.com/pricing/details/storage/files/)에 언급된 가격 세부 정보에 따라 Azure Files 사용량과 함께 청구됩니다.

2. **보호된 인스턴스 요금**: 2020년 9월 1일부터 [여기](https://azure.microsoft.com/pricing/details/backup/)에 언급된 가격 세부 정보에 따라 고객에게 보호된 인스턴스 요금이 부과됩니다. 보호된 인스턴스 요금은 스토리지 계정에 있는 보호된 파일 공유의 총 크기에 따라 다릅니다.

Azure 파일 공유 백업에 대한 자세한 추정치를 얻으려면 자세한 [Azure Backup 가격 책정 예측 도구](https://aka.ms/AzureBackupCostEstimates)를 다운로드할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* [Azure 파일 공유 백업](backup-afs.md) 방법 알아보기
* [Azure Files 백업에 대한 질문](backup-azure-files-faq.yml)의 답변 찾기
