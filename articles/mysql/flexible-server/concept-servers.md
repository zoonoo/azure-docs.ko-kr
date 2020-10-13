---
title: 서버 개념-유연한 서버 Azure Database for MySQL
description: 이 항목에서는 Azure Database for MySQL 유연한 서버를 사용 하기 위한 고려 사항 및 지침을 제공 합니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7217817e9add6214c2da8362a2769cad0c2cf330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939314"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Azure Database for MySQL 유연한 서버 (미리 보기)의 서버 개념

> [!IMPORTANT] 
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 Azure Database for MySQL 유연한 서버를 사용 하기 위한 고려 사항 및 지침을 제공 합니다.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버는 무엇 인가요?

Azure Database for MySQL 유연한 서버는 커뮤니티 버전의 MySQL을 실행 하는 완전히 관리 되는 데이터베이스 서비스입니다. 일반적으로 서비스는 사용자 요구 사항에 따라 유연성 및 구성 사용자 지정을 제공 하도록 설계 되었습니다. 온-프레미스 환경에서도 익숙할 수 있는 동일한 MySQL 서버 구문입니다. 특히 유연한 서버는 관리 되며, 기본적으로 제공 되는 성능, 향상 된 서버 관리 효율성 및 제어 기능을 제공 하 고, 서버 수준에서 액세스 및 기능을 노출 합니다.

Azure Database for MySQL 유연한 서버:

- Azure 구독 내에서 만들어집니다.
- 데이터베이스에 대한 부모 리소스입니다.
- 서버 매개 변수를 통해 MySQL 구성을 노출할 수 있습니다 (서버 매개 변수 개념에 대 한 링크).
- 자동화 된 백업을 수행 하 고 특정 시점 복원을 지원 합니다.
- 데이터베이스에 대한 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 컨테이너로서 서버를 삭제하고 포함된 데이터베이스를 삭제합니다.
- 하위 지역에 리소스를 배치합니다.
- 고객 제공 서버 유지 관리 일정에 대 한 지원
- 고가용성을 위해 영역 중복 설정에서 유연한 서버를 배포 하는 기능
- 데이터베이스 서버 액세스에 대 한 가상 네트워크 통합을 제공 합니다.
- 사용 하지 않을 때 유연한 서버를 일시 중지 하 여 비용을 절감 하는 방법을 제공 합니다.
- 로그인, 방화벽, 사용자, 역할 구성 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다.
- 는 현재 MySQL 5.7 버전에서 지원 됩니다. 자세한 내용은 [지원 되는 Azure Database for MySQL 엔진 버전](./concepts-supported-versions.md)을 참조 하세요.

Azure Database for MySQL 유연한 서버 내에서 하나 이상의 데이터베이스를 만들 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 가격은 계산 계층, vCores 및 저장소 (GB)의 구성에 따라 서버 별로 구성 됩니다. 자세한 내용은 [compute and storage](./concepts-compute-storage.md)를 참조 하세요.

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버 중지/시작

유연한 서버를 Azure Database for MySQL 사용 하지 않을 때 서버를 **중지** 하 고 작업을 다시 시작할 때 서버를 **시작** 하는 기능을 제공 합니다. 이는 기본적으로 데이터베이스 서버에 대 한 비용을 절감 하 고 사용 하는 경우에만 리소스에 대 한 비용을 지불 합니다. 이는 개발-테스트 워크 로드에 대해 매우 중요 하 고 하루 중에만 서버를 사용 하는 경우에도 더욱 중요 합니다. 서버를 중지 하면 모든 활성 연결이 삭제 됩니다. 나중에 서버를 다시 온라인 상태로 전환 하려는 경우 [Azure Portal](how-to-stop-start-server-portal.md) 또는 CLI를 사용할 수 있습니다.

서버가 **중지** 됨 상태 이면 서버의 계산에는 요금이 청구 되지 않습니다. 그러나 서버가 다시 시작 될 때 데이터 파일을 사용할 수 있도록 서버의 저장소가 남아 있으므로 저장소는 계속 청구 됩니다.

> [!IMPORTANT]
> 서버를 **중지** 하면 스트레치에서 다음 7 일 동안 해당 상태가 유지 됩니다. 이 시간 동안 수동으로 **시작** 하지 않는 경우 서버는 7 일이 끝날 때 자동으로 시작 됩니다. 서버를 사용 하지 않는 경우 다시 **중지** 하도록 선택할 수 있습니다.

서버를 중지 하는 동안 서버에서 관리 작업을 수행할 수 없습니다. 서버에서 구성 설정을 변경 하려면 [서버를 시작](how-to-stop-start-server-portal.md)해야 합니다. [중지/시작 제한](./concepts-limitations.md#stopstart-operation)을 참조 하세요.

## <a name="how-do-i-manage-a-server"></a>서버는 어떻게 관리해야 하나요?

[Azure Portal](./quickstart-create-server-portal.md) 또는 [Azure CLI](./quickstart-create-server-cli.md)를 사용 하 여 Azure Database for MySQL 유연한 서버를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

-    [서버 만들기](./quickstart-create-server-portal.md) 에 대 한 자세한 정보
-   [모니터링 및 경고](./how-to-alert-on-metric.md) 에 대 한 자세한 정보

