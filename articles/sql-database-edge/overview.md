---
title: Azure SQL 데이터베이스 에지는 무엇입니까? | Microsoft Docs
description: Azure SQL 데이터베이스 에지에 대해 알아보기
keywords: SQL 데이터베이스 에지 소개, SQL 데이터베이스 가장자리, SQL 데이터베이스 가장자리 개요
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246707"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Azure SQL 데이터베이스 에지 미리 보기란 무엇입니까?

Azure SQL Database 에지 미리 보기는 IoT 및 IoT 에지 배포를 위해 준비된 최적화된 관계형 데이터베이스 엔진입니다. IoT 애플리케이션 및 솔루션을 위한 고성능 데이터 스토리지 및 처리 계층을 만들 수 있는 기능을 제공합니다. Azure SQL Database Edge는 JSON, 그래프 및 시간계 데이터와 같은 관계형 및 비관계형 데이터를 스트리밍, 처리 및 분석하는 기능을 제공하므로 다양한 최신 IoT 응용 프로그램에 적합한 선택입니다.

Azure SQL Database Edge는 업계 최고의 성능, 보안 및 쿼리 처리 기능을 제공하는 최신 버전의 [Microsoft SQL Server 데이터베이스 엔진을](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)기반으로 합니다. Azure SQL Database Edge는 SQL Server 및 Azure SQL Database와 동일한 엔진에 구축되므로 응용 프로그램 또는 솔루션 의 개발을 더 쉽고 빠르게 만드는 동일한 T-SQL 프로그래밍 표면 영역을 제공하며 동시에 응용 프로그램을 만듭니다. IoT Edge 장치, 데이터 센터 및 클라우드 간의 이식성을 제공합니다.

## <a name="deployment-models"></a>배포 모델

Azure SQL Database Edge는 Azure 마켓플레이스에서 사용할 수 있으며 [Azure IoT Edge에](../iot-edge/about-iot-edge.md)대한 모듈로 배포할 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 에지 배포](deploy-portal.md)를 참조하십시오.<br>

![SQL 데이터베이스 에지 개요 다이어그램](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL 데이터베이스 에지 에디션

SQL Database Edge는 세 가지 버전 또는 소프트웨어 계획으로 제공됩니다. 이 버전은 동일한 기능 집합을 가지고 있으며 사용 권한 과 지원하는 CPU / 메모리의 양에 따라 다릅니다.

   |**계획**  |**설명**  |
   |---------|---------|
   |Azure SQL 데이터베이스 에지 개발자  |  개발 만 sku, 각 SQL 데이터베이스 가장자리 컨테이너는 최대 4 코어와 32 GB 메모리로 제한됩니다  |
   |Azure SQL Database Edge    |  프로덕션 sku, 각 SQL 데이터베이스 가장자리 컨테이너는 최대 8 코어 및 64 GB 메모리로 제한됩니다. |

## <a name="pricing-and-availability"></a>가격 및 가용성

Azure SQL 데이터베이스는 현재 미리 보기 상태입니다. 가격 및 가용성에 대한 자세한 내용은 [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)를 참조하십시오.

> [!IMPORTANT]
> Azure SQL Database Edge와 SQL Server 간의 기능 차이와 다른 Azure SQL Database Edge 옵션 간의 차이점을 이해하려면 [SQL Database Edge 데이터베이스 기능을](https://azure.microsoft.com/services/sql-database-edge/)참조하십시오.

## <a name="streaming-capabilities"></a>스트리밍 기능  

Azure SQL Database Edge는 실시간 분석 및 복잡한 이벤트 처리를 위한 기본 스트리밍 기능을 제공합니다. 스트리밍 기능은 [Azure Stream Analytics와](../stream-analytics/stream-analytics-introduction.md) 동일한 구문으로 빌드되며 [IoT Edge에서 Azure Stream Analytics와](../stream-analytics/stream-analytics-edge.md)유사한 기능을 제공합니다.

Azure SQL Database Edge의 스트리밍 엔진은 대기 시간, 복원력, 대역폭 및 규정 준수의 효율적인 사용을 위해 설계되었습니다.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>머신 러닝 및 인공 지능 기능

Azure SQL Database Edge는 개방형 형식 ONNX(개방형 신경망 교환) 런타임을 통합하여 기본 제공 기계 학습 및 분석 기능을 제공하므로 서로 다른 프레임워크 간에 딥 러닝 및 신경망 모델을 교환할 수 있습니다. ONNX에 대한 자세한 내용은 [여기를](https://onnx.ai/)참조하십시오. ONNX 런타임은 선택한 언어 또는 도구로 모델을 개발할 수 있는 유연성을 제공하며, 이 모델을 SQL Database Edge 내에서 실행하기 위해 ONNX 형식으로 변환할 수 있습니다. 자세한 내용은 [SQL 데이터베이스 가장자리에서 ONNX를 사용하여 기계 학습 및 인공 지능을](onnx-overview.md)참조하십시오.

## <a name="working-with-azure-sql-database-edge"></a>Azure SQL 데이터베이스 에지 작업

Azure SQL Database Edge를 사용하면 응용 프로그램을 더 쉽고 생산적입니다. 사용자는 모든 익숙한 도구와 기술을 사용하여 IoT Edge 요구 사항에 맞는 훌륭한 앱과 솔루션을 구축할 수 있습니다. 사용자는 다음과 같은 도구를 사용하여 SQL Database Edge에서 개발할 수 있습니다.

- [Azure 포털](https://portal.azure.com/) - 모든 Azure 서비스를 관리하기 위한 웹 기반 응용 프로그램입니다.
- [SQL Server 관리 스튜디오](/sql/ssms/download-sql-server-management-studio-ssms/) - SQL Server에서 SQL 데이터베이스에 이르기까지 모든 SQL 인프라를 관리하기 위한 다운로드 가능한 무료 클라이언트 응용 프로그램입니다.
- [Visual Studio의 SQL Server 데이터 도구](/sql/ssdt/download-sql-server-data-tools-ssdt/) - SQL Server 관계형 데이터베이스, SQL 데이터베이스, 통합 서비스 패키지, 분석 서비스 데이터 모델 및 보고 서비스 보고서를 개발하기 위한 무료 다운로드 가능한 클라이언트 응용 프로그램입니다.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Windows, MacOS 및 Linux의 Microsoft 온-프레미스 및 클라우드 데이터 플랫폼 제품군을 사용하는 데이터 전문가를 위한 무료 다운로드 가능한 크로스 플랫폼 데이터베이스 도구입니다.
- [비주얼 스튜디오 코드](https://code.visualstudio.com/docs) - 윈도우, 맥 OS 및 리눅스에 대한 무료, 다운로드, 오픈 소스 코드 편집기. Microsoft SQL Server, Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스를 쿼리하기 위한 [mssql 확장을](https://aka.ms/mssql-marketplace) 비롯한 확장을 지원합니다.


## <a name="next-steps"></a>다음 단계

- 가격 및 가용성 관련 세부 정보는 [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)를 참조하십시오.
- 구독에 대해 Azure SQL 데이터베이스 가장자리를 사용하도록 요청합니다.
- 시작하려면 다음을 참조하십시오.
  - [Azure 포털을 통해 SQL 데이터베이스 에지 배포](deploy-portal.md)
  - [SQL 데이터베이스 에지의 머신 러닝 및 인공 지능](onnx-overview.md)
