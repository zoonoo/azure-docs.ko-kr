---
title: Azure Database for PostgreSQL – 하이퍼스케일(Citus)의 미리 보기 기능
description: 현재 미리 보기로 제공되는 기능의 업데이트된 목록
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 5f0552e09464a6b571a665cfe3895f48c3aa8c41
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258455"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>PostgreSQL - 하이퍼스케일(Citus)의 미리 보기 기능

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 출시되지 않은 기능에 대한 미리 보기를 제공합니다. 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.  자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="features-currently-in-preview"></a>현재 미리 보기로 제공되는 기능

현재 미리 보기로 제공되는 기능은 다음과 같습니다.

* **[기본 계층](concepts-hyperscale-tiers.md)** . 코디네이터 노드만 사용하고 작업자 노드는 사용하지 않는 서버 그룹을 실행합니다. 초기 테스트 및 개발을 수행하고 소규모 프로덕션 워크로드를 처리하는 경제적인 방법입니다.
* **[PostgreSQL 12 및 13](concepts-hyperscale-versions.md)** .
  서버 그룹에 최신 데이터베이스 버전을 사용합니다.
* **[Citus 10](concepts-hyperscale-versions.md#citus-and-other-extension-versions)** .
  PostgreSQL 13을 실행하는 서버 그룹에 자동으로 설치됩니다.
* **[열 형식 스토리지](concepts-hyperscale-columnar.md)** .
  선택한 테이블의 열(행이 아님)을 디스크에 연속적으로 저장합니다. 온디스크 압축을 지원합니다. 분석 및 데이터 웨어하우징 워크로드에 적합합니다.
* **[읽기 복제본](howto-hyperscale-read-replicas-portal.md)** (현재 동일한 지역에만 해당) 주 서버 그룹에 발생하는 모든 변경 내용은 해당 복제본에 반영되고, 복제본에 대한 쿼리는 원본에 추가 로드를 발생시키지 않습니다.
  복제본은 읽기 전용 작업의 성능을 개선하는 데 유용한 도구입니다.
* **[관리되는 PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)** .
  여러 클라이언트에서 동시에 서버 그룹에 연결할 수 있도록 하고 활성 연결 수를 제한하는 연결 풀러. 코디네이터 노드를 원활하게 실행하면서 연결 요청을 충족합니다.
* **[pgAudit](concepts-hyperscale-audit.md)** . 표준 PostgreSQL 로깅 기능을 통해 자세한 세션 및 개체 감사 로깅을 제공합니다. 특정 정부, 금융 또는 ISO 인증 감사를 전달하는 데 필요한 감사 로그를 생성합니다.

### <a name="available-regions-for-preview-features"></a>미리 보기 기능을 사용할 수 있는 지역

pgAudit 확장은 [하이퍼스케일(Citus)에서 지원하는 모든 지역](concepts-hyperscale-configuration-options.md#regions)에서 사용할 수 있습니다.
다른 미리 보기 기능은 **미국 동부** 에서만 사용할 수 있습니다.

## <a name="does-my-server-group-have-access-to-preview-features"></a>내 서버 그룹에 미리 보기 기능에 대한 액세스 권한이 있나요?

하이퍼스케일(Citus) 서버 그룹이 미리 보기 기능을 사용할 수 있는지 확인하려면 Azure Portal의 서버 그룹 **개요** 페이지로 이동합니다.
속성 **계층: 기본(미리 보기)** 또는 **계층: 표준(미리 보기)** 이 표시되면 서버 그룹에 미리 보기 기능에 대한 액세스 권한이 있는 것입니다.

### <a name="how-to-get-access"></a>액세스 권한을 얻는 방법

새 하이퍼스케일(Citus) 서버 그룹을 만들 때 **미리 보기 기능 사용** 확인란을 선택합니다.

## <a name="contact-us"></a>문의처

[Azure DB for PostgreSQL 문의](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)에 이메일을 보내 미리 보기 기능 사용 경험을 알려주세요.
(이 이메일 주소는 기술 지원 채널이 아닙니다. 기술 문제를 문의하려면 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 여세요.)
