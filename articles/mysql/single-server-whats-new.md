---
title: Azure Database for MySQL 단일 서버의 새로운 기능
description: MySQL Community Edition을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스인 Azure Database for MySQL - 단일 서버에 대한 최신 업데이트에 관해 알아봅니다.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 6b77ede348073dd09c0ba44bfe282d3bf546a092
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958550"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 단일 서버의 새로운 기능은 무엇인가요?

Azure Database for MySQL은 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. 이 서비스는 [MySQL Community Edition](https://www.mysql.com/products/community/)(GPLv2 라이선스에서 사용 가능) 데이터베이스 엔진을 기반으로 하며 버전 5.6, 5.7 및 8.0을 지원합니다. [Azure Database for MySQL - 단일 서버](./overview.md#azure-database-for-mysql---single-server)는 데이터베이스 사용자 지정에 대한 최소한의 요구 사항으로 완전 관리형 데이터베이스 서비스를 제공하는 배포 모드입니다. 단일 서버 플랫폼은 최소한의 사용자 구성과 제어를 통해 패치, 백업, 고가용성 및 보안 등 대부분의 데이터베이스 관리 기능을 처리하도록 설계되었습니다.

이 문서에서는 2021년 1월부터 Azure Database for MySQL - 단일 서버의 새로운 릴리스 및 기능을 요약합니다. 목록은 가장 최근의 업데이트가 먼저 있는 역방향 시간순으로 표시됩니다.

## <a name="february-2021"></a>2021년 2월

이 Azure Database for MySQL - 단일 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- 버전 5.7 및 8.0 대용량 스토리지 서버의 데이터 입력에 대한 GTID(전역 트랜잭션 식별자)를 지원하는 새 저장 프로시저가 추가되었습니다.
- MySQL 버전을 5.6.50 및 5.7.32로 지원하도록 업데이트되었습니다.

## <a name="january-2021"></a>2021년 1월

이 Azure Database for MySQL - 단일 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- 첫 번째 관리자 권한을 자동으로 수정하도록 "암호 재설정"을 사용하도록 설정했습니다.
- `auto_increment_increment/auto_increment_offset` 서버 매개 변수 및 `session_track_gtids`가 노출되었습니다.
- innodb 버퍼 풀 덤프/복원 제어에 대한 새 저장 프로시저가 추가되었습니다.
- 큰 스토리지 서버에 대한 innodb 준비 관련 서버 매개 변수가 노출되었습니다.

## <a name="contacts"></a>연락처

Azure Database for MySQL 작업에 대한 질문이나 제안이 있는 경우 Azure Database for MySQL 팀([@AskAzure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))에 문의하세요. 이 이메일 주소는 기술 지원 별칭이 아닙니다.

또한 문의의 다음 사항을 적절히 고려해 주세요.

- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL 가격 책정](https://azure.microsoft.com/pricing/details/mysql/server/)에 대해 자세히 알아봅니다.
- Azure Database for MySQL - 단일 서버에 대한 [공용 설명서](./single-server/index.yml)를 찾아보세요.
- [일반적인 오류 문제 해결](./howto-troubleshoot-common-errors.md)에 대한 세부 정보를 검토합니다.