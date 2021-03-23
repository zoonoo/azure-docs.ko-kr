---
title: Azure Maps 웹 SDK 모범 사례 | Microsoft Azure 맵
description: Azure Maps 웹 SDK 사용을 최적화 하기 위한 팁 & 트릭에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878347"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Azure Maps 웹 SDK 모범 사례

이 문서에서는 Azure Maps 웹 SDK에 대 한 모범 사례에 중점을 둔 반면, 설명 된 대부분의 모범 사례와 최적화는 다른 모든 Azure Maps Sdk에 적용 될 수 있습니다.

Azure Maps 웹 SDK는 다양 한 방식으로 많은 공간 데이터 집합을 렌더링 하기 위한 강력한 캔버스를 제공 합니다. 경우에 따라 데이터를 동일한 방식으로 렌더링 하는 여러 방법이 있지만 데이터 집합의 크기와 원하는 기능에 따라 한 방법이 다른 방법 보다 더 나은 성능을 수행할 수 있습니다. 이 문서에서는 성능을 최대화 하 고 원활한 사용자 환경을 만드는 모범 사례와 팁과 요령을 강조 합니다.

일반적으로 지도의 성능을 향상 시킬 때 계층 및 원본의 수를 줄이는 방법 및 사용 되는 데이터 집합 및 렌더링 스타일의 복잡성을 찾습니다.

## <a name="security-basics"></a>보안 기본 사항

응용 프로그램의 가장 중요 한 부분 중 하나는 보안입니다. 응용 프로그램이 안전 하지 않을 경우 해커가 어떤 응용 프로그램을 ruin 수 있나요? Azure Maps 응용 프로그램을 안전 하 게 유지 하기 위한 몇 가지 팁은 다음과 같습니다. Azure를 사용 하는 경우 사용할 수 있는 보안 도구에 대해 숙지 해야 합니다. [Azure 보안에](https://docs.microsoft.com/azure/security/fundamentals/overview)대 한 소개는이 문서를 참조 하세요.

> [!IMPORTANT]
> Azure Maps는 두 가지 인증 방법을 제공 합니다.
> * 구독 키 기반 인증
> * Azure Active Directory 인증은 모든 프로덕션 응용 프로그램에서 Azure Active Directory를 사용 합니다.
> 구독 키 기반 인증은 간단 하며, 대부분의 매핑 플랫폼은 요금 청구를 위해 플랫폼 사용량을 측정 하는 간단한 방법으로 사용 합니다. 그러나이는 보안 된 인증 형식이 아니므로 앱을 개발할 때에만 로컬로 사용 해야 합니다. 일부 플랫폼은 요청에 있는 IP 주소 및/또는 HTTP 참조를 제한 하는 기능을 제공 하지만이 정보는 쉽게 스푸핑 될 수 있습니다. 구독 키를 사용 하는 경우 [정기적으로 회전](how-to-manage-authentication.md#manage-and-rotate-shared-keys)해야 합니다.
> Azure Active Directory는 모든 종류의 응용 프로그램 시나리오에 대 한 다양 한 보안 기능과 설정을 선택할 수 있는 엔터프라이즈 id 서비스입니다. Azure Maps를 사용 하는 모든 프로덕션 응용 프로그램은 인증을 위해 Azure Active Directory를 사용 하는 것이 좋습니다.
> 이 문서에서 [Azure Maps 인증 관리](how-to-manage-authentication.md) 에 대해 자세히 알아보세요.

### <a name="secure-your-private-data"></a>개인 데이터 보호

Azure Maps 대화형 맵 Sdk에 데이터가 추가 되 면 최종 사용자의 장치에서 로컬로 렌더링 되며 어떤 이유로 든 인터넷으로 다시 전송 되지 않습니다.

응용 프로그램에서 공개적으로 액세스할 수 없는 데이터를 로드 하는 경우 데이터를 안전한 위치에 저장 하 고, 안전한 방식으로 액세스 하 고, 응용 프로그램 자체를 잠그고 원하는 사용자만 사용할 수 있도록 해야 합니다. 이러한 단계를 건너뛰는 경우 권한이 없는 사용자가이 데이터에 액세스할 수 있습니다. 이 잠금을 해제 하는 데에는 Azure Active Directory 수 있습니다.

[Azure App Service에서 실행 되는 웹 앱에 인증을 추가 하는 방법](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service) 에 대 한 자습서를 참조 하세요.

### <a name="use-the-latest-versions-of-azure-maps"></a>최신 버전의 Azure Maps 사용

Azure Maps Sdk는 Sdk에서 사용할 수 있는 모든 외부 종속성 라이브러리와 함께 일반 보안 테스트를 진행 합니다. 알려진 보안 문제는 적시에 고정 되어 프로덕션으로 릴리스 됩니다. 응용 프로그램이 호스트 된 버전의 Azure Maps 웹 SDK의 최신 주 버전을 가리키는 경우 보안 관련 픽스를 포함 하는 모든 부 버전 업데이트를 자동으로 받게 됩니다.

NPM 모듈을 통해 Azure Maps 웹 SDK를 자체 호스팅하는 경우 `package.json` 항상 최신 부 버전을 가리키도록 파일의 AZURE MAPS NPM 패키지 버전 번호와 함께 캐럿 (^) 기호를 사용 해야 합니다.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>초기 맵 로드 최적화

웹 페이지를 로드 하는 경우 가장 먼저 수행 해야 할 작업 중 하나는 사용자가 빈 화면에서 바랄 되지 않도록 가능한 한 빨리 렌더링 하기 시작 하는 것입니다.

### <a name="watch-the-maps-ready-event"></a>Maps 준비 이벤트 보기

마찬가지로 맵을 처음 로드 하는 경우에는 가능한 한 빨리 데이터를 로드 하는 것이 좋습니다. 그러면 사용자가 빈 맵을 볼 수 없습니다. 맵은 리소스를 비동기적으로 로드 하므로 사용자 고유의 데이터를 렌더링 하기 전에 맵을 상호 작용할 준비가 될 때까지 기다려야 합니다. `load`이벤트 및 이벤트와 같은 두 가지 이벤트를 기다릴 수 있습니다 `ready` . 로드 이벤트는 map이 초기 지도 보기를 완전히 로드 하 고 모든 맵 타일이 로드 된 후에 발생 합니다. 준비 이벤트는 맵과의 상호 작용을 시작 하는 데 필요한 최소 지도 리소스를 사용 하는 경우에 발생 합니다. 준비 이벤트는 로드 이벤트의 절반 정도를 발생 시킬 수 있으므로 데이터를 더 빨리 맵에 로드 하기 시작할 수 있습니다.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Azure Maps 웹 SDK 지연 로드

맵이 즉시 필요 하지 않은 경우 필요할 때까지 웹 SDK Azure Maps 지연 로드 합니다. 이렇게 하면 필요할 때까지 Azure Maps 웹 SDK에서 사용 하는 JavaScript 및 CSS 파일의 로드가 지연 됩니다. 이 문제가 발생 하는 일반적인 시나리오는 페이지가 로드 될 때 표시 되지 않는 탭 또는 플라이 아웃 패널에서 맵이 로드 되는 경우입니다.
다음 코드 샘플에서는 단추를 누를 때까지 Azure Maps 웹 SDK를 로드 하는 데 걸리는 시간을 지연 하는 방법을 보여 줍니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="지도의 지연 로드" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Pen이 CodePen에서 Azure Maps ()로 <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>지도의 지연 로드</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>맵에 대 한 자리 표시자 추가

네트워크 제한 또는 응용 프로그램 내의 다른 우선 순위로 map을 로드 하는 데 시간이 오래 걸리면 작은 배경 이미지를 지도에 `div` 대 한 자리 표시자로 맵에 추가 하는 것이 좋습니다. 로드 하는 동안 맵의 void가 채워집니다 `div` .

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>초기화 시 초기 지도 스타일 및 카메라 옵션 설정

앱은 종종 특정 위치 또는 스타일에 지도를 로드 하려고 합니다. 경우에 따라 개발자는 map이 로드 될 때까지 기다리거나 이벤트를 기다린 `ready` 다음 `setCemer` 맵의 또는 함수를 사용 합니다 `setStyle` . 이 작업은 원하는 맵 보기에 필요한 리소스를 로드 하기 전에 기본적으로 로드 되기 때문에 원하는 초기 맵 보기에 가져오는 데 더 많은 시간이 걸립니다. 더 나은 방법은 원하는 지도 카메라 및 스타일 옵션을 초기화할 때 지도에 전달 하는 것입니다.

## <a name="optimize-data-sources"></a>데이터 원본 최적화

웹 SDK에는 두 개의 데이터 원본이 있습니다.

* **GeoJSON source**: `DataSource` 클래스 라고 하며, GeoJSON 형식의 원시 위치 데이터를 로컬에서 관리 합니다. 중소 규모의 데이터 집합에 적합 합니다 (수많은 기능 중 매우 높음).
* **Vector tile source**: 클래스에서 알려진 `VectorTileSource` 지도 바둑판식 배열 시스템을 기반으로 현재 지도 보기의 벡터 타일로 형식이 지정 된 데이터를 로드 합니다. 대규모 데이터 집합 (수백만 또는 수십억 개의 기능)에 이상적입니다.

### <a name="use-tile-based-solutions-for-large-datasets"></a>대량 데이터 집합에 대 한 타일 기반 솔루션 사용

수백만 개의 기능을 포함 하는 큰 데이터 집합을 사용 하는 경우 최적의 성능을 얻기 위해 벡터 또는 래스터 이미지 타일 서비스와 같은 서버 쪽 솔루션을 사용 하 여 데이터를 노출 하는 것이 좋습니다.  
벡터 타일은 타일의 포커스 영역에 잘린 기 하 도형을 사용 하 여 뷰의 데이터를 로드 하도록 최적화 되며, 타일의 확대/축소 수준에 대 한 지도의 해상도와 일치 하도록 일반화 됩니다.

[Azure Maps Creator 플랫폼](creator-indoor-maps.md) 은 벡터 타일 형식으로 데이터를 검색 하는 기능을 제공 합니다. 다른 데이터 형식은 [Tippecanoe](https://github.com/mapbox/tippecanoe) 와 같은 도구를 사용 하거나 [이 페이지의 많은 리소스 목록](https://github.com/mapbox/awesome-vector-tiles)중 하나를 사용할 수 있습니다.

서버 쪽에서 데이터 집합을 래스터 이미지 타일로 렌더링 하 고 map SDK의 TileLayer 클래스를 사용 하 여 데이터를 로드 하는 사용자 지정 서비스를 만들 수도 있습니다. 이를 통해 맵은 몇 수십 개의 이미지를 로드 하 고 관리 하기만 하면 되므로 뛰어난 성능을 제공 합니다. 그러나 원시 데이터를 로컬에서 사용할 수 없기 때문에 래스터 타일 사용에 대 한 몇 가지 제한 사항이 있습니다. 보조 서비스는 사용자가 클릭 한 셰이프를 확인 하는 등 모든 유형의 상호 작용 환경을 지 원하는 데 필요한 경우가 많습니다. 또한 래스터 타일의 파일 크기는 일반화 되 고 확대/축소 수준으로 최적화 된 기 하 도형을 포함 하는 압축 된 벡터 타일 보다 큰 경우가 많습니다.

데이터 [원본 만들기](create-data-source-web-sdk.md) 문서에서 데이터 원본에 대해 자세히 알아보세요.

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>여러 데이터 집합을 단일 벡터 타일 원본으로 결합

지도에서 관리 해야 하는 데이터 원본이 적고 표시 되는 모든 기능을 더 빠르게 처리할 수 있습니다. 특히, 타일 소스와 함께 제공 되는 경우 두 벡터 타일 소스를 결합 하 여 타일을 절반으로 검색 하는 HTTP 요청 수를 잘라내고, 파일 헤더가 하나만 있기 때문에 총 데이터 양이 약간 작아집니다.

단일 벡터 타일 원본에서 여러 데이터 집합을 결합 하는 것은 [Tippecanoe](https://github.com/mapbox/tippecanoe)와 같은 도구를 사용 하 여 구현할 수 있습니다. 데이터 집합을 단일 기능 컬렉션으로 결합 하거나 벡터 타일 내에서 원본 계층 이라고 하는 별도의 계층으로 분리할 수 있습니다. 벡터 타일 원본을 렌더링 계층에 연결 하는 경우 계층으로 렌더링 하려는 데이터가 포함 된 원본 계층을 지정 합니다.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>데이터 업데이트로 인 한 캔버스 새로 고침 수 줄이기

클래스의 데이터를 `DataSource` 추가 하거나 업데이트할 수 있는 몇 가지 방법이 있습니다. 성능 향상을 위해 다음과 같은 몇 가지 방법과 몇 가지 고려 사항이 아래에 나와 있습니다.

* 데이터 원본 `add` 함수를 사용 하 여 데이터 원본에 하나 이상의 기능을 추가할 수 있습니다. 이 함수가 호출 될 때마다 지도 캔버스 새로 고침이 트리거됩니다. 많은 기능을 추가 하는 경우 데이터 집합을 반복 하 고 각 기능에 대해이 함수를 호출 하는 대신 배열 또는 기능 컬렉션으로 결합 하 여이 함수에 한 번 전달 합니다.
* 데이터 원본 `setShapes` 함수를 사용 하 여 데이터 원본의 모든 셰이프를 덮어쓸 수 있습니다. 내부적으로 데이터 원본과 함수를 결합 하 `clear` `add` 고 두 가지 대신 단일 지도 캔버스를 새로 고치는 것이 훨씬 더 빠릅니다. 데이터 원본의 모든 데이터를 업데이트 하려는 경우이를 사용 해야 합니다.
* 데이터 원본 `importDataFromUrl` 함수를 사용 하 여 URL을 통해 데이터 원본에 GeoJSON 파일을 로드할 수 있습니다. 데이터가 다운로드 되 면 데이터 원본 함수에 전달 됩니다 `add` . GeoJSON 파일이 다른 도메인에서 호스트 되는 경우 다른 도메인에서 CORs (도메인 간 요청)를 지원 해야 합니다. 도메인의 로컬 파일에 데이터를 복사 하거나 CORs를 사용 하도록 설정 된 프록시 서비스를 만드는 것을 고려 하지 않는 경우 파일이 클 경우 벡터 타일 소스로 변환 하는 것이 좋습니다.
* 기능이 클래스를 사용 하 여 래핑되는 경우 `Shape` `addProperty` `setCoordinates` 셰이프의, 및 `setProperties` 함수는 모두 데이터 소스에서 업데이트를 트리거하고 맵 캔버스를 새로 고칩니다. 데이터 소스 및 함수에서 반환 하는 모든 기능은 `getShapes` `getShapeById` 자동으로 클래스와 함께 래핑됩니다 `Shape` . 여러 셰이프를 업데이트 하려는 경우 데이터 원본 함수를 사용 하 여 JSON으로 변환 하 고 GeoJSON를 `toJson` 편집한 다음이 데이터를 데이터 소스 함수에 전달 하는 것이 더 빠릅니다 `setShapes` .

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>데이터 원본 clear 함수를 불필요 하 게 호출 하지 마십시오.

클래스의 clear 함수를 호출 `DataSource` 하면 지도 캔버스를 새로 고칩니다. `clear`함수가 한 행에서 여러 번 호출 되는 경우 각 새로 고침이 발생할 때까지 맵이 대기 하는 동안 지연이 발생할 수 있습니다.

응용 프로그램에 자주 표시 되는 일반적인 시나리오는 앱이 데이터 원본을 지우고, 새 데이터를 다운로드 하 고, 데이터 원본을 다시 지우고, 데이터 원본에 새 데이터를 추가 하는 경우입니다. 원하는 사용자 환경에 따라 다음과 같은 대안이 더 좋습니다.

* 새 데이터를 다운로드 하기 전에 데이터를 지운 다음 새 데이터를 데이터 원본 `add` 또는 함수에 전달 `setShapes` 합니다. 맵의 유일한 데이터 집합인 경우 새 데이터를 다운로드 하는 동안 맵은 비어 있게 됩니다.
* 새 데이터를 다운로드 한 다음 데이터 소스 함수에 전달 `setShapes` 합니다. 이렇게 하면 맵의 모든 데이터가 바뀝니다.

### <a name="remove-unused-features-and-properties"></a>사용 하지 않는 기능 및 속성 제거

데이터 집합에 앱에서 사용 되지 않는 기능이 포함 되어 있는 경우 제거 합니다. 마찬가지로 필요 없는 기능에 대 한 모든 속성을 제거 합니다. 여기에는 여러 가지 이점이 있습니다.

* 다운로드 해야 하는 데이터의 양을 줄입니다.
* 데이터를 렌더링할 때 반복 해야 하는 기능 수를 줄입니다.
* 는 경우에 따라 데이터 기반 식 및 필터를 단순화 하거나 제거 하 여 렌더링 시 필요한 처리를 줄일 수 있습니다.

기능에 많은 속성이 나 콘텐츠가 있는 경우 데이터 소스에 추가 되는 내용을 렌더링에 필요한 것 만으로 제한 하 고 필요한 경우 추가 속성이 나 콘텐츠를 검색 하기 위한 별도의 메서드나 서비스를 포함 하는 것이 훨씬 더 효율적입니다. 예를 들어 클릭 했을 때 지도에 위치를 표시 하는 간단한 지도를 표시 하는 경우 다양 한 상세 콘텐츠가 표시 됩니다. 데이터 기반 스타일을 사용 하 여 맵에서 위치가 렌더링 되는 방식을 사용자 지정 하려면 데이터 원본에 필요한 속성만 로드 합니다. 자세한 내용을 표시 하려는 경우 기능의 ID를 사용 하 여 추가 콘텐츠를 별도로 검색 합니다. 내용이 서버 쪽에 저장 된 경우 서비스를 사용 하 여 비동기적으로 검색할 수 있습니다. 그러면 맵이 처음 로드 될 때 다운로드 해야 하는 데이터의 양이 크게 줄어듭니다.

또한 기능 좌표의 유효 숫자 수를 줄이면 데이터 크기도 현저 하 게 줄일 수 있습니다. 12 개 이상의 소수 자릿수를 포함 하는 좌표는 일반적이 지 않습니다. 그러나 6 개의 소수 자릿수는 약 0.1 미터의 정확도를 가집니다 .이는 좌표가 나타내는 위치 보다 더 정확 합니다 (실내 빌딩 레이아웃 등의 작은 위치 데이터로 작업할 때 10 진수를 사용 하는 것이 좋습니다.) 소수 자릿수가 여섯 개를 초과 하는 경우 데이터가 렌더링 되는 방식에 차이가 없으며 추가 혜택을 얻기 위해 사용자에 게 더 많은 데이터를 다운로드 하기만 하면 됩니다.

다음은 [GeoJSON 데이터를 사용 하는 데 유용한 도구](https://github.com/tmcw/awesome-geojson)목록입니다.

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>데이터를 신속 하 게 변경 하기 위해 별도의 데이터 원본 사용

스트리밍 데이터의 라이브 업데이트를 표시 하거나 기능에 애니메이션을 적용 하는 등의 작업을 위해 맵에서 데이터를 신속 하 게 업데이트 해야 하는 경우가 있습니다. 데이터 원본이 업데이트 되 면 렌더링 엔진은 데이터 원본의 모든 기능을 반복 하 여 렌더링 합니다. 정적 데이터를 신속 하 게 변경 하는 데이터에서 다른 데이터 원본으로 분리 하면 데이터 원본에 대 한 각 업데이트에서 다시 렌더링 되는 기능 수가 상당히 줄어들고 전반적인 성능이 향상 됩니다.

라이브 데이터와 함께 vector 타일을 사용 하는 경우 업데이트를 지 원하는 손쉬운 방법은 응답 헤더를 사용 하는 것입니다 `expires` . 기본적으로 모든 벡터 타일 원본 또는 래스터 타일 계층은 날짜를 기준으로 타일을 자동으로 다시 로드 합니다 `expires` . 맵의 트래픽 흐름 및 인시던트 타일은이 기능을 사용 하 여 새 실시간 트래픽 데이터가 지도에 표시 되도록 합니다. Maps `refreshExpiredTiles` 서비스 옵션을로 설정 하 여이 기능을 사용 하지 않도록 설정할 수 있습니다 `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>GeoJSON 데이터 원본에서 버퍼 및 허용 오차 옵션 조정

`DataSource`클래스는 즉석 렌더링을 위해 원시 위치 데이터를 벡터 타일 로컬으로 변환 합니다. 이러한 로컬 벡터 타일은 타일 간 부드러운 렌더링을 보장 하기 위해 약간의 버퍼를 사용 하 여 원시 데이터를 타일 영역의 경계에 클리핑 합니다. `buffer`이 옵션의 값이 작을수록 중첩 된 데이터의 수가 로컬 벡터 타일에 저장 되 고 성능이 향상 되지만 발생 하는 렌더링 아티팩트의 변화가 커집니다. 최소 렌더링 아티팩트를 사용 하 여 적절 한 수준의 성능을 얻으려면이 옵션을 조정 하세요.

클래스에는 `DataSource` `tolerance` 렌더링 용도로 기 하 도형 해상도를 낮출 때 Douglas-Peucker 단순화 알고리즘과 함께 사용 되는 옵션도 있습니다. 이 허용 오차 값을 높이면 기 하 도형의 해상도를 줄이고 성능을 향상 시킵니다. 이 옵션을 조정 하 여 데이터 집합에 대 한 기 하 도형 해상도와 성능의 오른쪽 조합을 가져옵니다.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>GeoJSON 데이터 원본에 대 한 최대 확대/축소 옵션 설정

`DataSource`클래스는 즉석 렌더링을 위해 원시 위치 데이터를 벡터 타일 로컬으로 변환 합니다. 기본적으로이 작업은 확대/축소 수준 18까지 확대 하는 경우 확대/축소 수준 18로 생성 된 타일의 데이터를 샘플링 합니다. 이러한 수준에서 확대 하는 경우 높은 해상도를 필요로 하는 대부분의 데이터 집합에 적합 합니다. 그러나 상태 또는도를 볼 때 처럼 더 자세히 볼 수 있는 데이터 집합으로 작업 하는 경우 `minZoom` 데이터 원본의 옵션을와 같은 더 작은 값으로 설정 하면 `12` 계산, 로컬 타일 생성, 데이터 원본에서 사용 하는 메모리 및 성능 향상을 줄일 수 있습니다.

### <a name="minimize-geojson-response"></a>GeoJSON 응답 최소화

서버에서 서비스를 통하거나 플랫 파일을 로드 하 여 GeoJSON 데이터를 로드 하는 경우 필요한 공간 문자를 제거 하도록 데이터를 최소화 해야 합니다.

### <a name="access-raw-geojson-using-a-url"></a>URL을 사용 하 여 원시 GeoJSON 액세스

GeoJSON 개체를 JavaScript 내에 인라인으로 저장할 수도 있지만이 경우에는이 개체에 대해 만든 변수와 별도의 웹 작업자에서 관리 하는 데이터 소스 인스턴스에 저장 되므로 많은 메모리가 사용 됩니다. 대신 URL을 사용 하 여 앱에 GeoJSON를 노출 하면 데이터 소스가 데이터의 단일 복사본을 데이터 원본 웹 작업자에 게 직접 로드 합니다.  

## <a name="optimize-rendering-layers"></a>렌더링 계층 최적화

Azure maps는 지도에서 데이터를 렌더링 하는 다양 한 계층을 제공 합니다. 많은 최적화를 통해 이러한 계층을 시나리오에 맞게 조정 하 여 성능 향상 및 전반적인 사용자 경험을 활용할 수 있습니다.

### <a name="create-layers-once-and-reuse-them"></a>레이어를 한 번 만들고 다시 사용

Azure Maps 웹 SDK는 데이터 기반으로 결정 됩니다. 데이터는 데이터 원본으로 이동 하 여 렌더링 계층에 연결 됩니다. 맵의 데이터를 변경 하려면 데이터 원본의 데이터를 업데이트 하거나 계층의 스타일 옵션을 변경 합니다. 변경 사항이 있을 때마다 레이어를 제거한 다음 다시 만드는 것 보다 일반적으로 훨씬 빠릅니다.

### <a name="consider-bubble-layer-over-symbol-layer"></a>기호 계층 위에 거품형 계층 고려

거품형 계층은 지도에서 점으로 원을 렌더링 하 고 데이터 기반 식을 사용 하 여 해당 반지름과 색의 스타일을 쉽게 지정할 수 있습니다. 원은 WebGL 그릴 수 있는 간단한 모양이 기 때문에 렌더링 엔진은 이미지를 로드 하 고 렌더링 해야 하는 기호 계층 보다 훨씬 빠르게 렌더링할 수 있습니다. 수십 개의 요소를 렌더링 하는 경우 이러한 두 렌더링 계층의 성능 차이가 눈에 띄게 드러납니다.

### <a name="use-html-markers-and-popups-sparingly"></a>HTML 표식 및 팝업 사용 최소화

렌더링에 WebGL를 사용 하는 웹 컨트롤의 대부분의 레이어와 달리 HTML 표식과 팝업 Azure Maps은 렌더링에 기존 DOM 요소를 사용 합니다. 따라서 HTML 표식 및 팝업이 페이지를 추가 하면 더 많은 DOM 요소가 추가 됩니다. 수백 개의 HTML 표식 또는 팝업을 추가 하면 성능이 저하 될 수 있습니다. 더 큰 데이터 집합의 경우 데이터를 클러스터링 하거나 기호 또는 거품형 계층을 사용 하는 것이 좋습니다. 팝업의 경우 일반적인 전략은 아래 예제와 같이 단일 팝업을 만들고 해당 콘텐츠와 위치를 업데이트 하 여 다시 사용 하는 것입니다.

<br/>

<iframe height='500' scrolling='no' title='여러 점을 사용하여 팝업을 다시 사용' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>여러 점을 사용하여 팝업을 다시 사용</a>을 참조하세요.
</iframe>

즉, 지도에서 몇 가지 지점만 렌더링 해야 하는 경우 HTML 표식의 단순성을 선호 하는 것일 수 있습니다. 또한 필요에 따라 HTML 마커를 쉽게 끌 수 있습니다.

### <a name="combine-layers"></a>레이어 결합

지도는 수백 개의 계층을 렌더링할 수 있지만, 계층이 많을 수록 장면을 렌더링 하는 데 걸리는 시간이 길어집니다. 계층 수를 줄이기 위한 한 가지 전략은 비슷한 스타일이 있는 레이어를 결합 하거나 [데이터 기반 스타일](data-driven-style-expressions-web-sdk.md)을 사용 하 여 스타일을 지정할 수 있습니다.

예를 들어 모든 기능에 `isHealthy` 또는 값을 가질 수 있는 속성이 있는 데이터 집합을 생각해 보겠습니다 `true` `false` . 이 속성에 따라 색이 지정 된 다른 거품을 렌더링 하는 거품형 계층을 만드는 경우에는 아래에 나열 된 것과 같은 여러 가지 방법으로 성능이 가장 뛰어난 작업을 수행할 수 있습니다.

* 값을 기준으로 데이터를 두 데이터 원본으로 분할 `isHealthy` 하 고, 각 데이터 원본에 하드 코드 된 색 옵션을 사용 하 여 거품형 계층을 연결 합니다.
* 모든 데이터를 단일 데이터 원본에 넣고 하드 코드 된 색 옵션과 속성을 기반으로 하는 필터를 사용 하 여 두 개의 거품형 계층을 만듭니다 `isHealthy` . 
* 모든 데이터를 단일 데이터 원본에 넣고 `case` 속성에 따라 색 옵션에 대 한 스타일 식이 있는 단일 거품형 계층을 만듭니다 `isHealthy` . 이 방법을 보여주는 코드 샘플을 살펴보세요.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>부드러운 기호 계층 애니메이션 만들기

기호 계층은 기본적으로 충돌 검색을 사용 하도록 설정 되어 있습니다. 이 충돌 검색은 두 기호가 겹치지 않도록 합니다. 기호 레이어의 아이콘 및 텍스트 옵션에는 두 가지 옵션이 있습니다.

* `allowOverlap` -다른 기호와 충돌 하는 경우 기호를 표시할지 여부를 지정 합니다.
* `ignorePlacement` -다른 기호가 기호와 충돌 하도록 허용 되는지 여부를 지정 합니다.

이러한 두 옵션은 기본적으로로 설정 됩니다 `false` . 기호에 애니메이션을 적용할 때 애니메이션의 각 프레임에서 충돌 검색 계산이 실행 되므로 애니메이션 속도를 저하 하 고 유체를 줄일 수 있습니다. 애니메이션을 매끄럽게 하려면 이러한 옵션을로 설정 `true` 합니다.

다음 코드 샘플에서는 기호 계층에 애니메이션 효과를 주는 간단한 방법을 소개 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기호 계층 애니메이션" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>기호 계층 애니메이션</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

### <a name="specify-zoom-level-range"></a>확대/축소 수준 범위 지정

데이터가 다음 조건 중 하나를 충족 하는 경우 렌더링 엔진이 확대/축소 수준 범위 외부에서 건너뛸 수 있도록 계층의 최소 및 최대 확대/축소 수준을 지정 해야 합니다.

* 데이터가 벡터 타일 원본에서 제공 되는 경우 다양 한 데이터 형식에 대 한 소스 계층은 확대/축소 수준을 통해서만 사용할 수 있습니다.
* 모든 확대/축소 수준 0 ~ 24에 대 한 타일이 없는 타일 계층을 사용 하는 경우 타일을 포함 하는 수준 에서만 렌더링 하 고, 누락 된 타일을 다른 확대/축소 수준의 타일로 채우지 않는 것이 좋습니다.
* 특정 확대/축소 수준 에서만 레이어를 렌더링 하려면입니다.
모든 계층에는 `minZoom` `maxZoom` 이 논리에 따라 이러한 확대/축소 수준 사이에서 레이어가 렌더링 되는 및 옵션이 있습니다 ` maxZoom > zoom >= minZoom` .

**예제**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>타일 계층 범위 및 원본 확대/축소 범위 지정

기본적으로 타일 계층은 전 세계 전체에 타일을 로드 합니다. 그러나 타일 서비스에 특정 영역에 대 한 타일이 있는 경우 지도는이 영역 밖에 서 타일을 로드 하려고 시도 합니다. 이 경우 각 타일에 대 한 요청이 수행 되 고 맵에서 다른 요청을 차단할 수 있는 응답을 기다린 후 다른 계층의 렌더링 속도를 낮출 수 있습니다. 타일 계층의 경계를 지정 하면 해당 경계 상자 내에 있는 타일을 요청 하는 맵만 생성 됩니다. 또한 특정 확대/축소 수준 사이에서 타일 계층을 사용할 수 있는 경우에는 같은 이유로 최소 및 최대 원본 확대/축소를 지정 합니다.

**예제**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>기본 지도가 표시 되지 않는 경우 빈 지도 스타일 사용

지도에서 기본 맵을 완전히 포함 하는 레이어가 중첩 되는 경우 지도 스타일을 또는로 설정 하 여 `blank` `blank_accessible` 기본 지도가 렌더링 되지 않도록 하십시오. 이 작업을 수행 하는 일반적인 시나리오는의 전체 지구본 타일에 기본 지도 위에 불투명도 또는 투명 영역이 없는 경우입니다.

### <a name="smoothly-animate-image-or-tile-layers"></a>이미지 또는 타일 계층에 부드러운 애니메이션 효과 주기

지도에서 일련의 이미지 또는 타일 계층을 통해 애니메이션 효과를 주려면 다음을 선택 합니다. 각 애니메이션 프레임에서 단일 계층의 소스를 업데이트 하는 것 보다 각 이미지 또는 타일 계층에 대 한 계층을 만들고 불투명도를 변경 하는 것이 더 빠른 경우가 많습니다. 불투명도를 0으로 설정 하 고 해당 불투명도를 0 보다 큰 값으로 설정 하 여 새 계층을 표시 하 여 계층을 숨기면 계층의 원본을 업데이트 하는 것 보다 훨씬 빠릅니다. 또는 계층의 표시 유형을 전환할 수는 있지만 계층의 페이드 지속 시간은 0으로 설정 해야 합니다. 그렇지 않으면 계층에 애니메이션 효과를 적용 하 게 됩니다. 그러면 새 레이어가 표시 되기 전에 이전 계층이 숨겨져 있었기 때문에 깜박임 효과가 발생 합니다.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>기호 계층 충돌 감지 논리 조정

기호 계층에는 및 라는 아이콘과 텍스트 둘 다에 대해 두 개의 옵션이 있습니다 `allowOverlap` `ignorePlacement` . 이러한 두 옵션은 기호의 아이콘이 나 텍스트를 겹치거나 겹칠 수 있는지 여부를 지정 합니다. 이를로 설정 하면 `false` 기호 계층은 각 점을 렌더링할 때 계산을 수행 하 여 계층의 이미 렌더링 된 다른 기호와 충돌 하는지 확인 하 고, 그렇지 않은 경우 충돌 하는 기호를 렌더링 하지 않습니다. 이는 맵의 혼란을 줄이고 렌더링 되는 개체 수를 줄이는 데 유용 합니다. 이러한 옵션을로 설정 하면 `false` 이 충돌 검색 논리를 건너뛰고 모든 기호가 맵에 렌더링 됩니다. 성능 및 사용자 환경의 최상의 조합을 얻으려면이 옵션을 조정 합니다.

### <a name="cluster-large-point-data-sets"></a>클러스터 large point 데이터 집합

많은 데이터 요소 집합으로 작업 하는 경우 특정 확대/축소 수준에서 렌더링 될 때 대부분의 점이 겹쳐서 표시 되 고 일부만 표시 되는 경우를 발견할 수 있습니다. 클러스터링은 서로 가까이 있는 요소를 그룹화 하 여 단일 클러스터형 점으로 나타내는 프로세스입니다. 사용자가에서 지도를 확대 하면 클러스터는 개별 점으로 분리 됩니다. 이렇게 하면 렌더링 해야 하는 데이터의 양이 크게 줄어들고, 지도를 최소화 하 고, 성능을 향상 시킬 수 있습니다. 클래스에는 `DataSource` 데이터를 로컬로 클러스터링 하는 옵션이 있습니다. 또한 벡터 타일을 생성 하는 많은 도구에는 클러스터링 옵션도 있습니다.

또한 클러스터 반경 크기를 늘려 성능을 향상 시킵니다. 클러스터 반지름이 클수록 클러스터 되지 않는 지점은 추적 및 렌더링을 유지 하는 것입니다.
[클러스터링 지점 데이터 문서](clustering-point-data-web-sdk.md) 에서 자세히 알아보세요.

### <a name="use-weighted-clustered-heat-maps"></a>가중치가 적용 된 클러스터 된 열 지도 사용

열 지도 계층은 수십 개의 데이터 요소를 쉽게 렌더링할 수 있습니다. 더 큰 데이터 집합의 경우 데이터 원본에 대 한 클러스터링을 사용 하도록 설정 하 고 작은 클러스터 radius를 사용 하 고 클러스터 `point_count` 속성을 높이 맵의 가중치로 사용 하는 것이 좋습니다. 클러스터 반경의 크기가 몇 픽셀인 경우 렌더링 된 열 지도에 시각적 차이가 거의 없습니다. 더 큰 클러스터 radius를 사용 하면 성능이 향상 되지만 렌더링 된 열 지도의 해상도가 낮아질 수 있습니다.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

[이 문서의 클러스터링 및 열 지도](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer) 에서 자세히 알아보세요.

### <a name="keep-image-resources-small"></a>이미지 리소스 작게 유지

이미지를 지도 이미지 스프라이트에 추가 하 여 기호 계층의 아이콘을 다각형 계층의 패턴으로 렌더링할 수 있습니다. 다운로드 해야 하는 데이터의 양과 맵 이미지 스프라이트에 걸리는 공간의 크기를 최소화 하기 위해 이러한 이미지를 작게 유지 합니다. 옵션을 사용 하 여 아이콘의 크기를 조정 하는 기호 계층을 사용 하는 경우에는 계획에 표시 되는 `size` 최대 크기인 이미지를 사용 하 고 더 크게 표시 하지 않습니다. 이렇게 하면 사용 하는 리소스를 최소화 하면서 아이콘이 고해상도를 사용 하 여 렌더링 됩니다. 또한 SVG는 단순한 아이콘 이미지에 대해 더 작은 파일 형식으로 사용할 수도 있습니다.

## <a name="optimize-expressions"></a>식 최적화

[데이터 기반 스타일 식은](data-driven-style-expressions-web-sdk.md) 지도에서 데이터를 필터링 하 고 스타일을 지정할 수 있는 다양 한 유연성과 성능을 제공 합니다. 식을 최적화할 수 있는 여러 가지 방법이 있습니다. 다음은 몇 가지 팁입니다.

### <a name="reduce-the-complexity-of-filters"></a>필터의 복잡성 줄이기

필터는 데이터 원본에 있는 모든 데이터에 대해 루프를 실행 하 고 각 필터가 필터의 논리와 일치 하는지 확인 합니다. 필터가 복잡 해지면이로 인해 성능 문제가 발생할 수 있습니다. 이러한 문제를 해결 하기 위한 몇 가지 가능한 전략은 다음과 같습니다.

* 벡터 타일을 사용 하는 경우 데이터를 다른 원본 레이어로 나눕니다.
* 클래스를 사용 하 `DataSource` 는 경우 해당 데이터를 별도의 데이터 원본으로 나눕니다. 데이터 원본 수를 필터의 복잡성으로 조정 합니다. 데이터 소스가 너무 많으면 성능 문제가 발생할 수 있으므로 시나리오에 가장 적합 한 것을 확인 하기 위해 몇 가지 테스트를 수행 해야 할 수도 있습니다.
* 계층에 복잡 한 필터를 사용 하는 경우 필터의 복잡성을 줄이기 위해 스타일 식으로 여러 계층을 사용 하는 것이 좋습니다. 많은 수의 계층으로 스타일 식을 사용할 수 있는 경우 하드 코딩 된 스타일을 사용 하 여 다양 한 계층을 만들지 마세요. 또한 성능 문제를 일으킬 수 있습니다.

### <a name="make-sure-expressions-dont-produce-errors"></a>식이 오류를 생성 하지 않는지 확인 합니다.

식은 렌더링 시 계산 또는 논리적 연산을 수행 하는 코드를 생성 하는 데 종종 사용 됩니다. 응용 프로그램의 나머지 부분에 있는 코드와 마찬가지로 계산과 논리는 의미가 없으며 오류가 발생 하기 쉽습니다. 식에서 오류가 발생 하면 식 계산에 문제가 발생 하 여 성능 및 렌더링 문제를 줄일 수 있습니다.

에 유의 해야 하는 한 가지 일반적인 오류는 모든 기능에 존재 하지 않을 수 있는 기능 속성에 의존 하는 식을 사용 하는 것입니다. 예를 들어 다음 코드에서는 식을 사용 하 여 거품형 계층의 color 속성을 기능의 속성으로 설정 합니다 `myColor` .

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

위의 코드는 데이터 소스의 모든 기능에 `myColor` 속성이 있고 해당 속성의 값이 색 인 경우 제대로 작동 합니다. 데이터 원본의 데이터를 완벽 하 게 제어 하 고 특정 기능에 대해 알고 있으면 속성에 유효한 색이 있는 경우에는 문제가 되지 않을 수 있습니다 `myColor` . 즉,이 코드를 오류 로부터 안전 하 게 만들기 위해 식에 `case` 식을 사용 하 여 `has` 기능에 속성이 있는지 확인할 수 있습니다 `myColor` . 이 경우 `to-color` 형식 식을 사용 하 여 해당 속성의 값을 색으로 변환할 수 있습니다. 색이 잘못 된 경우 대체 (fallback) 색을 사용할 수 있습니다. 다음 코드에서는이 작업을 수행 하 고 대체 색을 녹색으로 설정 하는 방법을 보여 줍니다.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>가장 구체적인 식에서 가장 구체적인 식으로 부울 식 정렬

여러 조건부 테스트를 포함 하는 부울 식을 사용 하는 경우 대부분의 특정에서 가장 구체적인 조건으로 조건부 테스트를 정렬 합니다. 이렇게 하면 첫 번째 조건에서 두 번째 조건이 테스트 되어야 하는 데이터의 양을 줄여야 하므로 수행 해야 하는 조건부 테스트의 총 수를 줄일 수 있습니다.

### <a name="simplify-expressions"></a>식 단순화

식은 강력 하 고 때로는 복잡할 수 있습니다. 식이 단순할수록 계산 속도가 빨라집니다. 예를 들어 간단한 비교가 필요한 경우와 같은 식은와 `['==', ['get', 'category'], 'restaurant']` 같은 일치 식을 사용 하는 것 보다 좋습니다 `['match', ['get', 'category'], 'restaurant', true, false]` . 이 경우 확인할 속성이 부울 값 이면 `get` 식이 훨씬 더 간단해 집니다 `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>웹 SDK 문제 해결

Azure Maps 웹 SDK를 사용 하 여 개발할 때 발생 하는 몇 가지 일반적인 문제를 디버깅 하는 몇 가지 팁은 다음과 같습니다.

**웹 컨트롤을 로드할 때 맵이 표시 되지 않는 이유는 무엇 인가요?**

다음을 수행합니다.

* 추가 된 인증 옵션을 지도에 추가 했는지 확인 합니다. 이를 추가 하지 않으면 맵은 인증 없이 기본 지도 데이터에 액세스할 수 없고 401 오류가 브라우저 개발자 도구의 네트워크 탭에 표시 되기 때문에 빈 캔버스로 로드 됩니다.
* 인터넷에 연결 되어 있는지 확인 합니다.
* 콘솔에서 브라우저의 개발자 도구에 대 한 오류를 확인 합니다. 일부 오류로 인해 맵이 렌더링 되지 않을 수 있습니다. 애플리케이션을 디버그합니다.
* [지원 되는 브라우저](supported-browsers.md)를 사용 하 고 있는지 확인 합니다.

**모든 데이터가 전 세계에 표시 되는 것은 무엇 인가요?**
Azure Maps Sdk에서 위치가 라고도 하는 좌표는의 지리 공간적 산업 표준 형식에 따라 정렬 `[longitude, latitude]` 됩니다. 이와 동일한 형식은 GeoJSON 스키마에서 좌표가 정의 되는 방식 이기도 합니다. Azure Maps Sdk 내에서 사용 되는 형식이 지정 된 핵심 데이터입니다. 데이터가 전 세계의 반대쪽에 표시 되는 경우에는 경도 및 위도 값이 좌표/위치 정보에서 반전 되기 때문일 수 있습니다.

**웹 컨트롤에서 HTML 표식이 잘못 된 위치로 나타나는 이유는 무엇입니까?**

확인할 사항:

* 표식에 대해 사용자 지정 콘텐츠를 사용 하는 경우 `anchor` 및 `pixelOffset` 옵션이 올바른지 확인 합니다. 기본적으로 콘텐츠의 아래쪽 중심은 지도의 위치와 정렬 됩니다.
* Azure Maps에 대 한 CSS 파일이 로드 되었는지 확인 합니다.
* HTML marker DOM 요소를 검사 하 여 응용 프로그램의 CSS가 표식에 추가 되 고 해당 위치에 영향을 주는지 확인 합니다.

**기호 계층의 아이콘이 나 텍스트가 잘못 된 자리에 표시 되는 이유는 무엇 인가요?**
`anchor`및 `offset` 옵션이 맵의 좌표와 정렬 하려는 이미지 또는 텍스트의 부분에 맞게 올바르게 구성 되어 있는지 확인 합니다.
맵이 회전 될 때만 기호가 없으면 옵션을 선택 `rotationAlignment` 합니다. 기본적으로 기호는 사용자에 게 수직으로 나타나도록 지도 뷰포트를 사용 하 여 회전 합니다. 그러나 시나리오에 따라 지도 방향에 기호를 잠그는 것이 바람직 할 수 있습니다. `rotationAlignment` `’map’` 이 작업을 수행 하려면 옵션을로 설정 합니다.
지도가 고주파음/기울어짐 인 경우에만 기호가 없으면 옵션을 선택 `pitchAlignment` 합니다. 기본적으로 지도에는 지도와 같이 지도 뷰포트가 수직으로 유지 됩니다. 그러나 시나리오에 따라 지도의 피치에 기호를 잠그는 것이 좋을 수 있습니다. `pitchAlignment` `’map’` 이 작업을 수행 하려면 옵션을로 설정 합니다.

**지도에 데이터가 나타나지 않는 이유는 무엇 인가요?**

확인할 사항:

* 브라우저의 개발자 도구에서 콘솔에 오류가 있는지 확인 합니다.
* 데이터 원본이 만들어지고 맵에 추가 되었는지, 그리고 데이터 원본이 지도에도 추가 된 렌더링 계층에 연결 되어 있는지 확인 합니다.
* 코드에 중단점을 추가 하 고 코드를 단계별로 실행 하 여 데이터가 데이터 소스에 추가 되 고 데이터 소스와 레이어가 발생 하지 않고 맵에 추가 되 고 있는지 확인 합니다.
* 렌더링 계층에서 데이터 기반 식을 제거해 보세요. 그 중 하나에 문제가 발생 하는 오류가 있을 수 있습니다.

**샌드박스 iframe에서 Azure Maps 웹 SDK를 사용할 수 있나요?**

예. Safari에는 샌드박스 iframe에서 웹 작업자를 실행 하는 것을 방지 하는 [버그가 있습니다](https://bugs.webkit.org/show_bug.cgi?id=170075) .이는 AZURE MAPS 웹 SDK를 요구 합니다. 이 솔루션은 `"allow-same-origin"` iframe의 sandbox 속성에 태그를 추가 하는 것입니다.

## <a name="get-support"></a>지원 받기

다음은 문제에 따라 Azure Maps를 지 원하는 다양 한 방법입니다.

**데이터 문제 또는 주소와 관련 된 문제를 보고 어떻게 할까요??**

Azure Maps에는 데이터 문제를 보고 하 고 추적할 수 있는 데이터 피드백 도구가 있습니다. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) 전송 된 각 문제는 데이터 문제의 진행률을 추적 하는 데 사용할 수 있는 고유한 URL을 생성 합니다. 데이터 문제를 해결 하는 데 걸리는 시간은 문제의 유형과 변경 내용이 올바른지 확인 하는 방법에 따라 달라 집니다. 수정 되 면 렌더링 서비스는 주별 업데이트에 업데이트를 표시 하 고, 지 오 코딩 및 라우팅과 같은 다른 서비스에는 월별 업데이트의 업데이트가 표시 됩니다. 데이터 문제를 보고 하는 방법에 대 한 자세한 지침은이 [문서](how-to-use-feedback-tool.md)에서 제공 합니다.

**서비스 또는 API에서 버그를 보고 어떻게 할까요??**

https://azure.com/support

**Azure Maps에 대 한 기술 도움말은 어디에서 볼 수 있나요?**

Power BI에서 Azure Maps 시각적 개체와 관련 된 경우: https://powerbi.microsoft.com/support/ 다른 모든 Azure Maps 서비스: https://azure.com/support 또는 개발자 포럼: https://docs.microsoft.com/answers/topics/azure-maps.html

**기능 요청을 어떻게 할까요? 하 시겠습니까?**

사용자 의견 사이트에서 기능 요청을 만듭니다. https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>다음 단계

응용 프로그램에서 사용자 환경을 개선 하는 방법에 대 한 자세한 설명은 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [응용 프로그램에 액세스할 수 있도록 설정](map-accessibility.md)

Azure Maps 및 지리 공간적 업계에서 사용 하는 용어에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Maps 용어집](glossary.md)
