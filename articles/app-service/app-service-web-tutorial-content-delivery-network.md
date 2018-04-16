---
title: Azure App Service에 CDN 추가 | Microsoft Docs
description: Azure App Service에 CDN(Content Delivery Network) 추가하여 전 세계의 고객에게 가까운 서버에서 정적 파일을 캐시하고 제공합니다.
services: app-service\web
author: syntaxc4
ms.author: cfowler
ms.date: 05/31/2017
ms.topic: tutorial
ms.service: app-service-web
manager: erikre
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: 74344b72869ef6b27f9e7329c7a1777a40662b17
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>자습서: Azure App Service에 CDN(Content Delivery Network) 추가

[Azure CDN(Content Delivery Network)](../cdn/cdn-overview.md)은 전략적으로 배치된 위치에서 정적 웹 콘텐츠를 캐싱하여 사용자에게 콘텐츠를 배달하기 위한 최대 처리량을 제공합니다. 또한 CDN은 웹앱에 대한 서버 부하를 감소시킵니다. 이 자습서에서는 Azure CDN을 [Azure App Service의 웹앱](app-service-web-overview.md)에 추가하는 방법을 보여줍니다. 

사용하게 될 샘플 정적 HTML 사이트의 홈 페이지는 다음과 같습니다.

![샘플 앱 홈 페이지](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

학습할 내용:

> [!div class="checklist"]
> * CDN 끝점 만들기
> * 캐시된 자산 새로 고침
> * 쿼리 문자열을 사용하여 캐시된 버전 제어
> * CDN 끝점에 사용자 지정 도메인 사용

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- [Git 설치](https://git-scm.com/)
- [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>웹앱 만들기

사용할 웹앱을 만들려면 **앱 찾아보기** 단계를 통해 [정적 HTML 빠른 시작](app-service-web-get-started-html.md)을 수행합니다.

### <a name="have-a-custom-domain-ready"></a>사용자 지정 도메인 준비

이 자습서의 사용자 지정 도메인 단계를 완료하려면 사용자 지정 도메인을 소유하고 도메인 공급자(예: GoDaddy)의 DNS 레지스트리에 액세스할 수 있어야 합니다. 예를 들어 `contoso.com` 및 `www.contoso.com`에 대한 DNS 항목을 추가하려면 `contoso.com` 루트 도메인에 대한 DNS 설정을 구성할 수 있는 액세스 권한이 있어야 합니다.

아직 도메인 이름이 없는 경우 Azure Portal을 사용하여 도메인을 구매하려면 [App Service 도메인 자습서](custom-dns-web-site-buydomains-web-app.md)를 수행하는 것이 좋습니다. 

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저를 열고 [Azure Portal](https://portal.azure.com)로 이동합니다.

## <a name="create-a-cdn-profile-and-endpoint"></a>CDN 프로필 및 끝점 만들기

왼쪽 탐색 영역에서 **App Services**를 선택한 다음 [정적 HTML 빠른 시작](app-service-web-get-started-html.md)에서 만든 앱을 선택합니다.

![포털에서 App Service 앱 선택](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

**App Service** 페이지의 **설정** 섹션에서 **네트워킹 > 앱에서 Azure CDN 구성**을 선택합니다.

![포털에서 CDN 선택](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

**Azure Content Delivery Network** 페이지에서 테이블에 지정된 대로 **새 끝점** 설정을 제공합니다.

![포털에서 프로필 및 끝점 만들기](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| 설정 | 제안 값 | 설명 |
| ------- | --------------- | ----------- |
| **CDN 프로필** | myCDNProfile | **새로 만들기**를 선택하여 CDN 프로필을 만듭니다. CDN 프로필은 동일한 가격 책정 계층을 가진 CDN 끝점의 컬렉션입니다. |
| **가격 책정 계층** | Standard Akamai | [가격 책정 계층](../cdn/cdn-overview.md#azure-cdn-features)은 공급자 및 사용 가능한 기능을 지정합니다. 이 자습서에서는 표준 Akamai를 사용합니다. |
| **CDN 끝점 이름** | azureedge.net 도메인에서 고유한 이름 | 도메인인 *\<endpointname>.azureedge.net*에서 캐시된 리소스에 액세스합니다.

**만들기**를 선택합니다.

Azure에서는 프로필 및 끝점을 만듭니다. 새 끝점은 동일한 페이지의 **끝점** 목록에서 표시되고 프로비전될 때 상태가 **실행 중**입니다.

![목록의 새 끝점](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>CDN 끝점 테스트

Verizon 가격 책정 계층을 선택한 경우 끝점 전파를 위해 일반적으로 약 90분이 걸립니다. Akamai의 경우 전파하는 데 몇 분이 걸립니다.

샘플 앱에는 `index.html` 파일 및 다른 정적 자산을 포함하는 *css*, *img* 및 *js* 폴더가 있습니다. 이러한 파일에 대한 콘텐츠 경로는 CDN 끝점과 동일합니다. 예를 들어 다음 URL은 모두 *css* 폴더의 *bootstrap.css* 파일에 액세스합니다.

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

브라우저를 다음 URL로 이동합니다.

```
http://<endpointname>.azureedge.net/index.html
```

![CDN에서 제공되는 샘플 앱 홈 페이지](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 Azure 웹앱에서 이전에 실행한 것과 동일한 페이지가 표시됩니다. Azure CDN에서 원본 웹앱의 자산을 검색하고 CDN 끝점에서 제공하고 있습니다.

이 페이지가 CDN에서 캐시된다는 것을 보장하기 위해 페이지를 새로 고칩니다. 동일한 자산에 대한 두 개의 요청에서 CDN은 요청된 콘텐츠를 캐시해야 합니다.

Azure CDN 프로필 및 끝점을 만드는 방법에 대한 자세한 내용은 [Azure CDN 시작](../cdn/cdn-create-new-endpoint.md)을 참조하세요.

## <a name="purge-the-cdn"></a>CDN 제거

CDN은 TTL(time-to-live) 구성을 기반으로 원본 웹앱의 자원을 주기적으로 새로 고칩니다. 기본 TTL은 7일입니다.

예를 들어 때때로 웹앱에 업데이트된 콘텐츠를 배포할 때 TTL이 만료되기 전에 CDN을 새로 고쳐야 합니다. 새로 고침을 트리거하기 위해 CDN 리소스를 수동으로 제거할 수 있습니다. 

자습서의 이 섹션에서는 웹앱에 대한 변경 내용을 배포하고 CDN을 삭제하여 캐시를 새로 고치기 위해 CDN을 트리거합니다.

### <a name="deploy-a-change-to-the-web-app"></a>웹앱에 대한 변경 내용 배포

다음 예제와 같이 `index.html` 파일을 열고 "-V2"를 H1 제목에 추가합니다. 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

변경 내용을 커밋하고 웹앱에 배포합니다.

```bash
git commit -am "version 2"
git push azure master
```

배포가 완료되면 웹앱 URL을 찾아 변경 사항을 확인합니다.

```
http://<appname>.azurewebsites.net/index.html
```

![웹앱의 제목에서 V2](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

홈 페이지의 CDN 끝점 URL로 이동하더라도 CDN에서 캐시된 버전이 아직 만료되지 않았기 때문에 변경 내용을 표시하지 않습니다. 

```
http://<endpointname>.azureedge.net/index.html
```

![CDN의 제목에서 V2 없음](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>포털에서 CDN 제거

CDN을 트리거하여 캐시된 버전을 업데이트하려면 CDN을 제거합니다.

포털 왼쪽 탐색 영역에서 **리소스 그룹**을 선택한 다음 웹앱(myResourceGroup)에 만든 리소스 그룹을 선택합니다.

![리소스 그룹 선택](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

리소스의 목록에서 CDN 끝점을 선택합니다.

![끝점 선택](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

**끝점** 페이지의 맨 위에서 **제거**를 클릭합니다.

![제거 선택](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

제거하려는 콘텐츠 경로를 입력합니다. 전체 파일 경로를 전달하여 개별 파일을 제거하거나 경로 세그먼트를 전달하여 폴더에서 모든 콘텐츠를 제거하고 새로 고칠 수 있습니다. `index.html`을 변경했으므로 경로 중 하나인지를 확인합니다.

페이지 맨 아래에서 **제거**를 선택합니다.

![페이지 제거](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>CDN이 업데이트되었는지 확인

제거 요청 처리가 완료될 때까지 일반적으로 몇 분 정도를 기다립니다. 현재 상태를 보려면 페이지 맨 아래에 있는 벨 아이콘을 선택합니다. 

![제거 알림](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

`index.html`에 대한 CDN 끝점 URL로 이동하면 이제 홈 페이지에서 제목에 추가한 V2가 표시됩니다. CDN 캐시를 새로 고쳤다는 것을 보여줍니다.

```
http://<endpointname>.azureedge.net/index.html
```

![CDN의 제목에서 V2](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

자세한 내용은 [Azure CDN 끝점 제거](../cdn/cdn-purge-endpoint.md)를 참조하세요. 

## <a name="use-query-strings-to-version-content"></a>버전 콘텐츠에 쿼리 문자열 사용

Azure CDN은 다음과 같은 캐싱 동작 옵션을 제공합니다.

* 쿼리 문자열 무시
* 쿼리 문자열에 대한 캐싱 우회
* 모든 고유한 URL 캐시 

이 중 첫 번째 옵션이 기본값입니다. 즉 액세스하는 URL의 쿼리 문자열에 관계없이 캐시된 자산의 버전이 하나뿐임을 나타냅니다. 

자습서의 이 섹션에서는 캐싱 동작을 변경하여 고유한 URL을 캐시합니다.

### <a name="change-the-cache-behavior"></a>캐시 동작 변경

Azure Portal의 **CDN 끝점** 페이지에서 **캐시**를 선택합니다.

**쿼리 문자열 캐싱 동작** 드롭다운 목록에서 **모든 고유한 URL 캐시**를 선택합니다.

**저장**을 선택합니다.

![쿼리 문자열 캐싱 동작 선택](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>고유 URL을 별도로 캐시했는지 확인

브라우저의 CDN 끝점에서 홈 페이지로 이동하지만 쿼리 문자열을 포함합니다. 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

CDN은 제목에 "V2"를 포함하는 현재 웹앱 콘텐츠를 반환합니다. 

이 페이지가 CDN에서 캐시된다는 것을 보장하기 위해 페이지를 새로 고칩니다. 

`index.html`을 열고 "V2" "V3"으로 바꾸고 변경 내용을 배포합니다. 

```bash
git commit -am "version 3"
git push azure master
```

브라우저에서 `q=2`과 같은 새 쿼리 문자열이 있는 CDN 끝점 URL로 이동합니다. CDN은 현재 `index.html` 파일을 가져오고 "V3"를 표시합니다.  그러나 `q=1` 쿼리 문자열을 사용하여 CDN 끝점으로 이동하는 경우 "V2"를 참조하세요.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![CDN에서 제목에 V3, 쿼리 문자열 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![CDN에서 제목에 V2, 쿼리 문자열 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

이 출력에서는 각 쿼리 문자열이 다르게 처리됨을 보여 줍니다.

* 이전에는 q=1이 사용되어 캐시된 내용(V2)을 반환합니다.
* q=2는 새로운 쿼리 문자열이므로 최신 웹앱 내용(V3)을 검색하여 반환합니다.

자세한 내용은 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](../cdn/cdn-query-string.md)를 참조하세요.

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>CDN 끝점에 사용자 지정 도메인 매핑

CNAME 레코드를 만들어서 CDN 끝점에 사용자 지정 도메인을 매핑합니다. CNAME 레코드는 원본 도메인을 대상 도메인에 매핑하는 DNS 기능입니다. 예를 들어 `cdn.contoso.com` 또는 `static.contoso.com`를 `contoso.azureedge.net`에 매핑할 수 있습니다.

사용자 지정 도메인이 없는 경우 Azure Portal을 사용하여 도메인을 구매하려면 [App Service 도메인 자습서](custom-dns-web-site-buydomains-web-app.md)를 따릅니다. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>CNAME을 사용하여 호스트 이름 찾기

Azure Portal의 **끝점** 페이지에 있는 왼쪽 탐색 영역에서 **개요**를 선택한 다음 페이지 맨 위에 있는 **+ 사용자 지정 도메인** 단추를 선택합니다.

![사용자 지정 도메인 추가 선택](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

**사용자 지정 도메인 추가** 페이지에서 CNAME 레코드를 만드는 데 사용할 끝점 호스트 이름을 표시합니다. 호스트 이름은 CDN 끝점 URL인 **&lt;EndpointName>.azureedge.net**에서 파생됩니다. 

![도메인 페이지 추가](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>도메인 등록 기관과 함께 CNAME 구성

도메인 등록 기관의 웹 사이트로 이동한 다음 DNS 레코드를 만들기 위한 섹션을 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**와 같은 섹션에서 이를 찾을 수 있습니다.

CNAME을 관리하기 위한 섹션을 찾습니다. 고급 설정 페이지로 이동하여 CNAME, 별칭 또는 하위 도메인과 같은 단어를 찾아야 할 수도 있습니다.

선택한 하위 도메인(예: **static** 또는 **cdn**)을 포털에서 이전에 표시한 **끝점 호스트 이름**에 매핑하는 CNAME 레코드를 만듭니다. 

### <a name="enter-the-custom-domain-in-azure"></a>Azure에서 사용자 지정 도메인 입력

**사용자 지정 도메인 추가** 페이지로 돌아가 하위 도메인을 포함하는 사용자 지정 도메인을 대화 상자에 입력합니다. 예를 들어 `cdn.contoso.com`을 입력합니다.   
   
Azure에서 입력한 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다.

CNAME 레코드가 인터넷의 이름 서버로 전파되는 데 시간이 걸릴 수 있습니다. 도메인의 유효성이 바로 검사되지 않으면 몇 분 후에 다시 시도합니다.

### <a name="test-the-custom-domain"></a>사용자 지정 도메인 테스트

브라우저에서 사용자 지정 도메인을 사용하여 `index.html` 파일로 이동(예: `cdn.contoso.com/index.html`)하여 결과가 직접 `<endpointname>azureedge.net/index.html`으로 이동할 경우와 동일한지를 확인합니다.

![사용자 지정 도메인 URL을 사용하는 샘플 앱 홈 페이지](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

자세한 내용은 [사용자 지정 도메인에 Azure CDN 컨텐츠 매핑](../cdn/cdn-map-content-to-custom-domain.md)을 참조하세요.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * CDN 끝점 만들기
> * 캐시된 자산 새로 고침
> * 쿼리 문자열을 사용하여 캐시된 버전 제어
> * CDN 끝점에 사용자 지정 도메인 사용

다음 문서에서 CDN 성능을 최적화하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure CDN에서 파일을 압축하여 성능 향상](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [Azure CDN 끝점에 자산 미리 로드](../cdn/cdn-preload-endpoint.md)
