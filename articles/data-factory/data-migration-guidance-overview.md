---
title: Azure Data Factory를 사용 하 여 data lake 및 데이터 웨어하우스의 데이터를 Azure로 마이그레이션합니다. Microsoft Docs
description: Azure Data Factory를 사용 하 여 data lake 및 데이터 웨어하우스의 데이터를 Azure로 마이그레이션합니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 937a076b3e0e3c5170779d3449776f0aa1cf5b49
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258994"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure Data Factory를 사용 하 여 data lake 또는 데이터 웨어하우스의 데이터를 Azure로 마이그레이션 

Data lake 또는 EDW (엔터프라이즈 데이터 웨어하우스)를 Azure로 마이그레이션하려는 경우 데이터 마이그레이션을 수행 하는 도구를 Azure Data Factory 수 있습니다. Data lake migration 및 데이터 웨어하우스 마이그레이션은 다음과 같은 시나리오와 관련이 있습니다. 

- AWS S3, 온-프레미스 Hadoop 파일 시스템에서 Azure로의 빅 데이터 워크 로드 마이그레이션 
- Oracle Exadata, Netezza, Teradata, AWS Redshift에서 Azure로의 EDW 마이그레이션. 

Data lake migration의 경우 데이터의 PBs를 이동 하 고 데이터 웨어하우스 마이그레이션에는 수십, TB의 데이터를 이동할 수 Azure Data Factory. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>데이터 마이그레이션에 Azure Data Factory를 사용할 수 있는 이유 

- Azure Data Factory는 고성능, 복원 력 및 확장성을 사용 하 여 서버를 사용 하지 않는 방식으로 데이터를 이동 하 고 사용 하는 만큼만 요금을 지불 하는 속도를 쉽게 확장할 수 있습니다.  
  - Azure Data Factory에는 데이터 볼륨과 파일 수에 대 한 제한이 없습니다.
  - 100%의 네트워크 및 저장소 대역폭을 활용 하 여 사용자 환경에서 가장 높은 데이터 이동 처리량을 달성할 수 Azure Data Factory.   
  - Azure Data Factory는 종 량 제 전략을 따라 Azure Data Factory를 사용 하 여 Azure로 데이터를 마이그레이션하는 시간에 대해서만 비용을 지불 하면 됩니다.  
- Azure Data Factory에는 예약 된 증분 로드 뿐만 아니라 일회성 기록 로드를 수행할 수 있는 기능이 있습니다. 
- Azure Data Factory는 Azure IR를 사용 하 여 공개적으로 액세스할 수 있는 data lake/웨어하우스 끝점 간에 데이터를 이동 하거나, VNet 내부 또는 방화벽 뒤에 있는 data lake/warehouse 끝점의 데이터를 이동 하기 위해 자체 호스팅 IR을 사용 합니다. 
- 엔터프라이즈급 보안 Azure Data Factory: 보안 서비스 간 통합을 위해 MSI 또는 서비스 Id를 사용 하거나, 자격 증명 관리를 위해 Azure Key Vault를 활용 합니다. 
- Azure Data Factory은 코드 없는 제작 환경과 풍부한 기본 제공 모니터링 대시보드를 제공 합니다.  

## <a name="online-vs-offline-data-migration"></a>온라인 및 오프 라인 데이터 마이그레이션

Azure Data Factory는 네트워크 (인터넷, ER 또는 VPN)를 통해 데이터를 전송 하는 일반적인 온라인 데이터 마이그레이션 도구입니다 .이 도구를 통해 오프 라인 데이터 마이그레이션을 통해 사용자는 조직에서 Azure 데이터 센터에 데이터 전송 장치를 실제로 제공할 수 있습니다.  

온라인 및 오프 라인 마이그레이션 방법을 선택할 때 세 가지 주요 고려 사항이 있습니다.  

- 마이그레이션할 데이터의 크기입니다. 
- 네트워크 대역폭. 
- 마이그레이션 창.   

2 주 (마이그레이션 창) 내에서 데이터 마이그레이션을 완료 하려면 아래 그림의 줄을 참조 하 여 다른 데이터 크기와 네트워크 대역폭으로 온라인 마이그레이션 도구 (Azure Data Factory)를 사용 하는 것이 적절 한 시기를 표시 합니다.   

![온라인 및 오프 라인](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> 온라인 마이그레이션 방법의 혜택은 기록 데이터 로드와 증분 피드를 모두 한 도구에서 종료 하는 것입니다.  이렇게 하면 데이터를 새로 고친 데이터를 사용 하 여 새 저장소에서 ETL 논리를 다시 작성할 수 있도록 전체 마이그레이션 기간 동안 기존 저장소와 새 저장소 간에 데이터를 동기화 된 상태로 유지할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

- [AWS S3에서 Azure로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
- [온-프레미스 hadoop 클러스터에서 Azure로 데이터 마이그레이션](data-migration-guidance-hdfs-azure-storage.md)
- [온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션](data-migration-guidance-netezza-azure-sqldw.md)
