---
title: dbForge Studio for MySQL을 사용하여 Azure Database for MySQL에 연결
description: 이 문서에서는 dbForge Studio for MySQL을 통해 Azure Database for MySQL 서버에 연결하는 방법을 보여줍니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: d5d694820c2ffd09868d81693d4f98f839a139d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591880"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>dbForge Studio for MySQL을 사용하여 Azure Database for MySQL에 연결

[dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/)을 사용하여 Azure Database for MySQL에 연결하려면 다음을 수행합니다.

1. 데이터베이스 메뉴에서 새 연결을 선택합니다.

2. 호스트 이름 및 로그인 자격 증명을 제공합니다.

3. 연결 테스트 단추를 선택하여 구성을 확인합니다.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure 연결":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>백업 및 복원 기능을 사용하여 데이터베이스 마이그레이션

스튜디오를 사용하면 여러 가지 방법으로 Azure로 데이터베이스를 마이그레이션할 수 있으며, 선택은 사용자의 요구에 따라서만 달라집니다. 전체 데이터베이스를 이동해야 하는 경우에는 백업 및 복원 기능을 사용하는 것이 가장 좋습니다. 이 예제에서는 MySQL 서버에 있는 *sakila* 데이터베이스를 Azure Database for MySQL로 마이그레이션합니다. dbForge Studio for MySQL의 백업 및 복원 기능을 사용하여 마이그레이션 프로세스를 진행하는 논리는 MySQL 데이터베이스의 백업을 만든 후 Azure Database for MySQL에서 복원하는 것입니다.

### <a name="back-up-the-database"></a>데이터베이스 백업

1. 데이터베이스 메뉴에서 백업 및 복원을 가리킨 다음 데이터베이스 백업을 선택합니다. 데이터베이스 백업 마법사가 나타납니다.

2. 데이터베이스 백업 마법사의 백업 콘텐츠 탭에서 백업하려는 데이터베이스 개체를 선택합니다.

3. 옵션 탭에서 요구 사항에 맞게 백업 프로세스를 구성합니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="백업 마법사 옵션":::

4. 다음으로 오류 처리 동작 및 로깅 옵션을 지정합니다.

5. 백업을 선택합니다.

### <a name="restore-the-database"></a>데이터베이스 복원

1. 위에서 설명한 대로 Azure Database for MySQL에 연결합니다.

2. 데이터베이스 탐색기 본문을 마우스 오른쪽 단추로 클릭하고 백업 및 복원을 가리킨 다음 데이터베이스 복원을 선택합니다.

3. 데이터베이스 복원 마법사가 열리면 데이터베이스 백업이 포함된 파일을 선택합니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="복원 단계":::

4. 복원을 선택합니다.

5. 결과를 확인합니다.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>데이터베이스 복사 기능을 사용하여 데이터베이스 마이그레이션

데이터베이스 복사 기능은 백업 및 복원과 유사하지만, 이 경우 데이터베이스를 마이그레이션하는 두 단계가 필요하지 않습니다. 그리고 이 기능을 사용하면 한 번에 둘 이상의 데이터베이스를 전송할 수 있습니다. 데이터베이스 복사 기능은 dbForge Studio for MySQL의 Enterprise Edition에서만 사용할 수 있습니다.
이 예제에서는 *world_x* 데이터베이스를 MySQL 서버에서 Azure Database for MySQL로 마이그레이션합니다.
데이터베이스 복사 기능을 이용해 데이터베이스를 마이그레이션하려면 다음을 수행합니다.

1. 데이터베이스 메뉴에서 데이터베이스 복사를 선택합니다. 

2. 표시되는 데이터베이스 복사 탭에서 원본 및 대상 연결을 지정하고 마이그레이션할 데이터베이스를 선택합니다. Azure MySQL 연결을 입력하고 *world_x* 데이터베이스를 선택합니다. 녹색 화살표를 선택하여 프로세스를 시작합니다.

3. 결과를 확인합니다.

데이터베이스 마이그레이션 활용으로 *world_x* 데이터베이스가 Azure MySQL에 성공적으로 표시되었습니다.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="데이터베이스 복사 결과":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>스키마 및 데이터 비교 도구를 사용하여 데이터베이스 마이그레이션

dbForge Studio for MySQL은 MySQL 데이터베이스, MySQL 스키마 및/또는 데이터를 Azure로 마이그레이션할 수 있는 몇 가지 도구를 통합합니다. 기능 선택은 사용자의 요구 및 프로젝트 요구 사항에 따라 달라집니다. 데이터베이스를 선택적으로 이동해야 하는 경우, 즉 특정 MySQL 테이블을 Azure로 마이그레이션하려면 스키마 및 데이터 비교 기능을 사용하는 것이 가장 좋습니다.
이 예에서는 MySQL 서버에 있는 *world* 데이터베이스를 Azure Database for MySQL로 마이그레이션합니다. DbForge Studio for MySQL의 스키마 및 데이터 비교 기능을 사용하여 마이그레이션 프로세스를 수행하는 논리는 Azure Database for MySQL에서 빈 데이터베이스를 만들고 먼저 스키마 비교 도구를 사용하여 필요한 MySQL 데이터베이스로 동기화한 다음 데이터 비교 도구를 사용하는 것입니다. 이러한 방식으로 MySQL 스키마와 데이터가 Azure로 정확하게 이동합니다.

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Azure Database for MySQL에 연결하여 빈 데이터베이스 만들기

Azure Database for MySQL에 연결하여 빈 데이터베이스를 만듭니다.

### <a name="step-2-schema-synchronization"></a>2단계. 스키마 동기화

1. 비교 메뉴에서 새 스키마 비교를 선택합니다.
새 스키마 비교 마법사가 나타납니다.

2. 원본 및 대상을 선택하고 스키마 비교 옵션을 지정합니다. 비교를 선택합니다.

3. 표시되는 비교 결과 그리드에서 동기화할 개체를 선택합니다. 녹색 화살표 단추를 선택하여 스키마 동기화 마법사를 엽니다.

4. 마법사의 단계를 진행하여 동기화를 구성합니다. 동기화를 선택하여 변경 내용을 배포합니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="스키마 동기화 마법사":::

### <a name="data-comparison"></a>데이터 비교

1. 비교 메뉴에서 새 데이터 비교를 선택합니다. 새 데이터 비교 마법사가 나타납니다.

2. 원본 및 대상을 선택하고 필요한 경우 데이터 비교 옵션을 지정하고 매핑을 변경합니다. 비교를 선택합니다.

3. 표시되는 비교 결과 그리드에서 동기화할 개체를 선택합니다. 녹색 화살표 단추를 선택하여 데이터 동기화 마법사를 엽니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="데이터 구성 요소 결과":::

4. 마법사의 단계를 진행하여 동기화를 구성합니다. 동기화를 선택하여 변경 내용을 배포합니다.

5. 결과를 확인합니다.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="데이터 동기화 결과":::

## <a name="summary"></a>요약

오늘날 더 많은 기업이 데이터베이스를 Azure Database for MySQL로 이동합니다. 이는 해당 데이터베이스 서비스를 사용하여 쉽게 설정하고 관리하고 크기를 조정할 수 있기 때문입니다. 마이그레이션은 어려운 과정일 필요가 없습니다. MySQL용 dbForge 스튜디오의 완벽한 마이그레이션 도구를 통해 프로세스를 대폭 지원할 수 있습니다. 스튜디오를 사용하면 데이터베이스 전송을 쉽게 구성, 저장, 편집, 자동화 및 예약할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [MySQL 개요](overview.md)
