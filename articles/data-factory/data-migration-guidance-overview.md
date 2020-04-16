---
title: 데이터 레이크 및 데이터 웨어하우스에서 Azure로 데이터 마이그레이션
description: Azure 데이터 팩터리를 사용하여 데이터 레이크 및 데이터 웨어하우스에서 Azure로 데이터를 마이그레이션합니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4408546c892299e5bbbc22b00a4b334c36eda616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416441"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure 데이터 팩터리를 사용하여 데이터 레이크 또는 데이터 웨어하우스에서 Azure로 데이터를 마이그레이션합니다.

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

데이터 레이크 또는 엔터프라이즈 데이터 웨어하우스(EDW)를 Microsoft Azure로 마이그레이션하려면 Azure 데이터 팩터리를 사용하는 것이 좋습니다. Azure 데이터 팩터리는 다음 시나리오에 적합합니다.

- 아마존 단순 스토리지 서비스(Amazon S3) 또는 온-프레미스 하두롭 분산 파일 시스템(HDFS)에서 Azure로의 빅 데이터 워크로드 마이그레이션
- 오라클 엑사데이타, 네테자, 테라데이터 또는 아마존 레드시프트에서 Azure로의 EDW 마이그레이션

Azure Data Factory는 데이터 레이크 마이그레이션을 위해 페타바이트(PB) 데이터 및 데이터 웨어하우스 마이그레이션을 위한 수십 테라바이트(TB)의 데이터를 이동할 수 있습니다.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Azure 데이터 팩터리를 데이터 마이그레이션에 사용할 수 있는 이유

- Azure Data Factory는 처리 능력의 양을 쉽게 확장하여 고성능, 복원력 및 확장성을 통해 서버없는 방식으로 데이터를 이동할 수 있습니다. 그리고 당신은 당신이 사용하는 것에 대해서만 지불합니다. 다음 사항도 유의해야 합니다. 
  - Azure 데이터 팩터리는 데이터 볼륨 또는 파일 수에 제한이 없습니다.
  - Azure Data Factory는 네트워크 및 저장소 대역폭을 완전히 사용하여 사용자 환경에서 가장 많은 양의 데이터 이동 처리량을 달성할 수 있습니다.
  - Azure Data Factory는 종량제 메서드를 사용하므로 실제로 Azure로 데이터 마이그레이션을 실행하는 데 사용하는 시간에 대해서만 비용을 지불합니다.  
- Azure Data Factory는 일회성 기록 로드와 예약된 증분 로드를 모두 수행할 수 있습니다.
- Azure Data Factory는 IR(Azure 통합 런타임)을 사용하여 공개적으로 액세스할 수 있는 데이터 레이크와 웨어하우스 끝점 간에 데이터를 이동합니다. 또한 자체 호스팅 IR을 사용하여 Azure 가상 네트워크(VNet) 내부 또는 방화벽 뒤의 데이터 레이크 및 웨어하우스 끝점에 대한 데이터를 이동할 수 있습니다.
- Azure Data Factory에는 엔터프라이즈급 보안이 있습니다: 보안 서비스 간 통합을 위해 Windows 설치 관리자(MSI) 또는 서비스 ID를 사용하거나 자격 증명 관리를 위해 Azure Key Vault를 사용할 수 있습니다.
- Azure Data Factory는 코드 없는 작성 환경과 풍부한 기본 제공 모니터링 대시보드를 제공합니다.  

## <a name="online-vs-offline-data-migration"></a>온라인 과 오프라인 데이터 마이그레이션

Azure Data Factory는 네트워크(인터넷, ER 또는 VPN)를 통해 데이터를 전송하는 표준 온라인 데이터 마이그레이션 도구입니다. 오프라인 데이터 마이그레이션을 통해 사용자는 조직에서 Azure 데이터 센터로 데이터 전송 장치를 물리적으로 전송합니다.  

온라인 및 오프라인 마이그레이션 방법 중에서 선택할 때 세 가지 주요 고려 사항이 있습니다.  

- 마이그레이션할 데이터 크기
- 네트워크 대역폭
- 마이그레이션 창

예를 들어 Azure Data Factory를 사용하여 2주 이내에 데이터 마이그레이션을 완료할 계획이라고 가정합니다(마이그레이션 *기간).* 다음 표의 분홍색/파란색 커트 라인을 확인합니다. 지정된 열에 대해 가장 낮은 분홍색 셀은 마이그레이션 창이 가장 가깝지만 2주 미만인 데이터 크기/네트워크 대역폭 페어링을 보여 주어집니다. 파란색 셀의 모든 크기/대역폭 페어링에는 2주 이상의 온라인 마이그레이션 기간이 있습니다. 

![온라인 과](media/data-migration-guidance-overview/online-offline.png) 오프라인 이 표는 데이터 크기와 사용 가능한 네트워크 대역폭에 따라 온라인 마이그레이션(Azure Data Factory)을 통해 의도한 마이그레이션 창을 충족할 수 있는지 여부를 결정하는 데 도움이 됩니다. 온라인 마이그레이션 기간이 2주 이상인 경우 오프라인 마이그레이션을 사용해야 합니다.

> [!NOTE]
> 온라인 마이그레이션을 사용하면 단일 도구를 통해 기록 데이터 로드 및 증분 피드를 모두 달성할 수 있습니다.  이 방법을 통해 전체 마이그레이션 기간 동안 기존 저장소와 새 저장소 간에 데이터를 동기화된 정보로 유지될 수 있습니다. 즉, 새로 고쳐진 데이터로 새 저장소에서 ETL 논리를 다시 빌드할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [AWS S3에서 Azure로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
- [온-프레미스 hadoop 클러스터에서 Azure로 데이터 마이그레이션](data-migration-guidance-hdfs-azure-storage.md)
- [온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션](data-migration-guidance-netezza-azure-sqldw.md)
