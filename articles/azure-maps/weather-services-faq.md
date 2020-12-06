---
title: Microsoft Azure Maps 날씨 서비스 FAQ (질문과 대답)
description: Azure Maps 날씨 서비스 데이터 및 기능에 대 한 일반적인 질문에 대 한 답변을 찾습니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/04/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2a5a58c1515c647bb76bf35f3a5eaade3d00588a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747330"
---
# <a name="azure-maps-weather-services-frequently-asked-questions-faq"></a>Azure Maps 날씨 서비스 FAQ (질문과 대답)

이 문서에서는 [Azure Maps 날씨 서비스](https://docs.microsoft.com/rest/api/maps/weather) 데이터 및 기능에 대 한 일반적인 질문에 답변 합니다. 이 섹션에서 다루는 항목은 다음과 같습니다.

* 데이터 원본 및 데이터 모델
* 날씨 서비스 범위 및 가용성
* 데이터 업데이트 빈도
* Azure Maps Sdk를 사용 하 여 개발
* Microsoft Power BI 통합을 포함 하 여 날씨 데이터를 시각화 하는 옵션

## <a name="data-sources-and-data-models"></a>데이터 원본 및 데이터 모델

**Azure Maps 원본 날씨 데이터는 어떻게 되나요?**

Azure Maps은 기본 날씨 데이터를 제공 하는 AccuWeather를 비롯 한 세계 최고 수준의 이동성 및 위치 기술 파트너의 공동 작업으로 빌드됩니다. AccuWeather를 사용 하는 Azure Maps의 공동 작업에 대 한 공지를 읽으려면 [Azure Maps 날씨 서비스는 엔터프라이즈에 대 한 정보를 가져옵니다](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather에는 전 세계 어디에서 나 사용할 수 있는 실시간 날씨 및 환경 정보가 있습니다. 이러한 정보는 대부분의 국내 정부 기관 및 기타 독점 정렬과의 파트너 관계 때문입니다. 이 기본 정보 목록은 아래에 나와 있습니다.

* 정부 기관에서 공개적으로 사용할 수 있는 글로벌 노출 관찰
* 정부 및 개인 회사의 소유 surface 관찰 데이터 집합
* 40 개 이상의 국가/지역에 대 한 고해상도 방사형 데이터
* 최고 수준의 실시간 글로벌 라이트닝 데이터
* 60 국가/지역 및 지역에 대 한 정부에서 발급 된 날씨 경고
* 전체 세계를 덮는 geostationary 된 날씨 위성의 위성 데이터
* 전세계 GFS (Global 예측 시스템)와 같은 정부 모델 및 사설 회사에서 제공 하는 고유한 다운 모델을 포함 하 여 150 개 이상의 숫자 예측 모델
* 공기 품질 관찰
* 교통 부서에서 관찰

다른 데이터와 함께 수십 개의 surface 관측값은 사용자에 게 제공 되는 현재 조건을 만들고 영향을 주기 위해 통합 됩니다. 여기에는 무료로 사용할 수 있는 표준 데이터 집합 뿐만 아니라 인도, 브라질, 캐나다 및 기타 독점 입력을 포함 하 여 여러 국가/지역의 국가 meteorological 서비스에서 얻은 고유한 관찰도 포함 됩니다. 이러한 고유 데이터 집합은 사용자에 대 한 현재 조건 데이터의 공간 및 임시 해상도를 늘립니다. 

이러한 데이터 집합은 AccuWeather의 독점 인공 지능 알고리즘을 활용 하 여 예측을 지속적으로 수정 하 고, 항상 최신 데이터를 통합 하 여 지속적인 정확성을 극대화 하는 디지털 예측 시스템의 정확도를 실시간으로 검토 합니다.

**날씨 예측 데이터를 만드는 모델은 무엇 인가요?**

수많은 날씨 예측 지침 시스템을 활용 하 여 글로벌 예측을 작성 합니다. 150 초과 숫자 예측 모델은 매일 외부 및 내부 데이터 집합 모두에 사용 됩니다. 여기에는 유럽 센터 ECMWF 및 미국 GFS (Global 예측 시스템)와 같은 정부 모델이 포함 됩니다. 또한 AccuWeather는 특정 위치 및 전략적 지역 도메인에 대 한 확장 예측을 다운 하는 소유 고해상도 모델을 통합 하 여 더 정확한 정확성으로 일기 예보를 예측 합니다. 몇 년간의 고유한 혼합 및 가중치 알고리즘이 지난 몇 년간 개발 되었습니다. 이러한 알고리즘은 매우 정확한 예측을 제공 하기 위해 다양 한 예측 입력을 최적으로 활용 합니다.

## <a name="weather-services-coverage-and-availability"></a>날씨 서비스 범위 및 가용성

**다른 국가/지역에 대해 어떤 종류의 적용을 받을 수 있나요?**

날씨 서비스 범위는 국가/지역에 따라 다릅니다. 모든 기능을 모든 국가/지역에서 사용할 수 있는 것은 아닙니다. 자세한 내용은 [검사 설명서](https://docs.microsoft.com/azure/azure-maps/weather-coverage)를 참조 하세요.

## <a name="data-update-frequency"></a>데이터 업데이트 빈도

**현재 상태 데이터는 얼마나 자주 업데이트 되나요?**

현재 상태 데이터는 약 한 시간에 한 번 이상 업데이트 되지만 많은 온도 변화, 하늘 조건 변경, 강 변경 등의 신속한 변경 조건으로 더 자주 업데이트할 수 있습니다. 전 세계 대부분의 관찰 된 스테이션은 조건이 변경 됨에 따라 시간당 많은 시간을 보고 합니다. 그러나 일부 영역은 예약 된 간격으로 한 번, 두 번 또는 네 번만 업데이트 됩니다.  

Azure Maps는 현재 상태 데이터를 최대 10 분 동안 캐시 하 여 발생 하는 데이터의 거의 실시간 업데이트 빈도를 캡처합니다. 캐시 된 응답이 만료 되는 시점을 확인 하 고 오래 된 데이터를 표시 하지 않으려면 Azure Maps API 응답의 HTTP 헤더에서 Expires 헤더 정보를 활용할 수 있습니다.

**매일 및 시간별 예측 데이터는 얼마나 자주 업데이트 되나요?**

업데이트 된 관찰이 수신 됨에 따라 매일 및 매시간 예측 데이터가 매일 여러 번 업데이트 됩니다.  예를 들어 예측 된 높은/낮은 온도가 초과 면 다음 업데이트 주기에서 예측 데이터가 조정 됩니다. 이는 서로 다른 간격으로 수행 될 수 있지만 일반적으로 1 시간 이내에 발생 합니다. 갑작스러운 많은 날씨 조건으로 인해 예측 데이터 변경이 발생할 수 있습니다. 예를 들어, 핫 여름 오후에서 격리 된 뇌우은 과도 하 게 클라우드 범위와 과도 하 게 적용 될 수 있습니다. 격리 된 스톰은 최대 10도 만큼 온도를 효과적으로 삭제할 수 있습니다. 이 새로운 온도 값은 하루 중 남은 시간에 대 한 시간별 및 매일 예측에 영향을 주므로 데이터 집합에서 업데이트 됩니다.

Azure Maps 예측 Api는 최대 30 분 동안 캐시 됩니다. 캐시 된 응답이 만료 되는 시점을 확인 하 고 오래 된 데이터를 표시 하지 않으려면 Azure Maps API 응답의 HTTP 헤더에서 Expires 헤더 정보를 활용할 수 있습니다. 필요한 경우 특정 제품 사용 사례 및 UI (사용자 인터페이스)에 따라 업데이트 하는 것이 좋습니다.

## <a name="developing-with-azure-maps-sdks"></a>Azure Maps Sdk를 사용 하 여 개발

**웹 SDK Azure Maps는 기본적으로 날씨 서비스 통합을 지원 하나요?**

Azure Maps 웹 SDK는 서비스 모듈을 제공 합니다. 서비스 모듈은 웹 또는 Node.js 응용 프로그램에서 Azure Maps REST 서비스를 쉽게 사용할 수 있도록 하는 도우미 라이브러리입니다. JavaScript 또는 TypeScript를 사용 합니다. 시작 하려면 [설명서](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)를 참조 하세요.

**Azure Maps는 기본적으로 날씨 서비스 통합을 지원 Android SDK 하나요?**

Azure Maps Android Sdk는 x/y/zoom 표기법, 쿼드 키 표기법 또는 EPSG 3857 경계 상자 표기법을 사용할 수 있는 Mercator 타일 계층을 지원 합니다.

Java/Android 용 서비스 모듈은 웹 SDK 모듈과 유사 하 게 만들 예정입니다. Android 서비스 모듈을 통해 Java 또는 Android 앱의 모든 Azure Maps 서비스에 쉽게 액세스할 수 있습니다.  

## <a name="data-visualizations"></a>데이터 시각화  

**날씨 타일 Azure Maps 시각적 지원 Power BI Azure Maps 합니까?**

예. 레이더 및 적외선 위성 타일을 Microsoft Power BI 시각적 개체에 마이그레이션하는 방법을 알아보려면 [Power BI 시각적 개체에 타일 계층 추가](https://docs.microsoft.com/azure/azure-maps/power-bi-visual-add-tile-layer)를 참조 하세요. 

**방사형 및 위성 타일에 사용 되는 색을 해석 어떻게 할까요??**

Azure Maps [날씨 개념 문서](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#radar-and-satellite-imagery-color-scale) 에는 방사형 및 위성 타일에 사용 된 색을 해석 하는 데 도움이 되는 가이드가 포함 되어 있습니다. 이 문서에서는 색 샘플과 16 진수 색 코드에 대해 설명 합니다.
 
**방사형 및 위성 타일 애니메이션을 만들 수 있나요?**

예. Azure Maps 고객은 실시간 레이더와 위성 타일 외에도 이전 및 이후 타일을 요청 하 여 지도 오버레이를 통해 데이터 시각화를 향상 시킬 수 있습니다. 이 작업은 [Get Map Tile V2 API](https://aka.ms/AzureMapsWeatherTiles ) 를 직접 호출 하거나 AZURE MAPS 웹 SDK를 통해 타일을 요청 하 여 수행할 수 있습니다. 레이더 타일은 과거 최대 1.5 시간 동안, 그리고 나중에 최대 2 시간 동안 제공 됩니다. 및 타일은 5 분 간격으로 사용할 수 있습니다. 적외선 타일은 지난 3 시간 동안 제공 되며 10 분 간격으로 사용할 수 있습니다. 자세한 내용은 오픈 소스 날씨 타일 애니메이션 [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)을 참조 하세요.  

**다른 날씨 조건에 대 한 아이콘을 제공 하나요?**

예. [여기](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#weather-icons)에서 아이콘 및 해당 코드를 찾을 수 있습니다. [현재 조건 가져오기 API](https://aka.ms/azuremapsweathercurrentconditions)와 같은 일부 날씨 서비스 api만 응답의 *iconcode* 를 반환 합니다. 자세한 내용은 현재 WeatherConditions 오픈 소스 [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 FAQ에서 질문에 대답할 수 없는 경우 다음 채널을 통해 연락할 수 있습니다 (순서 대로).

* 이 문서의 의견 섹션입니다.
* [MSFT Q&Azure Maps 페이지](https://docs.microsoft.com/answers/topics/azure-maps.html)입니다.
* Microsoft 지원 새 지원 요청을 만들려면 [Azure Portal](https://portal.azure.com/)의 도움말 탭에서 **도움말 +** 지원 단추를 선택한 다음 **새 지원 요청** 을 선택 합니다.
* [UserVoice를 Azure Maps](https://feedback.azure.com/forums/909172-azure-maps) 하 여 기능 요청을 제출 합니다.

Azure Maps 날씨 서비스를 사용 하 여 실시간 및 예측 날씨 데이터를 요청 하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [실시간 날씨 데이터 요청 ](how-to-request-weather-data.md)

Azure Maps 날씨 서비스 개념 문서:
> [!div class="nextstepaction"]
> [날씨 서비스 개념](weather-coverage.md)

Azure Maps 날씨 서비스 API 설명서를 탐색 합니다.

> [!div class="nextstepaction"]
> [Azure Maps 날씨 서비스](/rest/api/maps/weather)
