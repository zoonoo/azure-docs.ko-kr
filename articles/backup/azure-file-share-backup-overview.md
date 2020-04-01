---
title: Azure 파일 공유 백업 소개
description: 복구 서비스 자격 증명 모음에서 Azure 파일 공유를 백업하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396273"
---
# <a name="about-azure-file-share-backup"></a>Azure 파일 공유 백업 소개

Azure 파일 공유 백업은 클라우드에서 데이터를 보호하고 온-프레미스 백업 솔루션과 관련된 추가 유지 관리 오버헤드를 제거하는 네이티브 클라우드 기반 백업 솔루션입니다. Azure Backup 서비스는 Azure 파일 동기화와 원활하게 통합되며 파일 공유 데이터와 백업을 중앙 집중화할 수 있습니다. 이 간단하고 안정적이며 안전한 솔루션을 사용하면 재해 시나리오가 발생했을 때 데이터를 복구할 수 있다는 확신을 가지고 몇 가지 간단한 단계로 엔터프라이즈 파일 공유에 대한 보호를 구성할 수 있습니다.

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 파일 공유 백업의 주요 이점

* 인프라 없음: 파일 공유에 대한 보호를 구성하기 위해 배포가 필요하지 않습니다.
* 기본 제공 관리 기능: 데이터 프런싱의 추가 오버헤드 없이 백업을 예약하고 원하는 보존 기간을 지정할 수 있습니다.
* 즉시 복원: Azure 파일 공유 백업은 파일 공유 스냅샷을 사용하므로 즉시 복원할 파일만 선택할 수 있습니다.
* 경고 및 보고: 백업 및 복원 오류에 대한 경고를 구성하고 Azure Backup에서 제공하는 보고 솔루션을 사용하여 파일 공유 에서 백업에 대한 통찰력을 얻을 수 있습니다.

## <a name="architecture"></a>Architecture

![Azure 파일 공유 백업 아키텍처](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>백업 프로세스의 작동 방식

1. Azure File 공유에 대한 백업을 구성하는 첫 번째 단계는 복구 서비스 자격 증명 모음을 만드는 것입니다. 볼트는 다양한 워크로드에 걸쳐 구성된 백업에 대한 통합보기를 제공합니다.

2. 볼트를 만들면 Azure Backup 서비스는 자격 증명 모음에 등록할 수 있는 저장소 계정을 검색합니다. 보호하려는 파일 공유를 호스팅하는 저장소 계정을 선택할 수 있습니다.

3. 저장소 계정을 선택하면 Azure Backup 서비스가 저장소 계정에 있는 파일 공유 집합을 나열하고 해당 이름을 관리 계층 카탈로그에 저장합니다.

4. 그런 다음 요구 사항에 따라 백업 정책(일정 및 보존)을 구성하고 백업할 파일 공유를 선택합니다. Azure Backup 서비스는 예약된 백업을 수행할 제어 평면에 일정을 등록합니다.

5. 지정된 정책에 따라 Azure Backup 스케줄러는 예약된 시간에 백업을 트리거합니다. 해당 작업의 일부로 파일 공유 스냅숏은 파일 공유 API를 사용하여 만들어집니다. 스냅샷 URL만 메타데이터 저장소에 저장됩니다.

    >[!NOTE]
    >백업 서비스는 저장소 계정의 일부인 스냅숏을 만들고 관리하기 때문에 파일 공유 데이터는 백업 서비스로 전송되지 않습니다.

6. 원본 파일 공유에서 사용할 수 있는 스냅숏에서 Azure 파일 공유 내용(개별 파일 또는 전체 공유)을 복원할 수 있습니다. 작업이 트리거되면 스냅숏 URL이 메타데이터 저장소에서 검색되고 데이터가 나열되고 원본 스냅숏에서 선택한 대상 파일 공유로 전송됩니다.

7. 백업 및 복원 작업 모니터링 데이터는 Azure 백업 모니터링 서비스로 푸시됩니다. 이렇게 하면 단일 대시보드에서 파일 공유에 대한 클라우드 백업을 모니터링할 수 있습니다. 또한 백업 상태의 영향을 받는 경우 경고 또는 전자 메일 알림을 구성할 수도 있습니다. 전자 메일은 Azure 전자 메일 서비스를 통해 전송됩니다.

## <a name="backup-costs"></a>백업 비용

Azure File 공유 백업은 스냅숏 기반 솔루션이며 스냅숏에 대해 발생하는 저장소 요금은 [여기에](https://azure.microsoft.com/pricing/details/storage/files/)언급된 가격 세부 정보에 따라 Azure 파일 사용량과 함께 청구됩니다.

그러나 백업 솔루션을 활용하기 위한 보호된 인스턴스 요금은 [Azure 파일 백업](https://azure.microsoft.com/pricing/details/backup/) 섹션에 설명된 가격 책정 모델에 따라 다다. 현재 실제 가격은 미국 중서부에 대해서만 업데이트되었습니다. 다른 지역의 경우 일부 지역별 가격으로 곧 정확한 가격이 업데이트되지만 동일한 가격 책정 모델을 사용할 수 있습니다.

>[!NOTE]
>미리 보기 중에는 "보호된 인스턴스 요금"이 없으며 [여기에](https://azure.microsoft.com/pricing/details/storage/files/)언급된 가격에 따라 스냅샷에 대해서만 요금이 부과됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure 파일 공유 백업](backup-afs.md) 하는 방법에 대해 알아봅니다.
* [Azure 파일 백업에 대한 질문에 대한](backup-azure-files-faq.md) 답변 찾기
