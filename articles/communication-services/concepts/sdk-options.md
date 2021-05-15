---
title: Azure Communication Services용 SDK 및 REST API
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services SDK 및 REST API에 대해 자세히 알아보세요.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1e5c932460ada6060c77d233b2271813d6ea327b
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108292309"
---
# <a name="sdks-and-rest-apis"></a>SDK 및 REST API

Azure Communication Services 기능은 개념적으로 6개 영역으로 구성됩니다. 대부분의 영역에는 인터넷을 통해 직접 사용할 수 있는 게시된 REST API에 대해 프로그래밍된 완전한 오픈 소스 SDK가 있습니다. 호출하는 SDK는 소유 네트워크 인터페이스를 사용하며 현재 닫혀 있는 소스입니다. SDK에 대한 샘플 및 추가 기술 세부 정보는 [Azure Communication Services GitHub 리포지토리](https://github.com/Azure/communication)에 게시됩니다.

## <a name="rest-apis"></a>REST API
통신 서비스 API는 [docs.microsoft.com](/rest/api/azure/)의 다른 Azure REST API와 함께 설명됩니다. 이 설명서에서는 HTTP 메시지를 구성하는 방법을 설명하고 Postman 사용에 대한 지침을 제공합니다. 이 설명서는 [GitHub](https://github.com/Azure/azure-rest-api-specs)에서 Swagger 형식으로도 제공됩니다.


## <a name="sdks"></a>SDK

| 어셈블리 | 네임스페이스| 프로토콜 | 기능 |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure.ResourceManager.Communication | [REST (영문)](/rest/api/communication/communicationservice)| Communication Services 리소스 프로비전 및 관리|
| 일반 | Azure.Communication.Common| REST | 다른 SDK의 기본 형식을 제공합니다. |
| ID | Azure.Communication.Identity| [REST (영문)](/rest/api/communication/communicationidentity)| 사용자, 액세스 토큰 관리|
| 전화 번호 _(베타)_| Azure.Communication.PhoneNumbers| [REST (영문)](/rest/api/communication/phonenumbers)| 전화 번호 가져오기 및 관리 |
| 채팅 | Azure.Communication.Chat| 소유 신호를 포함하는 [REST](/rest/api/communication/) | 애플리케이션에 실시간 텍스트 기반 채팅 추가 |
| sms| Azure.Communication.SMS | [REST (영문)](/rest/api/communication/sms)| SMS 메시지 보내기 및 받기|
| 호출| Azure.Communication.Calling | 소유 전송 | 음성, 비디오, 화면 공유 및 기타 실시간 데이터 통신 기능 사용 |

Azure Resource Manager, ID 및 SMS SDK는 서비스 통합에 초점을 맞추고 있으며 해당 기능을 최종 사용자 애플리케이션에 통합하는 경우 대부분의 경우 보안 문제가 발생합니다. 공용 및 채팅 SDK는 서비스 및 클라이언트 애플리케이션에 적합합니다. 호출 SDK는 클라이언트 애플리케이션용으로 설계되었습니다. 서비스 시나리오에 초점을 맞춘 SDK를 개발하고 있습니다.


### <a name="languages-and-publishing-locations"></a>언어 및 게시 위치

개별 SDK 패키지의 게시 위치는 아래에 자세히 설명되어 있습니다.

| 영역           | JavaScript | .NET | Python | Java SE | iOS | Android | 기타                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [GitHub를 통해 이동](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 일반         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | 해당 없음      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| ID | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| 전화 번호 | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| 채팅           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| sms            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| 호출        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| 참조 설명서     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.android.communication.calling)            | -                              |


## <a name="rest-api-throttles"></a>REST API 제한
특정 REST API 및 해당 SDK 메서드에는 주의가 필요한 제한이 있습니다. 이 제한을 초과하면 `429 - Too Many Requests` 오류 응답이 트리거됩니다. [Azure 지원에 대한 요청](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 통해 제한을 늘릴 수 있습니다.

| API                                                                                                                          | 제한            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [모든 검색 전화 번호 계획 API](/rest/api/communication/phonenumbers)         | 4개 요청/일      |
| [전화 번호 요금제 구매](/rest/api/communication/phonenumbers/purchasephonenumbers) | 1 한 달 구매  |
| [SMS 보내기](/rest/api/communication/sms/send)                                                       | 200개 요청/분 |


## <a name="sdk-platform-support-details"></a>SDK 플랫폼 지원 세부 정보

### <a name="ios-and-android"></a>iOS 및 Android 

- 통신 서비스 iOS SDK 대상 iOS 버전 13 이상 및 Xcode 11 이상
- Android Java SDK 대상 Android API 레벨 21 이상 및 Android Studio 4.0 이상

### <a name="net"></a>.NET 

호출하는 경우를 제외하고, 통신 서비스 패키지는 아래 나열된 플랫폼을 지원하는 .NET Standard 2.0을 대상으로 합니다.

.NET Framework 4.6.1을 통한 지원
- Windows 10, 8.1, 8 및 7
- Windows Server 2012 R2, 2012 및 2008 R2 SP1

.NET Core 2.0을 통한 지원
- Windows 10(1607+), 7 SP1+, 8.1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12+
- Linux의 여러 버전/배포
- UWP 10.0.16299(RS3) 2017년 9월
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

## <a name="api-stability-expectations"></a>API 안정성 기대 수준

> [!IMPORTANT]
> 이 섹션에서는 **안정적인** 것으로 표시된 REST API 및 SDK에 대한 지침을 제공합니다. 시험판, 미리 보기 또는 베타로 표시된 API는 **통지 없이** 변경되거나 더 이상 사용되지 않을 수 있습니다.

향후에는 Communication Services SDK 버전의 사용을 중지할 수 있으며, REST API와 출시된 SDK에 대한 주요 변경 내용이 도입될 수 있습니다. Azure Communication Services는 *일반적으로* 서비스 버전 사용을 중지하기 위한 두 가지 지원 가능성 정책을 따릅니다.

- Communication Services 인터페이스 변경으로 인해 코드를 변경하기 전에 3년 이상 알림이 표시됩니다. 문서화된 모든 REST API 및 SDK API는 일반적으로 인터페이스의 서비스가 해제되기 전에 3년 이상 경고를 발생시킵니다.
- SDK 어셈블리를 최신 부 버전으로 업데이트하기 전에 적어도 1년 이상 알림이 표시됩니다. 해당 필수 업데이트는 동일한 주 버전에 있기 때문에 코드를 변경할 필요가 없습니다. 보안 및 성능 업데이트가 자주 필요한 실시간 구성 요소를 포함하는 호출 및 채팅 라이브러리의 경우 특히 그렇습니다. 통신 서비스 SDK를 계속 업데이트하는 것이 좋습니다.

### <a name="api-and-sdk-decommissioning-examples"></a>API 및 SDK 서비스 해제 예제

**V24 버전의 SMS REST API를 애플리케이션에 통합했습니다. Azure Communication은 v25를 릴리스합니다.**

해당 API의 작동이 중지되고 v25로 강제로 업데이트되기 전에 3년 동안 경고가 발생합니다. 이 업데이트를 수행하려면 코드를 변경해야 할 수 있습니다.

**호출 SDK의 v2.02 버전을 애플리케이션에 통합했습니다. Azure Communication은 v 2.05를 릴리스합니다.**

v2.05의 릴리스 12개월 안에 호출 SDK의 V2.05 버전으로 업데이트해야 할 수 있습니다. 이 작업은 v2.05가 v2 주 버전에 있고 주요 변경 내용이 없기 때문에 코드를 변경하지 않고 아티팩트를 간단히 대체합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 SDK 개요를 참조하세요.

- [호출 SDK 개요](../concepts/voice-video-calling/calling-sdk-features.md)
- [채팅 SDK 개요](../concepts/chat/sdk-features.md)
- [SMS SDK 개요](../concepts/telephony-sms/sdk-features.md)

Azure Communication Services를 시작하려면 다음을 수행하세요.

- [Azure 통신 리소스 만들기](../quickstarts/create-communication-resource.md)
- [사용자 액세스 토큰](../quickstarts/access-tokens.md) 생성