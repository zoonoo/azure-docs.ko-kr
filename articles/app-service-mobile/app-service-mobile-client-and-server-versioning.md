<properties
  pageTitle="모바일 앱 및 모바일 서비스에서 클라이언트 및 서버 SDK 버전 관리 | Azure 앱 서비스"
  description="모바일 서비스와 Azure 모바일 앱에 대한 클라이언트 SDK의 목록 및 서버 SDK 버전과 호환성"
  services="app-service\mobile"
  documentationCenter=""
  authors="lindydonna" 
  manager="dwrede"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="12/15/2015"
  ms.author="donnam"/>

# 모바일 앱 및 모바일 서비스에서 클라이언트 및 서버 버전 관리

Azure 모바일 서비스의 최신 버전은 Azure 앱 서비스의 **모바일 앱** 기능입니다.

<!-- Azure App Service offers a number of platform benefits over Mobile Services, including continuous integration and deployment, staging lots, and VNET support.
 -->

모바일 앱 클라이언트 및 서버 SDK는 원래 모바일 서비스를 기반으로 하지만 서로 호환되지 *않습니다*. 즉, *모바일 앱* 서버 SDK 및 마찬가지로 *모바일 서비스*를 사용하는 *모바일 앱* 클라이언트 SDK를 사용해야 합니다. 이 계약은 클라이언트 및 서버 SDK인 `ZUMO-API-VERSION`에서 사용하는 특별한 헤더 값을 통해 적용됩니다.

참고: 이 문서가 *모바일 서비스* 백 엔드를 참조할 때마다 반드시 모바일 서비스에서 호스팅해야 할 필요는 없습니다. 이제 코드를 변경하지 않고 앱 서비스에서 실행되도록 모바일 서비스를 마이그레이션할 수 있지만 서비스는 *모바일 서비스* SDK 버전을 사용합니다.

코드 변경 없이 앱 서비스에 마이그레이션하는 방법을 자세히 알아보려면 [Azure 앱 서비스에 모바일 서비스 마이그레이션] 문서를 참조하세요.

## 헤더 사양

키 `ZUMO-API-VERSION`는 HTTP 헤더 또는 쿼리 문자열에 지정될 수 있습니다. 값은 **x.y.z **형식의 버전 문자열입니다.

예:

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## 버전 확인 건너뛰기

앱 설정 **MS\_SkipVersionCheck**에 대한 **true** 값을 설정하여 버전 확인을 건너뛸 수 있습니다. Web.config 또는 Azure 포털의 응용 프로그램 설정 섹션에서 이를 지정합니다.

> [AZURE.NOTE]오프라인 동기화, 인증 및 푸시 알림 영역에서 특히 모바일 서비스와 모바일 앱 간의 많은 동작 변경 사항이 있습니다. 이러한 동작 변경이 앱의 기능을 중단하지 않도록 테스트를 완료한 후에 버전 확인을 건너뛰어야 합니다.

## 모든 버전에 대한 호환성 요약

아래 차트에서는 모든 클라이언트 및 서버 형식 간의 호환성을 보여줍니다. 백 엔드는 사용하는 서버 SDK에 기반하여 모바일 **서비스** 또는 모바일 **앱**으로 구분됩니다.

| | **모바일 서비스** Node.js 또는 .NET | **모바일 앱** Node.js 또는 .NET |
| ----------                | -----------------------             |   ----------------              |
| [모바일 서비스 클라이언트] | 확인 | 오류* |
| [모바일 앱 클라이언트] | 오류* | 확인 |

***MS\_SkipVersionCheck**를 지정하여 제어될 수 있습니다.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>모바일 서비스 클라이언트 및 서버

아래 테이블의 클라이언트 SDK는 **모바일 서비스**와 호환됩니다.

참고: 모바일 서비스 클라이언트 SDK는 `ZUMO-API-VERSION`에 헤더 값을 보내지 *않습니다*. 서비스가 헤더 또는 쿼리 문자열 값을 수신하는 경우 위에서 설명한 대로 명시적으로 건너뛰지 않으면 오류가 반환됩니다.

### <a name="MobileServicesClients"></a> 모바일 *서비스* 클라이언트 SDK

| 클라이언트 플랫폼 | 버전 | 버전 헤더 값 |
| -------------------               | ------------------------                                                  | -------------------  |
| 관리된 클라이언트(Windows, Xamarin) | [1\.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | 해당 없음 |
| iOS | [2\.2.2](http://aka.ms/gc6fex) | 해당 없음 |
| Android | [2\.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) | 해당 없음 |
| HTML | [1\.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | 해당 없음 |

### 모바일 *서비스* 서버 SDK

| 서버 플랫폼 | 버전                                                                                                       | 수락된 버전 헤더 |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* 버전 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **버전 헤더 없음 ** |
| Node.js           | (출시 예정)                        | **버전 헤더 없음** |

<!-- TODO: add Node npm version -->

### 모바일 서비스 백 엔드의 동작

| ZUMO-API-VERSION | MS\_SkipVersionCheck의 값 | 응답 |
| ---------------- | ---------------------------- | -------- |
| 지정되지 않음 | 모두 | 200 - 확인 |
| 어떤 값 | True | 200 - 확인 |
| 어떤 값 | False/지정되지 않음 | 400 - 잘못된 요청 | 

## <a name="2.0.0"></a>Azure 모바일 앱 클라이언트 및 서버

### <a name="MobileAppsClients"></a> 모바일 *앱* 클라이언트 SDK

버전 확인은 **Azure 모바일 앱**에 대한 클라이언트 SDK의 다음 버전부터 도입됩니다.

| 클라이언트 플랫폼 | 버전 | 버전 헤더 값 |
| -------------------               | ------------------------  | -----------------    |
| 관리된 클라이언트(Windows, Xamarin) | [2\.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2\.0.0 |
| iOS | [3\.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2\.0.0 |
| Android | [3\.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3\.0.0 |

<!-- TODO: add HTML version when released -->

### 모바일 *앱* 서버 SDK

버전 검사는 다음 서버 SDK 버전에 포함됩니다.

| 서버 플랫폼 | SDK) | 수락된 버전 헤더 |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2\.0.0 |
| Node.js | [azure-mobile-apps 버전 1.0-beta1 (또는 이상)](https://www.npmjs.com/package/azure-mobile-apps) | 2\.0.0 |

### 모바일 앱 백 엔드의 동작

| ZUMO-API-VERSION | MS\_SkipVersionCheck의 값 | 응답 |
| ---------------- | ---------------------------- | -------- |
| x.y.z 또는 Null | True | 200 - 확인 |
| Null | False/지정되지 않음 | 400 - 잘못된 요청 |
| 1\.x.y | False/지정되지 않음 | 400 - 잘못된 요청 |
| 2\.0.0-2.x.y | False/지정되지 않음 | 200 - 확인 |
| 3\.0.0-3.x.y | False/지정되지 않음 | 400 - 잘못된 요청 |


## 다음 단계

- [모바일 서비스를 Azure 앱 서비스로 마이그레이션]


[모바일 서비스 클라이언트]: #MobileServicesClients
[모바일 앱 클라이언트]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Azure 앱 서비스에 모바일 서비스 마이그레이션]: app-service-mobile-migrating-from-mobile-services.md
[모바일 서비스를 Azure 앱 서비스로 마이그레이션]: app-service-mobile-migrating-from-mobile-services.md

<!----HONumber=AcomDC_1217_2015-->