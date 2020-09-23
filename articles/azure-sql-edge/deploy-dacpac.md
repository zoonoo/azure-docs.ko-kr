---
title: SQL Database DACPAC 및 BACPAC 패키지 사용-Azure SQL Edge
description: Azure SQL Edge에서 dacpac 및 bacpac 사용에 대 한 자세한 정보
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 6c8be6e67b1d7b919d6ea221c473c8975e559658
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887481"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Edge에서 DACPAC 및 BACPAC 패키지 SQL Database

Azure SQL Edge는 IoT 및 에지 배포를 위해 최적화된 관계형 데이터베이스 엔진입니다. 업계 최고의 성능, 보안 및 쿼리 처리 기능을 제공 하는 최신 버전의 Microsoft SQL Database 엔진을 기반으로 합니다. Azure SQL Edge는 SQL Server의 업계 최고의 관계형 데이터베이스 관리 기능과 함께 실시간 분석 및 복잡한 이벤트 처리를 위한 기본 제공 스트리밍 기능을 제공합니다.

또한 Azure SQL Edge는 SQL Edge 배포 중에 [SQL DATABASE DACPAC 및 BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 패키지를 배포할 수 있도록 하는 SqlPackage.exe의 기본 구현을 제공 합니다. 

환경 변수를 사용 하 여 SQL Edge에 SQL Database dacpac 및 bacpac 패키지를 배포할 수 있습니다 `MSSQL_PACKAGE` . 환경 변수는 다음 중 하나를 사용 하 여 구성할 수 있습니다.  
- Dacpac 및 bacpac 파일을 포함 하는 SQL 컨테이너 내의 로컬 폴더 위치입니다. 이 폴더는 탑재 지점이 나 데이터 볼륨 컨테이너를 사용 하 여 호스트 볼륨에 매핑할 수 있습니다. 
- Dacpac 또는 bacpac 파일에 대 한 SQL 컨테이너 매핑 내의 로컬 파일 경로입니다. 이 파일 경로는 탑재 지점이 나 데이터 볼륨 컨테이너를 사용 하 여 호스트 볼륨에 매핑할 수 있습니다. 
- Dacpac 또는 bacpac 파일이 포함 된 zip 파일에 대 한 SQL 컨테이너 매핑 내의 로컬 파일 경로입니다. 이 파일 경로는 탑재 지점이 나 데이터 볼륨 컨테이너를 사용 하 여 호스트 볼륨에 매핑할 수 있습니다. 
- Dacpac 및 bacpac 파일이 포함 된 zip 파일에 대 한 Azure Blob SAS URL입니다.
- Dacpac 또는 bacpac 파일에 대 한 Azure Blob SAS URL입니다. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Edge에서 SQL Database DAC 패키지 사용

`(*.dacpac)` `(*.bacpac)` Azure Blob storage 및 zip 파일을 사용 하 여 SQL Database DAC 패키지 또는 BACPAC 파일을 배포 (또는 가져오기) 하려면 아래 단계를 수행 합니다. 

1. 아래에 설명 된 메커니즘을 사용 하 여 DAC 패키지를 만들거나 추출 하거나 Bacpac 파일을 내보냅니다. 
    - SQL Database DAC 패키지를 만들거나 추출합니다. 기존 SQL Server 데이터베이스에 대한 DAC 패키지를 생성하는 방법에 대한 자세한 내용은 [데이터베이스에서 DAC 추출](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)을 참조하세요.
    - 배포 된 DAC 패키지 또는 데이터베이스 내보내기 기존 SQL Server 데이터베이스용 bacpac 파일을 생성 하는 방법에 대 한 자세한 내용은 [데이터 계층 응용 프로그램 내보내기](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) 를 참조 하세요.

2. 또는 파일을 압축 하 여 `*.dacpac` `*.bacpac` Azure Blob storage 계정에 업로드 합니다. Azure Blob 스토리지에 파일을 업로드하는 데 관한 자세한 내용은 [Azure Portal을 사용하여 Blob 업로드, 다운로드 및 나열](../storage/blobs/storage-quickstart-blobs-portal.md)을 참조하세요.

3. Azure Portal을 사용하여 zip 파일에 대한 공유 액세스 서명을 생성합니다. 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 액세스 위임](../storage/common/storage-sas-overview.md)을 참조하세요.

4. DAC 패키지에 대한 공유 액세스 URI를 포함하도록 SQL Edge 모듈 구성을 업데이트합니다. SQL Edge 모듈을 업데이트하려면 다음 단계를 수행합니다.

    1. Azure Portal에서 IoT Hub 배포로 이동합니다.

    2. 왼쪽 창에서 **IoT Edge**를 선택합니다.

    3. **IoT Edge** 페이지에서 SQL Edge 모듈이 배포된 IoT Edge을 찾아 선택합니다.

    4. **IoT Edge 디바이스** 페이지에서 **모듈 설정**을 선택합니다.

    5. **모듈 설정** 페이지에서 Azure SQL Edge 모듈을 클릭 합니다.

    6. **업데이트 IoT Edge 모듈** 창에서 **환경 변수**를 선택 합니다. `MSSQL_PACKAGE`환경 변수를 추가 하 고 위의 3 단계에서 생성 된 SAS URL을 환경 변수에 대 한 값으로 지정 합니다. 

    7. **업데이트**를 선택합니다.

    8. **모듈 설정** 페이지에서 **검토 + 만들기**를 선택 합니다.

    9. **모듈 설정** 페이지에서 **만들기**를 선택 합니다.

5. 모듈을 업데이트 한 후 패키지 파일이 다운로드 되 고, 압축을 푼 다음 SQL Edge 인스턴스에 대해 배포 됩니다.

Azure SQL Edge 컨테이너를 다시 시작할 때마다 SQL Edge는 압축 된 파일 패키지를 다운로드 하 고 변경 내용을 확인 하려고 시도 합니다. 새 버전의 dacpac 파일이 발견되면 변경 내용이 SQL Edge의 데이터베이스에 배포됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 SQL Edge 배포](deploy-portal.md)
- [스트림 데이터](stream-data.md)
- [SQL Edge에서 ONNX를 통한 기계 학습 및 AI](onnx-overview.md)
