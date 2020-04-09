---
title: 비즈니스 연속성 플랜 - QnA Maker
description: 비즈니스 연속성 계획의 주요 목표는 Bot 또는 이를 사용하는 애플리케이션에 대한 작동 중지 시간이 없도록 보장하는 복원력 있는 기술 자료 엔드포인트를 만드는 것입니다.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887074"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>QnA Maker 서비스를 위한 비즈니스 연속성 계획 만들기

비즈니스 연속성 계획의 주요 목표는 Bot 또는 이를 사용하는 애플리케이션에 대한 작동 중지 시간이 없도록 보장하는 복원력 있는 기술 자료 엔드포인트를 만드는 것입니다.

## <a name="business-continuity-with-traffic-manager"></a>트래픽 관리자와의 비즈니스 연속성

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 계획](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

위에 표시된 것처럼 대략적인 아이디어는 다음과 같습니다.

1. [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)에서 두 개의 병렬 [QnA Maker 서비스](set-up-qnamaker-service-azure.md)를 설정합니다.

2. 기본 QnA Maker 앱 서비스를 [백업하고](../../../app-service/manage-backup.md) 보조 설정에서 [복원합니다.](../../../app-service/web-sites-restore.md) 이렇게 하면 두 설정이 동일한 호스트 이름과 키로 작동합니다.

3. 기본 및 보조 Azure 검색 인덱스를 동기화된 상태로 유지합니다. [여기에서](https://github.com/pchoudhari/QnAMakerBackupRestore) GitHub 샘플을 사용하여 Azure 인덱스를 백업 복원하는 방법을 확인합니다.

4. [연속 내보내기](../../../application-insights/app-insights-export-telemetry.md)를 사용하여 Application Insights를 백업합니다.

5. 기본 및 보조 스택이 설치되면 [트래픽 관리자](../../../traffic-manager/traffic-manager-overview.md)를 사용하여 두 개의 엔드포인트를 구성하고 라우팅 메서드를 설정합니다.

6. 트래픽 관리자 끝점에 대한 보안 소켓 계층(SSL)이라고 도던 전송 계층 보안(TLS)을 만들어야 합니다. 앱 서비스에서 [TLS/SSL 인증서를 바인딩합니다.](../../../app-service/configure-ssl-bindings.md)

7. 마지막으로, 봇 또는 앱에서 트래픽 관리자 엔드포인트를 사용합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 선택](./improve-knowledge-base.md)