---
title: "마이그레이션: 데이터 웨어하우스 마이그레이션 유틸리티 | Microsoft Docs"
description: "SQL 데이터 웨어하우스로 마이그레이션합니다."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 61adb7ae8fddc3cf423ee4558308eb9ded11fea3
ms.lasthandoff: 12/08/2016


---
# <a name="data-warehouse-migration-utility-preview"></a>데이터 웨어하우스 마이그레이션 유틸리티(미리 보기)
> [!div class="op_single_selector"]
> * [마이그레이션 유틸리티 다운로드][Download Migration Utility]
> 
> 

데이터 웨어하우스 마이그레이션 유틸리티는 SQL 서버 및 Azure SQL 데이터베이스에서 Azure SQL 데이터 웨어하우스로 스키마와 데이터를 마이그레이션하기 위해 설계된 도구입니다. 스키마를 마이그레이션하는 동안 이 도구는 해당 스키마를 소스에서 대상으로 자동으로 매핑합니다. 스키마가 마이그레이션된 후 이 도구는 자동으로 생성된 스크립트를 사용하여 데이터를 이동하는 옵션을 제공합니다.

이 도구는 스키마 및 데이터 마이그레이션 외에도, 효율적인 마이그레이션을 방해하는 대상 및 소스 간 비호환성을 요약하는 호환성 보고서를 생성하는 옵션을 제공합니다.

## <a name="get-started"></a>시작
설치를 위한 필수 요소로 마이그레이션 스크립트를 실행하기 위한 BCP 명령줄 유틸리티와 호환성 보고서를 보기 위한 Office가 필요합니다 다운로드한 실행 파일을 실행하면 도구를 설치하기 전에 표준 EULA에 동의하라는 메시지가 표시됩니다.

또한 마이그레이션 유틸리티를 실행하려면 마이그레이션하려는 데이터베이스에서 CREATE DATABASE, ALTER ANY DATABASE 또는 VIEW ANY DEFINITION 권한 중 하나가 있어야 합니다.

### <a name="launching-the-tool-and-connecting"></a>도구 시작 및 연결
설치 후 나타나는 바탕 화면 아이콘을 클릭하여 도구를 시작합니다. 도구를 열면 초기 연결 페이지가 표시됩니다. 여기에서 마이그레이션 도구의 원본 및 대상을 선택할 수 있습니다. 현재는 원본으로 SQL Server 및 Azure SQL 데이터베이스를, 대상으로 SQL 데이터 웨어하우스를 지원합니다. 선택한 후에는 서버 이름을 입력하고 인증한 후 '연결'을 클릭하여 소스 서버에 연결하라는 메시지가 표시됩니다.

인증한 후 도구는 연결된 서버에 있는 데이터베이스 목록을 보여 줍니다. 마이그레이션할 데이터베이스를 선택한 후 '마이그레이션 선택됨'을 클릭하여 마이그레이션을 시작할 수 있습니다.

## <a name="migration-report"></a>마이그레이션 보고서
도구에서 '데이터베이스 호환성 검사'를 선택하면 마이그레이션하려는 데이터베이스에서 모든 개체 비호환성을 요약하는 보고서가 생성됩니다. SQL Data Warehouse에 없는 SQL Server 기능에 대한 광범위한 목록은 [마이그레이션 설명서][migration documentation]에서 확인할 수 있습니다. 보고서가 생성되면 Excel에서 보고서를 저장하고 열 수 있습니다.

마이그레이션 스키마를 생성할 때는 '개체'로 식별되는 대부분의 문제가 해당 데이터의 즉각적인 마이그레이션을 허용하기 위해 조정됩니다. 해당 스키마가 적용되기 전에 추가로 조정되지 않도록 변경 내용을 검토하십시오.

## <a name="migrate-schema"></a>마이그레이션 스키마
연결한 후에는 '스키마 마이그레이션'을 선택하면 선택한 테이블에 대한 스키마 마이그레이션 스크립트가 생성됩니다. 이 스크립트는 테이블 구조를 포팅하고 호환되지 않는 데이터 형식을 보다 호환 가능한 양식으로 매핑하며 마이그레이션 설정에서 사용자가 표시한 경우 보안 자격 증명 및 스키마를 생성합니다. 이 코드는 대상 SQL 데이터 웨어하우스 인스턴스에 대해 실행할 수 있으며 파일로 저장하고 클립보드에 복사하거나 추가 작업을 수행하기 전에 인라인으로 편집할 수도 있습니다.  

위에 언급된 것처럼 스키마를 마이그레이션할 때는 도구에서 마이그레이션 변경 내용을 검토하여 완전히 이해해야 합니다.  

## <a name="migrate-data"></a>데이터 마이그레이션
'데이터 마이그레이션' 옵션을 클릭하여 먼저 데이터를 서버의 플랫 파일로 이동한 후 SQL 데이터 웨어하우스로 직접 이동하는 BCP 스크립트를 생성할 수 있습니다. 다시 시도가 기본 제공되지 않고 네트워크 연결이 끊길 경우 오류가 발생할 수 있으므로 적은 양의 데이터 이동에는 이 프로세스를 사용하는 것이 좋습니다. 이를 실행하기 위해서는 BCP 명령줄 유틸리티가 설치되어 있어야 하며 데이터에 대한 스키마가 이미 생성되어 있어야 합니다.

위의 매개 변수를 입력한 후 마이그레이션 실행을 클릭하기만 하면 두 패키지 집합이 지정된 위치에 생성됩니다. 마이그레이션 소스에서 플랫 파일로 데이터를 내보내려면 파일 내보내기를 실행하고 데이터를 SQL 데이터 웨어하우스로 가져오려면 파일 가져오기를 실행합니다.

## <a name="next-steps"></a>다음 단계
일부 데이터를 마이그레이션했으니 [개발][develop] 방법을 알아보겠습니다.

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

