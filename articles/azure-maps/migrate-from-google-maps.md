---
title: Google Maps에서 마이그레이션 | Microsoft Docs
description: Google Maps에서 Microsoft Azure 맵으로 마이그레이션하는 방법에 대 한 자습서입니다. 지침은 Azure Maps Api 및 Sdk로 전환 하는 방법을 안내 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6709c42b19b18c8cae783a6b4ecc2c0721e1217d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770308"
---
# <a name="migrate-from-google-maps"></a>Google Maps에서 마이그레이션

이 자습서에서는 Google Maps에서 Microsoft Azure Maps 플랫폼으로 웹, 모바일 및 서버 기반 응용 프로그램을 마이그레이션하는 방법에 대 한 정보를 제공 합니다. 이 자습서에는 Azure Maps로 마이그레이션하기 위한 비교 코드 샘플, 마이그레이션 제안 사항 및 모범 사례가 포함 되어 있습니다.

## <a name="azure-maps-platform-overview"></a>Azure Maps 플랫폼 개요

Azure Maps은 정기적으로 업데이트 된 지도 데이터로 압축 하 여 웹 및 모바일 응용 프로그램에 대 한 지리적 컨텍스트를 제공 하는 모든 업계 강력한 지리 공간 기능을 개발자에 게 제공 합니다. Azure Maps에는 지도, 검색, 라우팅, 트래픽, 표준 시간대, 지리적 위치, 지 오 펜싱, 지도 데이터, 날씨, 이동성 및 공간 작업을 위한 REST Api의 Azure One API 집합이 있습니다. 웹 및 Android Sdk를 함께 사용 하 여 개발을 쉽고 유연 하 고 유연 하 게 만들 수 있습니다. 여러 플랫폼에서 이식 가능.

## <a name="high-level-platform-comparison"></a>상위 수준 플랫폼 비교

다음 표에서는 Google 지도 기능의 개략적인 목록과 Azure Maps 기능에 대 한 상대적 지원을 제공 합니다. 이 목록에는 내게 필요한 옵션, 지 오 펜싱 Api, 등시성, 공간 작업, 직접 맵 타일 액세스, 일괄 처리 서비스 및 데이터 검사 비교 (즉, 이미지 적용 범위)와 같은 추가 Azure Maps 기능이 포함 되어 있지 않습니다.

| Google Maps 기능         | 지원 Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| 웹 SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | 계획                                |
| REST 서비스 Api           | ✓                                      |
| 방향 (라우팅)        | ✓                                      |
| 거리 매트릭스             | ✓                                      |
| 상승                   | 계획                                |
| 지 오 코딩 (전진/역방향) | ✓                                      |
| 지리적 위치                 | N/A                                    |
| 배치 검색               | ✓                                      |
| 장소 세부 정보              | 해당 없음 – 웹 사이트 & 사용 가능한 전화 번호 |
| 사진 배치               | N/A                                    |
| 자동 완성 기능          | ✓                                      |
| 정적 맵                 | ✓                                      |
| 정적 주소 보기          | N/A                                    |
| 표준 시간대                   | ✓                                      |
| Maps Embedded API           | N/A                                    |
| 맵 Url                    | N/A                                    |

Google Maps는 기본 키 기반 인증을 제공 합니다. Azure Maps는 기본 키 기반 인증 뿐만 아니라 매우 안전한 Azure Active Directory 인증을 제공 합니다.

## <a name="licensing-considerations"></a>라이선스 고려 사항

Google Maps에서 Azure Maps로 마이그레이션할 때 라이선스와 관련 하 여 다음 사항을 고려해 야 합니다.

- 로드 된 지도 타일의 수에 따라 대화형 지도의 사용에 대 한 요금이 청구 되는 반면 Google 지도에는 지도 컨트롤의 로드에 대 한 요금이 부과 됩니다. Azure Maps 대화형 Azure Maps Sdk에서 맵 타일은 개발자에 대 한 비용을 줄이기 위해 자동으로 캐시 됩니다. 로드 되는 모든 15 개의 지도 타일에 대해 하나의 Azure Maps 트랜잭션이 생성 됩니다. 대화형 Azure Maps Sdk는 512 픽셀 타일을 사용 하 고 평균적으로는 페이지 보기 당 하나 이상의 트랜잭션을 생성 합니다.
- 지도 타일을 사용 하 고 사용자가 지도를 계획 하 고이를 확대/축소 하는 경우가 아니면 맵 부하 당 트랜잭션의 일부만 생성 하는 경우에는 Google Maps 웹 서비스에서 정적 지도 이미지를 Azure Maps Web SDK와 대체 하는 것이 훨씬 더 비용 효율적입니다. Azure Maps 웹 SDK에는 패닝 및 확대/축소를 사용 하지 않도록 설정 하는 옵션이 있습니다. 또한 Azure Maps web SDK는 정적 지도 웹 서비스 보다 훨씬 더 많은 시각화 옵션을 제공 합니다.
- Azure Maps를 사용 하면 플랫폼의 데이터를 Azure에 저장할 수 있습니다. [사용 약관](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)에 따라 최대 6 개월 동안 다른 곳에서 캐시 될 수도 있습니다.

Azure Maps에 대 한 몇 가지 관련 리소스는 다음과 같습니다.

- [가격 책정 페이지 Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps 사용](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) 약관 (Microsoft Online Services 약관에 포함 됨)
- [Azure Maps에서 적절 한 가격 책정 계층을 선택 합니다.](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>제안 된 마이그레이션 계획

다음은 상위 수준 마이그레이션 계획입니다.

1. 응용 프로그램에서 사용 중인 Google Maps Sdk 및 서비스에 대 한 인벤토리를 작성 하 고, Azure Maps에서 마이그레이션할 다른 Sdk 및 서비스를 제공 하는지 확인 합니다.
2. [https://azure.com](https://azure.com)에 Azure 구독 (아직 없는 경우)을 만듭니다.
3. Azure Maps 계정 ([설명서](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys))과 인증 키 또는 Azure Active Directory ([설명서](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication))를 만듭니다.
4. 응용 프로그램 코드를 마이그레이션합니다.
5. 마이그레이션된 응용 프로그램을 테스트 합니다.
6. 마이그레이션된 응용 프로그램을 프로덕션 환경에 배포 합니다.

## <a name="azure-maps-technical-resources"></a>Azure Maps 기술 리소스

Azure Maps에 대 한 유용한 기술 리소스 목록은 다음과 같습니다.

- 개요: [https://azure.com/maps](https://azure.com/maps)
- 설명서: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- 웹 SDK 코드 샘플: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 개발자 포럼: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- 비디오: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- 블로그: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps 피드백 (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>마이그레이션 지원

개발자는 [포럼](https://aka.ms/AzureMapsForums) 을 통해 또는 여러 Azure 지원 옵션 중 하나를 통해 마이그레이션 지원을 검색할 수 있습니다. [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>다음 단계

다음 문서를 사용 하 여 Google Maps 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [웹 앱 마이그레이션](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Android 앱 마이그레이션](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [웹 서비스 마이그레이션](migrate-from-google-maps-web-services.md)
