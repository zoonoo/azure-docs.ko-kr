---
title: QnA Maker 서비스 구성-QnA Maker
description: 이 문서에서는 QnA Maker 리소스에 대 한 고급 구성을 간략하게 설명 합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220158"
---
# <a name="configure-qna-maker-resources"></a>QnA Maker 리소스 구성

사용자는 다른 인지 검색 리소스를 사용 하도록 QnA Maker를 구성할 수 있습니다. QnA Maker GA를 사용 하는 경우 App service 설정을 구성할 수도 있습니다.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>다른 Cognitive Search 리소스를 사용 하도록 QnA Maker 구성

포털을 통해 QnA 서비스 및 해당 종속성 (예: 검색)을 만드는 경우 검색 서비스가 만들어지고 QnA Maker 서비스에 연결 됩니다. 이러한 리소스를 만든 후 이전에 기존 검색 서비스를 사용 하도록 App Service 설정을 업데이트 하 고 방금 만든 검색 서비스를 제거할 수 있습니다.

QnA Maker의 **App Service** 리소스는 Cognitive Search 리소스를 사용 합니다. QnA Maker에서 사용 하는 Cognitive Search 리소스를 변경 하려면 Azure Portal에서 설정을 변경 해야 합니다.

1. 사용할 QnA Maker Cognitive Search 리소스의 **관리자 키** 와 **이름을** 가져옵니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 QnA Maker 리소스와 연결 된 **App Service** 를 찾습니다. 둘 다 동일한 이름을 갖습니다.

1. **설정**, **구성** 을 차례로 선택 합니다. 그러면 QnA Maker의 App Service에 대 한 모든 기존 설정이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![App Service 구성 설정을 보여 주는 Azure Portal의 스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 다음 키에 대 한 값을 변경 합니다.

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 새 설정을 사용 하려면 App service를 다시 시작 해야 합니다. **개요** 를 선택한 다음 **다시 시작** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![구성 설정이 변경 된 후 App Service를 다시 시작 하 Azure Portal 스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Azure Resource Manager 템플릿을 통해 QnA 서비스를 만드는 경우 모든 리소스를 만들고 기존 검색 서비스를 사용 하는 App Service 만들기를 제어할 수 있습니다.

App Service [응용 프로그램 설정을](../../../app-service/configure-common.md#configure-app-settings)구성 하는 방법에 대해 자세히 알아보세요.

### <a name="get-the-latest-runtime-updates"></a>최신 런타임 업데이트 가져오기

QnAMaker 런타임은 Azure Portal에서 [QnAMaker 서비스를 만들](./set-up-qnamaker-service-azure.md) 때 배포 되는 Azure App Service 인스턴스의 일부입니다. 런타임은 주기적으로 업데이트됩니다. QnA Maker App Service 인스턴스는 4 월 2019 사이트 확장 릴리스 (버전 5 이상) 이후 자동 업데이트 모드에 있습니다. 이 업데이트는 업그레이드 하는 동안 가동 중지 시간을 0으로 처리 하도록 설계 되었습니다.

에서 현재 버전을 확인할 수 있습니다 https://www.qnamaker.ai/UserSettings . 버전이 버전 5.x 보다 이전 버전인 경우 App Service를 다시 시작 하 여 최신 업데이트를 적용 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스 (리소스 그룹)로 이동 합니다.

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service 인스턴스를 선택 하 고 **개요** 섹션을 엽니다.

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service 인스턴스](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. App Service를 다시 시작 합니다. 업데이트 프로세스는 몇 초 내에 완료 되어야 합니다. 이 QnAMaker 서비스를 사용 하는 모든 종속 응용 프로그램 또는 봇은이 재시작 기간 동안 최종 사용자가 사용할 수 없습니다.

    ![QnAMaker App Service 인스턴스 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>시간 초과를 방지 하도록 App service 유휴 설정 구성

게시 된 기술 자료에 대 한 QnA Maker 예측 런타임에 사용 되는 app service는 유휴 시간 제한 구성을 포함 하며,이는 서비스가 유휴 상태 이면 기본적으로 시간 초과 됩니다. QnA Maker의 경우,이는 트래픽이 없는 기간 후에도 예측 런타임 generateAnswer API가 때때로 시간 초과 되는 것을 의미 합니다.

트래픽이 없는 경우에도 예측 끝점 앱이 로드 되도록 하려면 유휴 상태를 always on으로 설정 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. QnA Maker 리소스의 app service를 검색 하 고 선택 합니다. QnA Maker 리소스와 동일한 이름을 갖지만 다른 **유형의** App Service를 갖게 됩니다.
1. **설정을** 찾은 후 **구성** 을 선택 합니다.
1. 구성 창에서 **일반 설정** 을 선택 하 고 **Always on** 을 찾은 다음 값을 **선택 합니다** .

    > [!div class="mx-imgBorder"]
    > ![구성 창에서 * * 일반 설정 * *을 선택 하 고 * * Always on * *을 찾은 다음 값으로 * *를 선택 합니다.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. **저장** 을 선택 하 여 구성을 저장 합니다.
1. 새 설정을 사용 하도록 앱을 다시 시작할지 묻는 메시지가 표시 됩니다. **계속** 을 선택합니다.

App Service [일반 설정을](../../../app-service/configure-common.md#configure-general-settings)구성 하는 방법에 대해 자세히 알아보세요.

### <a name="business-continuity-with-traffic-manager"></a>Traffic manager를 통한 비즈니스 연속성

비즈니스 연속성 계획의 주요 목표는 Bot 또는 이를 사용하는 애플리케이션에 대한 작동 중지 시간이 없도록 보장하는 복원력 있는 기술 자료 엔드포인트를 만드는 것입니다.

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 계획](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

위에 표시된 것처럼 대략적인 아이디어는 다음과 같습니다.

1. [Azure 쌍을 이루는 지역](../../../best-practices-availability-paired-regions.md)에서 두 개의 병렬 [QnA Maker 서비스](set-up-qnamaker-service-azure.md)를 설정합니다.

1. 기본 QnA Maker App service를 [백업](../../../app-service/manage-backup.md) 하 고 보조 설정에서 [복원](../../../app-service/web-sites-restore.md) 합니다. 이렇게 하면 두 설정이 동일한 호스트 이름 및 키로 작동 합니다.

1. 기본 및 보조 Azure 검색 인덱스를 동기화 된 상태로 유지 합니다. Azure 인덱스를 백업 하는 방법을 보려면 [여기](https://github.com/pchoudhari/QnAMakerBackupRestore) 에서 GitHub 샘플을 사용 하세요.

1. [연속 내보내기](../../../azure-monitor/app/export-telemetry.md)를 사용하여 Application Insights를 백업합니다.

1. 기본 및 보조 스택이 설치되면 [트래픽 관리자](../../../traffic-manager/traffic-manager-overview.md)를 사용하여 두 개의 엔드포인트를 구성하고 라우팅 메서드를 설정합니다.

1. Traffic manager 끝점에 대해 이전에는 TLS (전송 계층 보안), 이전에는 SSL (SSL(Secure Sockets Layer)) 인증서를 만들어야 합니다. App service에서 [TLS/SSL 인증서를 바인딩합니다](../../../app-service/configure-ssl-bindings.md) .

1. 마지막으로, 봇 또는 앱에서 트래픽 관리자 엔드포인트를 사용합니다.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리형(미리 보기 릴리스)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>다른 Cognitive Search 리소스를 사용 하도록 QnA Maker 관리 (미리 보기) 서비스 구성

포털을 통해 관리 되는 QnA 서비스 (미리 보기) 및 해당 종속성 (예: 검색)을 만드는 경우 검색 서비스가 만들어지고 QnA Maker 관리 (미리 보기) 서비스에 연결 됩니다. 이러한 리소스를 만든 후 **구성** 탭에서 검색 서비스를 업데이트할 수 있습니다.

1. Azure Portal에서 QnA Maker 관리 (미리 보기) 서비스로 이동 합니다.

1. **구성** 을 선택 하 고 QnA Maker 관리 (미리 보기) 서비스와 연결 하려는 Azure Cognitive Search 서비스를 선택 합니다.

    ![QnA Maker 관리 (미리 보기) 구성 페이지의 스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. **저장** 을 클릭합니다.

> [!NOTE]
> QnA Maker와 연결 된 Azure Search 서비스를 변경 하면 이미 있는 모든 기술 자료에 대 한 액세스 권한이 손실 됩니다. Azure Search 서비스를 변경 하기 전에 기존 기술 자료를 내보내야 합니다.

---
