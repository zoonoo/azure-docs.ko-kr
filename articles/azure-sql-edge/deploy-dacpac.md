---
title: SQL Database DACPAC 및 BACPAC 패키지 사용 - Azure SQL Edge
description: Azure SQL Edge에서 dacpac 및 bacpac을 사용하는 방법 알아보기
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392251"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Edge에서 SQL Database DACPAC 및 BACPAC 패키지

Azure SQL Edge는 IoT 및 에지 배포를 위해 최적화된 관계형 데이터베이스 엔진입니다. 업계 최고 수준의 성능, 보안 및 쿼리 처리 기능을 제공하는 Microsoft SQL Database 엔진의 최신 버전을 기반으로 합니다. Azure SQL Edge는 SQL Server의 업계 최고의 관계형 데이터베이스 관리 기능과 함께 실시간 분석 및 복잡한 이벤트 처리를 위한 기본 제공 스트리밍 기능을 제공합니다.

Azure SQL Edge는 SQL Edge 배포 도중 또는 이후에 [SQL DATABASE DACPAC 및 BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) 패키지를 배포할 수 있도록 하는 기본 메커니즘을 제공합니다.

`MSSQL_PACKAGE` 환경 변수를 사용하여 SQL Edge에 SQL Database dacpac 및 bacpac 패키지를 배포할 수 있습니다. 환경 변수는 다음 중 하나를 사용하여 구성할 수 있습니다.  
- dacpac 및 bacpac 파일을 포함하는 SQL 컨테이너 내의 로컬 폴더 위치. 이 폴더는 탑재 지점이나 데이터 볼륨 컨테이너를 사용하여 호스트 볼륨에 매핑할 수 있습니다. 
- dacpac 또는 bacpac 파일에 대한 SQL 컨테이너 매핑 내의 로컬 파일 경로. 이 파일 경로는 탑재 지점이나 데이터 볼륨 컨테이너를 사용하여 호스트 볼륨에 매핑될 수 있습니다. 
- dacpac 또는 bacpac 파일이 포함된 zip 파일에 대한 SQL 컨테이너 매핑 내의 로컬 파일 경로. 이 파일 경로는 탑재 지점이나 데이터 볼륨 컨테이너를 사용하여 호스트 볼륨에 매핑될 수 있습니다. 
- dacpac 및 bacpac 파일이 포함된 zip 파일에 대한 Azure Blob SAS URL
- dacpac 또는 bacpac 파일에 대한 Azure Blob SAS URL 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Edge에서 SQL Database DAC 패키지 사용

Azure Blob Storage 및 zip 파일을 사용하여 SQL Database DAC 패키지 `(*.dacpac)` 또는 BACPAC 파일 `(*.bacpac)`를 배포하려면(또는 가져오려면) 아래 단계를 수행합니다. 

1. 아래에 설명된 메커니즘을 사용하여 DAC 패키지를 생성/추출하거나 Bacpac 파일을 내보냅니다. 
    - SQL Database DAC 패키지를 만들거나 추출합니다. 기존 SQL Server 데이터베이스에 대한 DAC 패키지를 생성하는 방법에 대한 자세한 내용은 [데이터베이스에서 DAC 추출](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)을 참조하세요.
    - 배포된 DAC 패키지 또는 데이터베이스 내보내기. 기존 SQL Server 데이터베이스용 bacpac 파일을 생성하는 방법에 대한 자세한 내용은 [데이터 계층 애플리케이션 내보내기](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/)를 참조하세요.

2. `*.dacpac` 또는 `*.bacpac` 파일을 zip으로 압축하여 Azure Blob Storage 계정에 업로드합니다. Azure Blob 스토리지에 파일을 업로드하는 데 관한 자세한 내용은 [Azure Portal을 사용하여 Blob 업로드, 다운로드 및 나열](../storage/blobs/storage-quickstart-blobs-portal.md)을 참조하세요.

3. Azure Portal을 사용하여 zip 파일에 대한 공유 액세스 서명을 생성합니다. 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 액세스 위임](../storage/common/storage-sas-overview.md)을 참조하세요.

4. DAC 패키지에 대한 공유 액세스 URI를 포함하도록 SQL Edge 모듈 구성을 업데이트합니다. SQL Edge 모듈을 업데이트하려면 다음 단계를 수행합니다.

    1. Azure Portal에서 IoT Hub 배포로 이동합니다.

    2. 왼쪽 창에서 **IoT Edge** 를 선택합니다.

    3. **IoT Edge** 페이지에서 SQL Edge 모듈이 배포된 IoT Edge을 찾아 선택합니다.

    4. **IoT Edge 디바이스** 페이지에서 **모듈 설정** 을 선택합니다.

    5. **모듈 설정** 페이지에서 Azure SQL Edge 모듈을 클릭합니다.

    6. **IoT Edge 모듈 업데이트** 창에서 **환경 변수** 를 선택합니다. `MSSQL_PACKAGE` 환경 변수를 추가하고 위의 3단계에서 생성된 SAS URL을 환경 변수 값으로 지정합니다. 

    7. **업데이트** 를 선택합니다.

    8. **모듈 설정** 페이지에서 **검토 + 만들기** 를 선택합니다.

    9. **모듈 설정** 페이지에서 **만들기** 를 선택합니다.

5. 모듈 업데이트 후에는 패키지 파일을 다운로드하고 압축을 해제한 후, SQL Edge 인스턴스에 대해 배포합니다.

Azure SQL Edge 컨테이너를 다시 시작할 때마다 SQL Edge는 압축된 파일 패키지를 다운로드하고 변경 내용을 확인하려고 합니다. 새 버전의 dacpac 파일이 발견되면 변경 내용이 SQL Edge의 데이터베이스에 배포됩니다.

## <a name="known-issue"></a>알려진 문제

일부 DACPAC 또는 BACPAC 배포 중에 명령 시간 초과가 발생하여 dacpac 배포 작업이 실패할 수 있습니다. 이 문제가 발생하는 경우 SQLPackage.exe(또는 SQL 클라이언트 도구)를 사용하여 DACPAC 또는 BACPAC를 수동으로 적용하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 SQL Edge 배포](deploy-portal.md)
- [스트림 데이터](stream-data.md)
- [SQL Edge에서 ONNX를 통한 기계 학습 및 AI](onnx-overview.md)