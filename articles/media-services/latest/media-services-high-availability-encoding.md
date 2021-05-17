---
title: Media Services 주문형 비디오를 통한 고가용성
description: 이 문서는 VOD 애플리케이션의 고가용성을 높이는 데 사용할 수 있는 Azure 서비스에 대한 개요입니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 2b0158c3b1bbee37fdb10c8fc0131be580ad6fc0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562616"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Media Services 및 VOD(주문형 비디오)를 통한 고가용성

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>VOD의 고가용성

Azure 아키텍처 설명서에는 [Geodes](/azure/architecture/patterns/geodes)라는 고가용성 디자인 패턴이 있습니다. 이 설명서에서는 확장성과 복원력을 제공하기 위해 중복 리소스가 서로 다른 지역에 배포되는 방법을 설명합니다.  Azure 서비스를 사용하여 중복성, 상태 모니터링, 부하 분산, 데이터 백업 및 복구와 같은 많은 고가용성 설계 고려 사항을 포괄하는 아키텍처를 만들 수 있습니다.  개별 서비스를 사용하여 VOD 애플리케이션을 위한 고가용성 아키텍처를 만드는 방법 외에도 솔루션에 사용되는 각 서비스에 대한 세부 정보와 함께 이러한 아키텍처가 아래에 설명되어 있습니다.

### <a name="sample"></a>샘플

Media Services 및 VOD(주문형 비디오)를 사용하여 고가용성을 익히는 데 사용할 수 있는 샘플이 있습니다. 또한 VOD 시나리오에 서비스를 사용하는 방법에 대해 자세히 설명합니다.  샘플은 현재 형태로 프로덕션에 사용하기 위한 것이 아닙니다.  프로덕션 애플리케이션에 통합하기 전에 샘플 코드와 추가 정보, 특히 [오류 모드](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)에 대한 섹션을 주의 깊게 검토합니다.  VOD(주문형 비디오)에 대한 고가용성의 프로덕션 구현의 경우 CDN(콘텐츠 배달 네트워크) 전략을 주의 깊게 검토해야 합니다.  [GitHub에서 코드](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)를 확인합니다.

## <a name="overview-of-services"></a>서비스 개요

이 예제 아키텍처에 사용되는 서비스는 다음과 같습니다.

| 아이콘 | Name | Description |
| :--: | ---- | ----------- |
|![Media Services 계정 아이콘입니다.](media/media-services-high-availability-encoding/azure-media-services.svg)| Media Services 계정 | **설명:**<br>Media Services 계정은 Azure에서 미디어 콘텐츠를 관리, 암호화, 인코딩, 분석 및 스트리밍하기 위한 시작점입니다. Azure Storage 계정 리소스와 연결됩니다. 계정과 연결된 모든 스토리지는 동일한 Azure 구독에 포함되어야 합니다.<br><br>**VOD 사용:**<br>비디오 및 오디오 자산을 인코딩하고 제공하는 데 사용하는 서비스입니다.  고가용성을 위해 각기 다른 지역에 2개 이상의 Media Services 계정을 설정합니다. [Azure Media Services에 대해 자세히 알아보세요](media-services-overview.md). |
|![스토리지 계정 아이콘입니다.](media/media-services-high-availability-encoding/storage-account.svg)| 스토리지 계정 | **설명:**<br>Azure Storage 계정에는 BLOB, 파일, 큐, 테이블, 디스크 등의 모든 Azure Storage 데이터 개체가 포함됩니다. 전 세계 어디서나 HTTP 또는 HTTPS를 통해 데이터에 액세스할 수 있습니다.<br><br>각 Media Services 계정의 각 지역에는 동일한 지역에 스토리지 계정이 있습니다.<br><br>**VOD 사용:**<br>VOD 처리 및 스트리밍을 위한 입력 및 출력 데이터를 저장할 수 있습니다. [Azure Storage에 대해 자세히 알아보세요](../../storage/common/storage-introduction.md). |
|![Azure Storage 큐 아이콘입니다.](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure Storage Queue | **설명:**<br>Azure Queue Storage는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다.<br><br>**VOD 사용:**<br>큐를 사용하여 메시지를 보내고 받아서 여러 모듈 간에 작업을 조정할 수 있습니다. 이 샘플에서는 Azure Storage 큐를 사용하지만 Azure는 사용자의 요구에 더 잘 맞는 Service Bus 및 Service Fabric 신뢰할 수 있는 큐 등의 다른 유형의 큐를 제공합니다. [Azure 큐에 대해 자세히 알아보세요](../../storage/queues/storage-queues-introduction.md). |
|![Azure Cosmos DB 아이콘입니다.](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **설명:**<br>Azure Cosmos DB는 전 세계 여러 Azure 지역에서 처리량과 스토리지의 크기를 독립적으로 조정하는 Microsoft의 전 세계에 분산된 다중 모델 데이터베이스 서비스입니다.<br><br>**VOD 사용:**<br>테이블을 사용하여 작업 출력 상태 레코드를 저장하고 각 Media Services 인스턴스의 상태를 추적할 수 있습니다. Media Services API에 대한 각 호출의 상태를 추적/기록할 수도 있습니다. [Azure Cosmos DB에 대해 자세히 알아보세요](../../cosmos-db/introduction.md).  |
|![관리 ID 아이콘입니다.](media/media-services-high-availability-encoding/managed-identity.svg)| 관리 ID | **설명:**<br>관리 ID는 Azure AD에서 자동으로 관리되는 ID를 제공하는 Azure AD의 기능입니다. 코드에 자격 증명을 저장하지 않고 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증하는 데 관리 ID를 사용할 수 있습니다.<br><br>**VOD 사용:**<br>Azure Functions는 관리 ID를 사용하여 Media Services 인스턴스에 인증하여 Key Vault에 연결할 수 있습니다. [관리 ID에 대해 자세히 알아보세요](../../active-directory/managed-identities-azure-resources/overview.md). |
|![Key Vault 아이콘입니다.](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **설명:**<br>Azure Key Vault는 토큰, 암호, 인증서, API 키 및 기타 비밀에 대한 액세스를 안전하게 저장하고 엄격히 제어하는 데 사용할 수 있습니다. 또한 키 관리 솔루션으로 사용할 수 있습니다. Azure Key Vault를 사용하면 데이터를 암호화하는 데 사용되는 암호화 키를 쉽게 만들고 제어할 수 있습니다. Azure Key Vault는 Azure 및 내부 연결 리소스와 함께 사용할 퍼블릭 및 프라이빗 TLS/SSL(Transport Layer Security/Secure Sockets Layer) 인증서를 쉽게 프로비전, 관리 및 배포할 수 있습니다. 비밀과 키는 소프트웨어 또는 FIPS 140-2 Level 2 검증을 받은 HSM을 통해 보호할 수 있습니다.<br><br>**VOD 사용:**<br>Key Vault를 사용하여 애플리케이션의 서비스 주체에 대한 액세스 정책을 설정할 수 있습니다.  스토리지 계정에 연결 문자열을 저장하는 데 사용할 수 있습니다. Key Vault를 사용하여 스토리지 계정과 cosmos db에 대한 연결 문자열을 저장합니다. Key Vault를 사용하여 전체 클러스터 구성을 저장할 수도 있습니다. 각 미디어 서비스 인스턴스에 대해 구독 ID, 리소스 그룹 이름 및 계정 이름을 저장할 수 있습니다. 자세한 내용은 샘플에서 사용되는 방법을 참조하세요. [Key Vault에 대해 자세히 알아보세요](../../key-vault/general/overview.md). |
|![Azure Functions 아이콘입니다.](media/media-services-high-availability-encoding/function-app.svg)| Azure Functions | **설명:**<br>Azure Functions로 애플리케이션 인프라에 대한 걱정 없이 작은 코드 부분("함수"라고 함)을 실행합니다. [Azure Functions에 대해 자세히 알아보세요.](../../azure-functions/functions-overview.md)<br><br>**VOD 사용:**<br>Azure Functions를 사용하여 VOD 애플리케이션의 모듈을 저장하고 호스트할 수 있습니다.  VOD 애플리케이션용 모듈에는 다음이 포함될 수 있습니다.<br><br>**작업 일정 계획 모듈**<br>작업 일정 계획 모듈은 Media Services 클러스터(다른 지역에 있는 두 개 이상의 인스턴스)에 새 작업을 제출하는 데 사용됩니다. 각 Media Services 인스턴스의 상태를 추적하고 다음 정상 인스턴스에 새 작업을 제출합니다.<br><br>**작업 상태 모듈**<br>작업 상태 모듈은 Azure Event Grid 서비스에서 오는 작업 출력 상태 이벤트를 수신합니다. 나머지 모듈에서 Media Services API에 대한 호출 수를 최소화하기 위해 이벤트 저장소에 이벤트를 저장합니다.<br><br>**인스턴스 상태 모듈**<br>이 모듈은 제출된 작업을 추적하고 각 Media Services 인스턴스의 상태를 확인합니다. 완료된 작업, 실패한 작업 및 완료되지 않은 작업을 추적합니다.<br><br>**프로비전 모듈**<br>이 모듈은 프로세스 자산을 프로비전합니다. 모든 Media Services 인스턴스에 자산 데이터를 복사하고 일부 Media Services 인스턴스를 사용할 수 없는 경우에도 자산을 스트리밍할 수 있도록 Azure Front Door 서비스를 설정합니다. 스트리밍 로케이터도 설정합니다.<br><br>**작업 확인 모듈**<br>이 모듈은 제출된 각 작업을 추적하고 실패한 작업을 다시 제출하며 작업이 성공적으로 완료되면 작업 데이터 정리를 수행합니다.  |
|![App Service 아이콘입니다.](media/media-services-high-availability-encoding/application-service.svg)| App Service 및 계획  | **설명:**<br>Azure App Service는 웹 애플리케이션, REST API 및 모바일 백 엔드를 호스트하는 HTTP 기반 서비스입니다. .NET, .NET Core, Java, Ruby, Node.js, PHP 또는 Python을 지원합니다. Windows 및 Linux 기반 환경에서 애플리케이션을 실행하고 확장할 수 있습니다.<br><br>**VOD 사용:**<br>각 모듈은 App Service에서 호스트됩니다. [App Service에 대해 자세히 알아보세요](../../app-service/overview.md). |
|![Azure Front Door 아이콘입니다.](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **설명:**<br>Azure Front Door는 최적의 성능과 고가용성을 지원하는 빠른 글로벌 장애 조치(failover)를 최적으로 구현하여 웹 트래픽의 글로벌 라우팅을 정의, 관리, 모니터링하는 데 사용됩니다.<br><br>**VOD 사용:**<br>Azure Front Door를 사용하여 트래픽을 스트리밍 엔드포인트로 라우팅할 수 있습니다. [Azure Front Door에 대해 자세히 알아보세요](../../frontdoor/front-door-overview.md).  |
|![Azure Event Grid 아이콘입니다.](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure Event Grid | **설명:**<br>이벤트 기반 아키텍처용으로 만들어진 Event Grid는 기본적으로 스토리지 BLOB 및 리소스 그룹과 같은 Azure 서비스의 이벤트를 지원합니다. 또한 사용자 지정 주제 이벤트를 지원합니다. 필터를 사용하여 다른 엔드포인트에 대한 특정 이벤트를 라우팅하고, 여러 엔드포인트로 멀티캐스트하며, 이벤트가 안정적으로 배달되도록 할 수 있습니다. Azure Event Grid는 각 지역의 여러 장애 도메인과 가용성 영역으로 고유하게 분산되어 가용성을 극대화합니다.<br><br>**VOD 사용:**<br>Event Grid로 모든 애플리케이션 이벤트를 추적하고 저장하여 작업 상태를 유지할 수 있습니다. [Azure Event Grid에 대해 자세히 알아보세요](../../event-grid/overview.md). |
|![Application Insights 아이콘입니다.](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **설명:** <br>Azure Monitor의 기능인 Application Insights는 개발자와 DevOps 전문가를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. 라이브 애플리케이션을 모니터링하는 데 사용됩니다. 성능 이상을 감지하고, 문제를 진단하고 사용자가 앱을 사용하여 수행하는 작업을 파악할 수 있는 분석 도구를 포함하고 있습니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.<br><br>**VOD 사용:**<br>모든 로그를 Application Insights로 보낼 수 있습니다. 성공적으로 생성된 작업 메시지를 검색하여 각 작업을 처리한 인스턴스를 확인할 수 있습니다. 여기에는 고유 식별자와 인스턴스 이름 정보를 포함하여 제출된 모든 작업 메타데이터가 포함될 수 있습니다. [Application Insights에 대해 자세히 알아봅니다](../../azure-monitor/app/app-insights-overview.md). |
## <a name="architecture"></a>Architecture

이 개략적인 다이어그램에서는 고가용성과 미디어 서비스를 시작하기 위해 제공되는 샘플의 아키텍처를 보여줍니다.

[ ![VOD(주문형 비디오) 개략적인 아키텍처 다이어그램](media/media-services-high-availability-encoding/high-availability-architecture.svg) ](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>모범 사례

### <a name="regions"></a>영역

* Azure Media Services 계정을 두 개 이상 [만듭니다](./create-account-howto.md). 두 계정은 서로 다른 지역에 있어야 합니다. 자세한 내용은 [Azure Media Services 서비스가 배포된 지역](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)을 참조하세요.
* 작업을 제출하려는 동일한 지역에 미디어를 업로드합니다. 인코딩을 시작하는 방법에 대한 자세한 내용은 [HTTPS URL에서 작업 입력 만들기](./job-input-from-http-how-to.md) 또는 [로컬 파일에서 작업 입력 만들기](./job-input-from-local-file-how-to.md)를 참조하세요.
* 그런 다음, [작업](./transforms-jobs-concept.md)을 다른 지역에 다시 제출해야 하는 경우 `JobInputHttp`를 사용하거나 `Copy-Blob`을 사용하여 원본 자산 컨테이너에서 대체 지역의 자산 컨테이너로 데이터를 복사할 수 있습니다.

### <a name="monitoring"></a>모니터링

* Azure Event Grid를 통해 각 계정에서 `JobStateChange` 메시지를 구독합니다.
    * Azure Portal 또는 CLI를 통해 [이벤트를 등록](./reacting-to-media-services-events.md)합니다. Event Grid Management SDK로도 등록할 수 있습니다.
    * 기본적으로 Media Services 이벤트를 지원하는 [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)를 사용합니다.
    * Azure Functions를 통해 Event Grid 이벤트를 사용할 수도 있습니다.

    자세한 내용은 다음을 참조하세요.

    * 어떤 이유로 Azure Event Grid 메시지가 지연되는 경우 대체 추가를 포함하여 Azure Event Grid로 작업을 모니터링하는 방법을 보여주는 [Audio Analytics 샘플](./transforms-jobs-concept.md)을 참조하세요.
    * [Media Services 이벤트에 대한 Azure Event Grid 스키마](./media-services-event-schemas.md)를 참조하세요.

* [작업](./transforms-jobs-concept.md)을 만들 때는 다음을 수행합니다.
    * 현재 사용 중인 계정 목록에서 임의로 계정을 선택합니다. 이 목록에는 일반적으로 두 계정이 모두 포함되지만 문제가 검색되면 계정 하나만 포함될 수 있습니다. 목록이 비어 있으면 운영자가 조사할 수 있도록 경고를 발생시킵니다.
    * 각 진행 중인 작업과 사용된 지역/계정을 추적하는 레코드를 만듭니다.
* `JobStateChange` 처리기가 작업이 예약된 상태에 도달했다는 알림을 받으면 예약된 상태가 되는 시간과 사용된 지역/계정을 기록합니다.
* `JobStateChange` 처리기가 작업이 처리 상태에 도달했다는 알림을 받으면 해당 작업에 대한 레코드를 처리 중으로 표시하고 처리 상태가 되는 시간을 기록합니다.
* `JobStateChange` 처리기가 작업이 최종 상태(완료/오류 발생/취소됨)에 도달했다는 알림을 받으면 해당 작업에 대한 레코드를 적절하게 표시합니다.
* 작업 레코드를 주기적으로 확인하는 별도의 프로세스를 마련합니다.
    * 지정된 지역에 대해 적절한 시간 내에 처리 중 상태로 진행되지 않은 예약된 상태의 작업이 있는 경우 현재 사용되는 계정 목록에서 해당 지역을 제거합니다. 비즈니스 요구 사항에 따라 해당 작업을 바로 취소하고 다른 지역에 다시 제출하도록 결정할 수 있습니다. 또는 다음 상태로 이동하기 위한 시간을 더 줄 수 있습니다.
    * 계정 목록에서 지역이 제거된 경우 목록에 다시 추가하기 전에 복구를 위해 지역을 모니터링합니다. 일정 기간 후에 계정을 목록에 다시 추가하고 운영자가 Azure Media Services에 영향을 줄 수 있는 중단에 대한 Azure 통신을 모니터링하는 방식으로 지역의 기존 작업을 통해 지역 상태를 모니터링할 수 있습니다(취소하고 다시 제출하지 않은 경우).

## <a name="next-steps"></a>다음 단계

* [코드 샘플](/samples/browse/?products=azure-media-services) 체크 아웃