---
title: Azure Security Center에 예정된 중요한 변경
description: 알아야 하고 계획해야 할 수 있는 Azure Security Center에 예정된 변경입니다
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: ba0c68589f7ea4b32970e3ad81fea5175b998dc7
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629156"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center에 예정된 중요한 변경

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Security Center에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾고 있으면 [Azure Security Center의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>추가할 Azure 보안 벤치마크와 관련된 추천 사항(미리 보기)

| 양상 | 세부 정보 |
|---------|---------|
|공지 날짜 | 2020년 10월 26일  |
|이 변경에 대한 날짜  |  2020년 11월 |
|영향     | 검토할 더 많은 추천 사항이 있을 수 있습니다.<br>보안 점수에 즉각적인 영향을 주지 않음 - 미리 보기 추천 사항은 보안 점수에 영향을 주지 않습니다.<br>리소스 상태에 즉각적인 영향을 주지 않음 - 미리 보기 추천 사항은 리소스를 "비정상"으로 렌더링하지 않습니다.|
|  |  |

Azure 보안 벤치마크는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침 세트입니다. [Azure 보안 벤치마크에 대해 자세히 알아보세요](../security/benchmarks/introduction.md).

벤치마크의 적용 범위를 넓히기 위해 다음 29개의 새로운 추천 사항이 Security Center에 추가됩니다.

미리 보기 추천 사항은 리소스를 비정상으로 렌더링하지 않으며 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 끝나면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정합니다. [Azure Security Center의 추천 사항 수정](security-center-remediate-recommendations.md)에서 이러한 추천 사항에 대응하는 방법에 대해 자세히 알아보세요.

- Azure Backup을 가상 머신에 사용하도록 설정해야 합니다.
- SQL 서버에 대한 감사 보존 기간은 90일 이상으로 설정해야 합니다.
- App Service에서 진단 로그를 사용하도록 설정해야 합니다. 
- MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.
- PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.
- API 앱에서 FTPS를 요구해야 합니다.
- 함수 앱에서 FTPS를 요구해야 합니다.
- 웹앱에서 FTPS를 요구해야 합니다.
- Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Java를 최신 API 앱 버전으로 업데이트해야 합니다.
- Java를 최신 함수 앱 버전으로 업데이트해야 합니다.
- Java를 최신 웹앱 버전으로 업데이트해야 합니다.
- API 앱에서 관리 ID를 사용해야 합니다.
- 함수 앱에서 관리 ID를 사용해야 합니다.
- 웹앱에서 관리 ID를 사용해야 합니다.
- PHP를 최신 API 앱 버전으로 업데이트해야 합니다.
- PHP를 최신 웹앱 버전으로 업데이트해야 합니다.
- 프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.
- 프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.
- 프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.
- Python을 최신 API 앱 버전으로 업데이트해야 합니다.
- Python을 최신 함수 앱 버전으로 업데이트해야 합니다.
- Python을 최신 웹앱 버전으로 업데이트해야 합니다.
- TLS를 최신 API 앱 버전으로 업데이트해야 합니다.
- TLS를 최신 함수 앱 버전으로 업데이트해야 합니다.
- TLS를 최신 웹앱 버전으로 업데이트해야 합니다.
- 웹앱에서 들어오는 모든 요청에 대해 SSL 인증서를 요청해야 합니다.

관련 링크:

- [Azure 보안 벤치마크에 대한 자세한 정보](../security/benchmarks/introduction.md)
- [Azure API 앱에 대한 자세한 정보](../app-service/app-service-web-tutorial-rest-api.md)
- [Azure 함수 앱에 대한 자세한 정보](../azure-functions/functions-overview.md)
- [Azure 웹앱에 대한 자세한 정보](../app-service/overview.md)
- [Azure Database for MariaDB에 대한 자세한 정보](../mariadb/overview.md)
- [Azure Database for MySQL에 대한 자세한 정보](../mysql/overview.md)
- [Azure Database for PostgreSQL에 대한 자세한 정보](../postgresql/overview.md)

## <a name="next-steps"></a>다음 단계

제품에 대한 모든 최근 변경 내용은 [Azure Security Center의 새로운 기능](release-notes.md)을 참조하세요.