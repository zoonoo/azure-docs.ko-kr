---
title: 주 버전 업그레이드-Azure Portal-Azure Database for MySQL-단일 서버
description: 이 문서에서는를 사용 하 여 Azure Database for MySQL 단일 서버에 대 한 주 버전을 업그레이드할 수 있는 방법에 대해 설명 Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753054"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Azure Portal를 사용 하 Azure Database for MySQL 단일 서버에서 주 버전 업그레이드

> [!IMPORTANT]
> Azure database for MySQL 단일 서버에 대 한 주 버전 업그레이드는 공개 미리 보기 상태입니다.

이 문서에서는 Azure Database for MySQL 단일 서버에서 MySQL 주 버전을 현재 위치로 업그레이드 하는 방법을 설명 합니다.

이 기능을 통해 고객은 데이터 이동 이나 응용 프로그램 연결 문자열 변경 없이 단추를 클릭 하 여 MySQL 5.6 서버의 전체 업그레이드를 MySQL 5.7로 수행할 수 있습니다.

> [!Note]
> * 주 버전 업그레이드는 MySQL 5.6에서 MySQL 5.7으로 주 버전 업그레이드 하는 경우에만 사용할 수 있습니다.<br>
> * 주 버전 업그레이드는 아직 복제 서버에서 지원 되지 않습니다.
> * 업그레이드 작업을 수행 하는 동안에는 서버를 사용할 수 없습니다. 따라서 계획 된 유지 관리 기간 동안에는 업그레이드를 수행 하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [단일 서버 Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>MySQL 5.6에서 MySQL 5.7로 주 버전 업그레이드 수행

MySQL 5.6 서버의 Azure 데이터베이스에 대 한 주 버전 업그레이드를 수행 하려면 다음 단계를 따르세요.

> [!IMPORTANT]
> 프로덕션을 직접 업그레이드 하는 대신 복원 된 서버 복사본에서 업그레이드를 먼저 수행 하는 것이 좋습니다. 지정 [시간 복원을 수행 하는 방법을](howto-restore-server-portal.md#point-in-time-restore)참조 하세요. 

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 5.6 서버를 선택 합니다.

2. **개요** 페이지의 도구 모음에서 **업그레이드** 단추를 클릭 합니다.

3. **업그레이드** 섹션에서 **확인** 을 선택 하 여 Azure database for MySQL 5.6 서버를 5.7 서버로 업그레이드 합니다.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-개요-업그레이드":::

4. 알림이 성공적으로 업그레이드 되었는지 확인 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**1. 업그레이드 해야 하는 프로덕션 환경에 MySQL v 5.6이 있으므로이 업그레이드 기능이 GA 될 예정 인가요?**

이 기능의 GA는 MySQL v 5.6 사용 중지 전에 계획 됩니다. 그러나이 기능은 프로덕션 준비가 되었으며 Azure에서 완전히 지원 되므로 사용자 환경에서 자신 있게 실행 해야 합니다. 권장 되는 모범 사례로, 업그레이드 하는 동안 가동 중지 시간을 예측 하 고 프로덕션 환경에서 실행 하기 전에 응용 프로그램 호환성 테스트를 수행할 수 있도록 서버의 복원 된 복사본에서 먼저 실행 하 고 테스트 하는 것이 좋습니다. 자세한 내용은 지정 시간 [복원을 수행](howto-restore-server-portal.md#point-in-time-restore) 하 여 서버의 특정 시점 복사본을 만드는 방법을 참조 하세요. 

**2 .이로 인해 서버의 가동 중지 시간이 발생 하는 경우 시간이 오래 걸릴 수 있나요?**

예, 업그레이드 프로세스 중에는 서버를 사용할 수 없으므로 계획 된 유지 관리 기간 동안이 작업을 수행 하는 것이 좋습니다. 예상 되는 가동 중지 시간은 데이터베이스 크기, 프로 비전 된 저장소 크기 (IOPs 프로 비전 됨) 및 데이터베이스의 테이블 수에 따라 달라 집니다. 업그레이드 시간은 서버의 테이블 수에 직접적으로 비례 합니다. 기본 SKU 서버 업그레이드는 표준 저장소 플랫폼에 비해 시간이 더 오래 걸릴 것으로 예상 됩니다. 서버 환경의 가동 중지 시간을 예측 하려면 먼저 복원 된 서버 복사본에서 업그레이드를 수행 하는 것이 좋습니다.  

**3. 아직 복제 서버에서는 지원 되지 않습니다. 구체적인 의미는 무엇 인가요?**

현재는 복제본 서버에 대해 주 버전 업그레이드가 지원 되지 않으므로 복제 (원본 또는 복제 서버)와 관련 된 서버에 대해 실행 하면 안 됩니다. 업그레이드 기능에 대 한 복제본 지원 기능을 추가 하기 전에 복제와 관련 된 서버 업그레이드를 테스트 하려는 경우 다음 단계를 수행 하는 것이 좋습니다.

1. 계획 된 유지 관리 중에 [복제를 중지 하 고](howto-read-replicas-portal.md) 해당 이름과 모든 구성 정보 (방화벽 설정, 원본 서버와 다른 경우 서버 매개 변수 구성)를 캡처한 후 복제 서버를 삭제 합니다.
2. 원본 서버 업그레이드를 수행 합니다.
3. 1 단계에서 캡처한 동일한 이름 및 구성 설정을 사용 하 여 새 읽기 복제 서버를 프로 비전 합니다. 원본 서버가 v 5.7로 업그레이드 된 후에는 새 복제본 서버가 자동으로 v 5.7에 있습니다.

**4.2021 년 2 월 5 일 이전에 MySQL v 5.6 서버를 업그레이드 하도록 선택 하지 않으면 어떻게 되나요?**

계속 하기 전에 MySQL v 5.6 서버를 계속 실행할 수 있습니다. Azure는 서버에서 강제 업그레이드 **를 수행 하지 않습니다** . 그러나 [Azure Database for MySQL 버전 관리 정책](concepts-version-policy.md) 에 설명 된 제한 사항이 적용 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Database for MySQL 버전 관리 정책](concepts-version-policy.md)에 대해 알아봅니다.
