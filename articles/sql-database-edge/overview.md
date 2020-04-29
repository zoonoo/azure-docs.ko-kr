---
title: Azure SQL Database Edge 란? | Microsoft Docs
description: Azure SQL Database Edge에 대 한 자세한 정보
keywords: sql database edge 소개, sql database edge 정의, sql database edge 개요
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246707"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Edge Preview Azure SQL Database 이란?

Azure SQL Database Edge 미리 보기는 IoT 및 IoT Edge 배포를 위해 설계 된 최적화 된 관계형 데이터베이스 엔진입니다. IoT 응용 프로그램 및 솔루션에 대 한 고성능 데이터 저장소 및 처리 계층을 만드는 기능을 제공 합니다. Azure SQL Database Edge는 다양 한 최신 IoT 응용 프로그램에 적합 한 선택이 되도록 JSON, 그래프 및 시계열 데이터와 같은 관계형 및 비관계형 데이터를 스트림, 처리 및 분석 하는 기능을 제공 합니다.

Azure SQL Database Edge는 업계 최고의 성능, 보안 및 쿼리 처리 기능을 제공 하는 최신 버전의 [Microsoft SQL Server 데이터베이스 엔진](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)를 기반으로 합니다. Azure SQL Database Edge는 SQL Server 및 Azure SQL Database와 동일한 엔진에서 빌드되기 때문에 응용 프로그램 또는 솔루션 개발을 더 쉽고 빠르게 수행할 수 있도록 하는 동일한 T-sql 프로그래밍 노출 영역을 제공 하 고, 동시에 IoT Edge 장치, 데이터 센터 및 클라우드 간에 응용 프로그램 이식성을 향상 합니다.

## <a name="deployment-models"></a>배포 모델

Azure SQL Database Edge는 Azure Marketplace에서 사용할 수 있으며 [Azure IoT Edge](../iot-edge/about-iot-edge.md)의 모듈로 배포할 수 있습니다. 자세한 내용은 [Azure SQL Database Edge 배포](deploy-portal.md)를 참조 하세요.<br>

![SQL Database Edge 개요 다이어그램](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL Database Edge 버전

SQL Database Edge는 세 가지 버전 또는 소프트웨어 계획과 함께 사용할 수 있습니다. 이러한 버전은 동일한 기능 집합을 가지 며 사용 권한 및 지 원하는 cpu/메모리의 양에 따라 다릅니다.

   |**계획**  |**설명**  |
   |---------|---------|
   |Azure SQL Database Edge Developer  |  개발 전용 sku, 각 SQL Database Edge 컨테이너는 최대 4 개의 코어 및 32 GB 메모리로 제한 됩니다.  |
   |Azure SQL Database Edge    |  프로덕션 sku, 각 SQL Database Edge 컨테이너는 최대 8 개의 코어 및 64 GB 메모리로 제한 됩니다. |

## <a name="pricing-and-availability"></a>가격 책정 및 가용성

Azure SQL Database 현재 미리 보기 상태입니다. 가격 책정 및 가용성에 대 한 자세한 내용은 [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)를 참조 하세요.

> [!IMPORTANT]
> Azure SQL Database Edge와 SQL Server 간의 기능 차이점을 이해 하 고 서로 다른 Azure SQL Database 가장자리 옵션 간의 차이점을 이해 하려면 [Edge 데이터베이스 기능 SQL Database](https://azure.microsoft.com/services/sql-database-edge/)를 참조 하세요.

## <a name="streaming-capabilities"></a>스트리밍 기능  

Azure SQL Database Edge는 실시간 분석 및 복잡 한 이벤트 처리를 위한 기본 제공 스트리밍 기능을 제공 합니다. 스트리밍 기능은 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 와 동일한 구문을 사용 하 여 작성 되며 [IoT Edge의 Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md)와 유사한 기능을 제공 합니다.

Azure SQL Database Edge에 대 한 스트리밍 엔진은 대기 시간이 짧고 복원 력이 있으며 대역폭 및 규정 준수를 효율적으로 사용할 수 있도록 설계 되었습니다.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning 및 인공 지능 기능

Azure SQL Database Edge는 개방형 형식 ONNX (오픈 신경망 교환) 런타임을 통합 하 여 기본 제공 기계 학습 및 분석 기능을 제공 하 여 다양 한 프레임 워크 간에 심층 학습 및 신경망 모델을 교환할 수 있도록 합니다. ONNX에 대 한 자세한 내용은 [여기](https://onnx.ai/)를 참조 하세요. ONNX runtime은 사용자가 선택한 언어 또는 도구를 통해 모델을 개발할 수 있는 유연성을 제공 하며,이를 통해 SQL Database Edge 내에서 실행 하기 위한 ONNX 형식으로 변환할 수 있습니다. 자세한 내용은 [SQL Database Edge에서 ONNX를 사용 하는 Machine Learning 및 인공 지능](onnx-overview.md)을 참조 하세요.

## <a name="working-with-azure-sql-database-edge"></a>Azure SQL Database Edge 작업

Azure SQL Database Edge를 사용 하면 응용 프로그램을 더 쉽고 생산적으로 개발 하 고 유지 관리할 수 있습니다. 사용자는 친숙 한 모든 도구와 기술을 사용 하 여 IoT Edge 요구 사항에 적합 한 앱 및 솔루션을 빌드할 수 있습니다. 사용자는 다음과 같은 도구를 사용 하 여 SQL Database Edge에서 개발할 수 있습니다.

- [Azure Portal](https://portal.azure.com/) -모든 Azure 서비스를 관리 하기 위한 웹 기반 응용 프로그램입니다.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) -SQL Server에서 SQL Database까지 모든 SQL 인프라를 관리 하기 위한 다운로드 가능한 무료 클라이언트 응용 프로그램입니다.
- [Visual Studio에서 SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt/) -SQL Server 관계형 데이터베이스, SQL 데이터베이스, Integration Services 패키지, Analysis Services 데이터 모델 및 Reporting Services 보고서를 개발 하기 위한 다운로드 가능한 무료 클라이언트 응용 프로그램입니다.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) -Windows, Macos 및 Linux에서 Microsoft 온-프레미스 및 클라우드 데이터 플랫폼 제품군을 사용 하는 데이터 전문가를 위한 무료로 다운로드 가능한 플랫폼 간 데이터베이스 도구입니다.
- [Visual Studio Code](https://code.visualstudio.com/docs) -Windows, Macos 및 Linux 용 다운로드 가능한 무료 오픈 소스 코드 편집기입니다. Microsoft SQL Server, Azure SQL Database 및 Azure SQL Data Warehouse 쿼리를 위한 [mssql 확장](https://aka.ms/mssql-marketplace) 을 비롯 한 확장을 지원 합니다.


## <a name="next-steps"></a>다음 단계

- 가격 책정 및 가용성 관련 세부 정보는 [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)를 참조 하세요.
- 구독에 대 한 Azure SQL Database Edge 사용을 요청 합니다.
- 시작 하려면 다음을 참조 하세요.
  - [Azure Portal를 통해 SQL Database에 지 배포](deploy-portal.md)
  - [SQL Database Edge를 사용 하 여 Machine Learning 및 인공 지능](onnx-overview.md)
