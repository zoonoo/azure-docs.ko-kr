---
title: 1 단계-Media Services API V3로 마이그레이션하는 이점 이해 | Microsoft Docs
description: 이 문서에서는 Media Services v 2에서 v3로 마이그레이션하는 이점을 보여 줍니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: c1e3fae35ff249b4435cf2fdcd2bf691bc393d56
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690671"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>1 단계-Media Services API V3로 마이그레이션하는 이점 이해

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-1.svg)

현재 V3 API 에서만 새 기능, 기능 및 성능 최적화를 사용할 수 있기 때문에 이제 Azure Media Services V3 API 버전 2020-05-01을 사용 하 여 이점을 얻을 수 있습니다.

올바른 버전 문자열을 사용 하 여 포털, 최신 Sdk, 최신 CLI 및 REST API에서 API 버전을 변경할 수 있습니다.

V3을 사용 하 여 Media Services에 대 한 상당한 기능이 향상 되었습니다.  

## <a name="benefits-of-media-services-v3"></a>Media Services v3의 혜택

| **V3 기능** | **이점** |
| --- | --- |
| **Azure Portal** | |
| Azure Portal 업데이트 | V3 API 엔터티 관리를 포함 하도록 Azure Portal 업데이트 되었습니다. 이를 통해 고객은 포털을 사용 하 여 라이브 스트리밍을 시작 하 고, V3 변환 작업을 제출 하 고, 콘텐츠 보호 정책을 관리 하 고, API 액세스를 가져오고, 연결 된 저장소 계정을 관리 하 고, 모니터링 작업을 수행할 수 있습니다. |
| **계정 및 저장소** | |
| Azure 역할 기반 access control (RBAC) | 이제 고객은 자신의 역할을 정의 하 고 Media Services ARM API의 각 엔터티에 대 한 액세스를 제어할 수 있습니다. 이렇게 하면 AAD 계정으로 리소스에 대 한 액세스를 제어할 수 있습니다. |
| 관리 ID | 관리 되는 id를 통해 개발자는 Azure AD의 Azure 리소스에 대 한 id를 제공 하 여 자격 증명을 관리할 필요가 없습니다. [여기](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에서 관리 되는 id에 대 한 세부 정보를 참조 하세요. |
| 개인 링크 지원 | 고객은 VNet의 PrivateEndpoint를 통해 키 배달, LiveEvents 및 StreamingEndpoints에 대 한 Media Services 끝점에 액세스 합니다. |
| [고객 관리 되 키](concept-use-customer-managed-keys-byok.md) 또는 byok (사용자 고유 키) 지원 가져오기 | 고객은 Azure Key Vault의 키를 사용 하 여 Media Services 계정의 데이터를 암호화할 수 있습니다. |
| **Assets** | |
| 자산에는 서로 다른 [동적 패키징](dynamic-packaging-overview.md) 및 동적 암호화 설정을 사용 하는 여러 [스트리밍 로케이터가](streaming-locators-concept.md) 있을 수 있습니다. | 각 자산에 대해 허용 되는 스트리밍 로케이터는 100 개로 제한 됩니다. 고객은 자산에 미디어 콘텐츠의 단일 복사본을 저장할 수 있지만 대상 고객을 기반으로 하는 다양 한 스트리밍 정책 또는 콘텐츠 보호 정책으로 다양 한 스트리밍 Url을 공유할 수 있습니다.
| **작업 처리** ||
| V3은 [](transforms-jobs-concept.md)   파일 기반 작업 처리를 위한 변환의 개념을 소개 합니다. | 변환을 사용하여 재사용 가능한 구성을 빌드하고, Azure Resource Manager 템플릿을 만들고, 여러 고객 또는 테넌트 간에 처리 설정을 격리할 수 있습니다. |
| 파일 기반 작업 처리의 경우 HTTP (S) URL을 입력으로 사용할 수 있습니다. | 콘텐츠를 Azure에 이미 저장 하거나 입력 자산을 만들 필요가 없습니다. |
| **라이브 이벤트** ||
| 프리미엄 1080p 라이브 이벤트 | 새 라이브 이벤트 SKU를 사용 하면 고객이 해상도를 1080p까지 출력 하는 클라우드 인코딩을 얻을 수 있습니다. |
| 라이브 이벤트에서 새로운 [짧은 대기 시간](live-event-latency.md) 라이브 스트리밍 지원 | 이 설정을 사용 하도록 설정 하지 않은 경우 사용자는 라이브 이벤트를 실시간에 가깝게 볼 수 있습니다. |
| 라이브 이벤트 미리 보기는 [동적 패키징](dynamic-packaging-overview.md)   및 동적 암호화를 지원 합니다. | 이렇게 하면 미리 보기 및 대시 및 HLS 패키징을 통해 콘텐츠를 보호할 수 있습니다. |
| 라이브 출력 대체 프로그램 | 라이브 출력은 v2 Api에서 프로그램 엔터티 보다 사용 하기 쉽습니다. |
| 더 많은 인코더를 지원 하 여 라이브 이벤트에 대 한 RTMP 수집을 개선 했습니다. | 안정성을 높이고 원본 인코더 유연성을 제공 합니다. |
| 라이브 이벤트는 연중 무휴로 스트리밍할 수 있습니다. | 라이브 이벤트를 호스팅하고 사용자에 게 더 긴 기간을 유지할 수 있습니다. |
| 라이브 이벤트에서 라이브 기록 | 실시간 기록을 통해 고객은 라이브 이벤트 브로드캐스트 중에 음성 언어를 실시간으로 텍스트로 자동으로 높여줄 수 있습니다. 이렇게 하면 라이브 이벤트의 접근성이 크게 향상 됩니다. |
| 라이브 이벤트의 [독립 모드](live-events-outputs-concept.md#standby-mode) | 대기 상태의 라이브 이벤트는 라이브 이벤트를 실행 하는 것 보다 비용이 저렴 합니다. 이를 통해 고객은 실행 중인 라이브 이벤트 집합을 유지 관리 하는 것 보다 저렴 한 비용으로 몇 초 내에 시작할 수 있는 라이브 이벤트 집합을 유지할 수 있습니다. 대기 라이브 이벤트에 대 한 가격 절감은 대부분의 지역에서 2 월 2021 일에 적용 되며 나머지는 4 월 2021 일에 수행 됩니다.
|**콘텐츠 보호** ||
| [콘텐츠 보호](content-key-policy-concept.md)   에서는 다중 키 기능을 지원 합니다. | 이제 고객은 스트리밍 로케이터에서 여러 콘텐츠 암호화 키를 사용할 수 있습니다. |
| **Monitoring** | |
| [Azure EventGrid](reacting-to-media-services-events.md) 알림 지원 | EventGrid 알림은 풍부한 기능입니다. 사용자 응용 프로그램에서 알림을 수신 하기 위한 더 많은 유형의 알림과 더 광범위 한 SDK 지원과 이벤트 처리기로 작동할 수 있는 기존 Azure 서비스가 있습니다. |
| [Azure Portal Azure Monitor 지원 및 통합](monitor-events-portal-how-to.md) | 이를 통해 고객은 Media Services 계정 할당량 사용량, 스트리밍 끝점의 실시간 통계를 시각화 하 고 라이브 이벤트에 대 한 통계를 수집 및 보관할 수 있습니다. 이제 고객은 실시간 메트릭 데이터를 기반으로 경고를 설정 하 고 필요한 작업을 수행할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
