---
title: "MySQL용 Azure Database에서 서버를 복원하는 방법 | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 MySQL용 Azure Database에서 서버를 복원하는 방법을 설명합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6c1c0f8a0c0e59661b70b787b551b8cfdb024cda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure Portal을 사용하여 MySQL용 Azure Database에서 서버를 백업 및 복원하는 방법

## <a name="backup-happens-automatically"></a>자동으로 수행되는 백업
MySQL용 Azure Database를 사용할 경우 데이터베이스 서비스가 자동으로 5분마다 서비스 백업을 수행합니다. 

백업은 기본 계층을 사용하는 경우 7일, 표준 계층을 사용하는 경우 35일 동안 사용할 수 있습니다. 자세한 내용은 [MySQL용 Azure Database 서비스 계층](concepts-service-tiers.md)을 참조하세요.

이 자동 백업 기능을 사용하면 서버 및 모든 데이터베이스를 새 서버에 이전 특정 시점으로 복원할 수도 있습니다.

## <a name="restore-in-the-azure-portal"></a>Azure Portal에서 복원
MySQL용 Azure Database를 사용하면 서버를 특정 시점으로 서버의 새로운 복사본에 복원할 수 있습니다. 이 새 서버를 사용하여 데이터를 복구할 수 있습니다. 

예를 들어 오늘 정오에 실수로 테이블을 삭제한 경우 정오 바로 전으로 복원하고 누락된 테이블과 데이터를 서버의 새로운 복사본에서 검색할 수 있습니다.

다음 단계는 샘플 서버를 특정 시점으로 복원합니다.

1. [Azure 포털](https://portal.azure.com/)

2. MySQL용 Azure Database 서버를 찾습니다. 왼쪽 창에서 **모든 리소스**를 선택한 다음, 목록에서 서버를 선택합니다.

3.  서버 개요 블레이드 맨 위에서 도구 모음에 있는 **복원**을 클릭합니다. 복원 블레이드가 열립니다.
![복원 단추 클릭](./media/howto-restore-server-portal/click-restore-button.png)

4. 필요한 정보로 복원 양식을 채웁니다.

- **복원 지점(UTC)**: 날짜 선택 및 시간 선택을 사용하여 복원할 특정 시점을 선택합니다. 지정된 시간은 UTC 형식이므로 현지 시간을 UTC로 변환해야 하는 경우가 많습니다.
- **새 서버에 복원**: 기존 서버를 복원할 새 서버 이름을 입력합니다.
- **위치**: 지역 선택은 자동으로 원본 서버 지역으로 채워지며 변경할 수 없습니다.
- **가격 책정 계층**: 가격 책정 계층 선택은 자동으로 원본 서버와 동일한 가격 책정 계층으로 채워지고 여기서 변경할 수 없습니다. 
![PITR 복원](./media/howto-restore-server-portal/pitr-restore.png)

5. **확인**을 클릭하여 서버를 특정 시점으로 복원합니다. 

6. 복원이 완료되면 예상대로 데이터베이스가 복원되었는지 확인하기 위해 생성하였던 새 서버를 찾습니다.

## <a name="next-steps"></a>다음 단계
- [MySQL용 Azure Database에 대한 연결 라이브러리](concepts-connection-libraries.md)