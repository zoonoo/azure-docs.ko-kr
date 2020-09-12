---
title: SQL Database DACPAC 및 BACPAC 패키지 사용-Azure SQL Edge (미리 보기)
description: Azure SQL Edge (미리 보기)에서 dacpac 및 bacpac를 사용 하는 방법에 대해 알아봅니다.
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 52c8e9586d8ee53cdaac28cb1c48d2927d82c2ed
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462762"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Edge에서 DACPAC 및 BACPAC 패키지 SQL Database

Azure SQL Edge(미리 보기)는 IoT 및 에지 배포에 최적화된 관계형 데이터베이스 엔진입니다. 업계 최고 수준의 성능, 보안 및 쿼리 처리 기능을 제공하는 Microsoft SQL Server 데이터베이스 엔진의 최신 버전을 기반으로 합니다. Azure SQL Edge는 SQL Server의 업계 최고의 관계형 데이터베이스 관리 기능과 함께 실시간 분석 및 복잡한 이벤트 처리를 위한 기본 제공 스트리밍 기능을 제공합니다.

또한 Azure SQL Edge는 SQL Edge 배포 중에 [SQL DATABASE DACPAC 및 BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 패키지를 배포할 수 있도록 하는 SqlPackage.exe의 기본 구현을 제공 합니다. SQL Database dacpac은 SQL Edge 모듈의 `module twin's desired properties` 옵션을 통해 노출된 SqlPackage 매개 변수를 사용하여 SQL Edge에 배포할 수 있습니다.

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|필드 | Description |
|------|-------------|
| SqlPackage | SQL Database DAC 또는 BACPAC 패키지를 포함 하는 *.zip* 파일의 Azure BLOB storage URI입니다. Zip 파일에는 여러 dac 패키지 또는 bacpac 파일이 모두 포함 될 수 있습니다.
| ASAJobInfo | ASA Edge 작업에 대한 Azure Blob 스토리지 URI입니다.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Edge에서 SQL Database DAC 패키지 사용

SQL Edge에서 SQL Database DAC 패키지 `(*.dacpac)` 또는 BACPAC 파일을 사용 하려면 `(*.bacpac)` 다음 단계를 수행 합니다.

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

    5. **모듈 설정** 페이지에서 SQL Edge 모듈에 대한 **구성**을 선택합니다.

    6. **IoT Edge 사용자 지정 모듈** 창에서 **모듈 쌍의 원하는 속성 설정**을 선택합니다. 다음 예제와 같이 `SQLPackage` 옵션에 대한 URI를 포함하도록 원하는 속성을 업데이트합니다.

        > [!NOTE]
        > 다음 JSON의 SAS URI는 예제일 뿐입니다. URI를 배포의 실제 URI로 바꿉니다.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac and/or the bacpac files",
                }
            }
        ```

    7. **저장**을 선택합니다.

    8. **모듈 설정** 페이지에서 **다음**을 선택합니다.

    9. **모듈 설정** 페이지에서 **다음**을 선택한 다음, **제출**을 선택합니다.

5. 모듈 업데이트 후 패키지 파일이 다운로드 되 고, 압축을 해제 하 고, SQL Edge 인스턴스에 대해 배포 됩니다.

Azure SQL Edge 컨테이너를 다시 시작할 때마다 `*.dacpac` 파일 패키지가 다운로드 되어 변경 내용에 대해 평가 됩니다. 새 버전의 dacpac 파일이 발견되면 변경 내용이 SQL Edge의 데이터베이스에 배포됩니다. Bacpac 파일 

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 SQL Edge 배포](deploy-portal.md)
- [스트림 데이터](stream-data.md)
- [SQL Edge(미리 보기)에서 Open Neural Network Exchange를 통한 기계 학습 및 AI](onnx-overview.md)
