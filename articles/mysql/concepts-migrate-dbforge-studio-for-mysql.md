---
title: dbForge Studio for MySQL을 사용하여 MySQL 데이터베이스를 Azure Database for MySQL로 마이그레이션
description: 이 문서에서는 dbForge Studio for MySQL을 사용하여 Azure Database for MySQL로 마이그레이션하는 방법을 보여줍니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: f29d64da678ca65c91a90857cfa5a0e533cc72d6
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989165"
---
# <a name="migrate-data-to-azure-database-for-mysql-with-dbforge-studio-for-mysql"></a>dbForge Studio for MySQL을 사용하여 Azure Database for MySQL로 데이터 마이그레이션

MySQL 데이터베이스를 Azure Database for MySQL로 이동하려고 하십니까? dbForge Studio for MySQL의 마이그레이션 도구를 사용하면 좋습니다. 이 도구를 사용하면 데이터베이스 전송을 구성, 저장, 편집, 자동화 및 예약할 수 있습니다.

이 문서의 예제를 완료하려면 [dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/)을 다운로드하여 설치해야 합니다.

## <a name="connect-to-azure-database-for-mysql"></a>Azure Database for MySQL에 연결

1. dbForge Studio for MySQL의 **데이터베이스** 메뉴에서 **새 연결** 을 선택합니다.

1. 호스트 이름 및 로그인 자격 증명을 제공합니다.

1. **연결 테스트** 를 선택하여 구성을 확인합니다.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure Database for MySQL에 대한 연결 테스트 성공을 보여주는 스크린샷" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png":::

## <a name="migrate-with-the-backup-and-restore-functionality"></a>백업 및 복원 기능으로 마이그레이션

dbForge Studio for MySQL을 사용하여 데이터베이스를 Azure로 마이그레이션하는 경우 다양한 옵션 중에서 선택할 수 있습니다. 전체 데이터베이스를 이동해야 하는 경우에는 **백업 및 복원** 기능을 사용하는 것이 가장 좋습니다.

이 예제에서는 *sakila* 데이터베이스를 MySQL 서버에서 Azure Database for MySQL로 마이그레이션합니다. **백업 및 복원** 기능을 사용하는 논리는 MySQL 데이터베이스의 백업을 만든 다음, Azure Database for MySQL에서 복원하는 것입니다.

### <a name="back-up-the-database"></a>데이터베이스 백업

1. dbForge Studio for MySQL의 **백업 및 복원** 메뉴에서 **데이터베이스 백업** 을 선택합니다. **데이터베이스 백업 마법사** 가 나타납니다.

1. **데이터베이스 백업 마법사** 의 **콘텐츠 백업** 탭에서 백업하려는 데이터베이스 개체를 선택합니다.

1. **옵션** 탭에서 요구 사항에 맞게 백업 프로세스를 구성합니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="백업 마법사의 옵션 창을 보여주는 스크린샷" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png":::

1. **다음** 을 선택한 다음, 오류 처리 동작 및 로깅 옵션을 지정합니다.

1. **백업** 을 선택합니다.

### <a name="restore-the-database"></a>데이터베이스 복원

1.  dbForge Studio for MySQL에서 Azure Database for MySQL에 연결합니다. [지침을 참조하세요](#connect-to-azure-database-for-mysql).

1. **백업 및 복원** 메뉴에서 **데이터베이스 복원** 을 선택합니다. **데이터베이스 복원 마법사** 가 나타납니다.

1. **데이터베이스 복원 마법사** 에서 데이터베이스 백업이 포함된 파일을 선택합니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="데이터베이스 복원 마법사의 복원 단계를 보여주는 스크린샷" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png":::

1. **복원** 을 선택합니다.

1. 결과를 확인합니다.

## <a name="migrate-with-the-copy-databases-functionality"></a>데이터베이스 복사 기능을 사용하여 마이그레이션

dbForge Studio for MySQL의 **데이터베이스 복사** 기능은 **백업 및 복원과** 비슷합니다. 단, 데이터베이스를 마이그레이션하는 데 두 단계가 필요하지 않습니다. 또한 한 번에 두 개 이상의 데이터베이스를 전송할 수 있습니다.

>[!NOTE]
> **데이터베이스 복사** 기능은 dbForge Studio for MySQL의 Enterprise Edition에서만 사용할 수 있습니다.

이 예제에서는 *world_x* 데이터베이스를 MySQL 서버에서 Azure Database for MySQL로 마이그레이션합니다.

데이터베이스 복사 기능을 이용해 데이터베이스를 마이그레이션하려면 다음을 수행합니다.

1. dbForge Studio for MySQL의 **데이터베이스** 메뉴에서 **데이터 복사** 를 선택합니다. 

1. **데이터베이스 복사** 탭에서 원본 및 대상 연결을 지정합니다. 마이그레이션할 데이터베이스도 선택합니다. 

   Azure MySQL 연결을 입력하고 *world_x* 데이터베이스를 선택합니다. 녹색 화살표를 선택하여 프로세스를 시작합니다.

1. 결과를 확인합니다.

*world_x* 데이터베이스가 Azure MySQL에 표시되는 것을 볼 수 있습니다.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="데이터베이스 복사 기능의 결과를 보여주는 스크린샷" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png":::

## <a name="migrate-a-database-with-schema-and-data-comparison"></a>스키마 및 데이터 비교를 통해 데이터베이스 마이그레이션

dbForge Studio for MySQL을 사용하여 데이터베이스, 스키마 및/또는 데이터를 Azure로 마이그레이션하는 경우 다양한 옵션 중에서 선택할 수 있습니다. MySQL 데이터베이스에서 Azure로 선택적 테이블을 이동해야 하는 경우 **스키마 비교** 및 **데이터 비교** 기능을 사용하는 것이 가장 좋습니다.

이 예제에서는 *world* 데이터베이스를 MySQL 서버에서 Azure Database for MySQL로 마이그레이션합니다. 

**백업 및 복원** 기능을 사용하는 논리는 MySQL 데이터베이스의 백업을 만든 다음, Azure Database for MySQL에서 복원하는 것입니다.

이 방식의 논리는 Azure Database for MySQL에서 빈 데이터베이스를 만들고 원본 MySQL 데이터베이스와 동기화하는 것입니다. 먼저 **스키마 비교** 도구를 사용하고 다음으로 **데이터 비교** 기능을 사용합니다. 이러한 단계를 통해 MySQL 스키마 및 데이터를 Azure로 정확하게 이동할 수 있습니다.

이 연습을 완료하려면 먼저 [Azure Database for MySQL에 연결](#connect-to-azure-database-for-mysql)하고 빈 데이터베이스를 만들어야 합니다.

### <a name="schema-synchronization"></a>스키마 동기화

1. **비교** 메뉴에서 **새 스키마 비교** 를 선택합니다. **새 스키마 비교 마법사** 가 나타납니다.

1. 원본 및 대상을 선택한 다음, 스키마 비교 옵션을 지정합니다. **비교** 를 선택합니다.

1. 표시되는 비교 결과 그리드에서 동기화할 개체를 선택합니다. 녹색 화살표 단추를 선택하여 **스키마 동기화 마법사** 를 엽니다.

1. 마법사의 단계를 진행하여 동기화를 구성합니다. **동기화** 를 선택하여 변경 내용을 배포합니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="스키마 동기화 마법사를 보여주는 스크린샷" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png":::

### <a name="data-comparison"></a>데이터 비교

1. **비교** 메뉴에서 **새 데이터 비교** 를 선택합니다. **새 데이터 비교 마법사** 가 나타납니다.

1. 원본 및 대상을 선택한 다음, 데이터 비교 옵션을 지정합니다. 필요한 경우 매핑을 변경하고 **비교** 를 선택합니다.

1. 표시되는 비교 결과 그리드에서 동기화할 개체를 선택합니다. 녹색 화살표 단추를 선택하여 **데이터 동기화 마법사** 를 엽니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="데이터 비교 결과를 보여주는 스크린샷" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png":::

1. 마법사의 단계를 진행하여 동기화를 구성합니다. **동기화** 를 선택하여 변경 내용을 배포합니다.

1. 결과를 확인합니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="데이터 동기화 마법사의 결과를 보여주는 스크린샷" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png":::

## <a name="next-steps"></a>다음 단계
- [MySQL 개요](overview.md)
