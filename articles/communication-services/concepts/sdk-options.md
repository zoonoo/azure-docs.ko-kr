---
title: Azure Communication Services 용 Sdk 및 REST Api
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services Sdk 및 REST Api에 대해 자세히 알아보세요.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 92324d68eabfb1885a482a7f539140f93be77596
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605198"
---
# <a name="sdks-and-rest-apis"></a>SDK 및 REST API

Azure Communication Services 기능은 개념적으로 6 개 영역으로 구성 됩니다. 대부분의 영역에는 인터넷을 통해 직접 사용할 수 있는 게시 된 REST Api에 대해 프로그래밍 된 완전히 오픈 소스 Sdk가 있습니다. 호출 하는 SDK는 소유 네트워크 인터페이스를 사용 하며 현재 닫혀 있는 소스입니다. Sdk에 대 한 샘플 및 추가 기술 세부 정보는 [Azure Communication Services GitHub](https://github.com/Azure/communication)리포지토리에 게시 됩니다.

## <a name="rest-apis"></a>REST API
통신 서비스 Api는 [docs.microsoft.com](/rest/api/azure/)의 다른 Azure REST api와 함께 설명 됩니다. 이 설명서에서는 HTTP 메시지를 구성 하는 방법을 설명 하 고 Postman 사용에 대 한 지침을 제공 합니다. 이 설명서는 [GitHub](https://github.com/Azure/azure-rest-api-specs)에서 Swagger 형식으로도 제공 됩니다.


## <a name="sdks"></a>SDK

| 어셈블리 | 네임스페이스| 프로토콜 | 기능 |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure. ResourceManager. 통신 | [REST (영문)](https://docs.microsoft.com/rest/api/communication/communicationservice)| 통신 서비스 리소스 프로 비전 및 관리|
| 일반 | Azure. 통신 Common| REST | 다른 Sdk의 기본 형식을 제공 합니다. |
| ID | Azure. 통신 Id| [REST (영문)](https://docs.microsoft.com/rest/api/communication/communicationidentity)| 사용자 관리, 토큰 액세스|
| 전화 번호 _(베타)_| PhoneNumbers| [REST (영문)](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)| 전화 번호 획득 및 관리 |
| 채팅 | Azure. 통신 채팅| 소유 신호를 포함 하는 [REST](https://docs.microsoft.com/rest/api/communication/) | 응용 프로그램에 실시간 텍스트 기반 채팅 추가 |
| sms| Azure. 통신 SMS | [REST (영문)](https://docs.microsoft.com/rest/api/communication/sms)| SMS 메시지 보내기 및 받기|
| 호출| Azure. 통신. 호출 | 소유 전송 | 음성, 비디오, 화면 공유 및 기타 실시간 데이터 통신 기능 사용 |

Azure Resource Manager, Id 및 SMS Sdk는 서비스 통합에 초점을 맞추고 있으며 이러한 기능을 최종 사용자 응용 프로그램에 통합 하는 경우 대부분의 경우 보안 문제가 발생 합니다. 공용 및 채팅 Sdk는 서비스 및 클라이언트 응용 프로그램에 적합 합니다. 호출 SDK는 클라이언트 응용 프로그램용으로 설계 되었습니다. 서비스 시나리오에 초점을 맞춘 SDK는 개발 하 고 있습니다.


### <a name="languages-and-publishing-locations"></a>언어 및 게시 위치

개별 SDK 패키지의 게시 위치는 아래에 자세히 설명 되어 있습니다.

| 영역           | JavaScript | .NET | Python | Java SE | iOS | Android | 기타                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [GitHub를 통해 이동](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 일반         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | 해당 없음      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| ID | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| 전화 번호 | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| 채팅           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| sms            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| 호출        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| 참조 설명서     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |


## <a name="rest-api-throttles"></a>REST API 제한
특정 REST Api 및 해당 SDK 메서드에는 제한 제한이 있습니다. 이러한 제한 제한을 초과 하면  `429 - Too Many Requests` 오류 응답이 트리거됩니다. [Azure 지원에 대 한 요청을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)통해 이러한 제한을 늘릴 수 있습니다.

| API                                                                                                                          | 제한            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [모든 검색 전화 번호 계획 Api](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)         | 4 개 요청/일      |
| [전화 번호 요금제 구매](https://docs.microsoft.com/rest/api/communication/phonenumberadministration/purchasesearch) | 1 한 달 구매  |
| [SMS 보내기](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 요청/분 |


## <a name="sdk-platform-support-details"></a>SDK 플랫폼 지원 세부 정보

### <a name="ios-and-android"></a>iOS 및 Android 

- 통신 서비스 iOS Sdk 대상 iOS 버전 13 이상 및 Xcode 11 이상
- Android Java Sdk 대상 Android API 레벨 21 이상 및 Android Studio 4.0 이상

### <a name="net"></a>.NET 

을 호출 하는 경우를 제외 하 고, 통신 서비스 패키지는 아래 나열 된 플랫폼을 지 원하는 .NET Standard 2.0를 대상으로 합니다.

.NET Framework 4.6.1를 통한 지원
- Windows 10, 8.1, 8 및 7
- Windows Server 2012 R2, 2012 및 2008 R2 SP1

.NET Core 2.0을 통한 지원:
- Windows 10 (1607 +), 7 SP1 +, 8.1
- Windows Server 2008 R2 SP1+
- 최대 OS X 10.12 +
- Linux 다중 버전/배포
- UWP 10.0.16299 (RS3) 9 월 2017
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

## <a name="api-stability-expectations"></a>API 안정성 기대

> [!IMPORTANT]
> 이 섹션에서는 **안정적인** 것으로 표시 된 REST Api 및 sdk에 대 한 지침을 제공 합니다. 시험판, 미리 보기 또는 베타로 표시 된 Api는 **통지 없이** 변경 되거나 더 이상 사용 되지 않을 수 있습니다.

향후에는 통신 서비스 Sdk 버전을 사용 중지할 수 있으며, REST Api와 출시 된 Sdk에 대 한 주요 변경 내용이 도입 될 수 있습니다. Azure 통신 서비스는 *일반적으로* 서비스 버전 사용을 중지 하기 위한 두 가지 지원 가능성 정책을 따릅니다.

- 통신 서비스 인터페이스 변경으로 인해 코드를 변경 해야 하기 전에 3 년 이상 알림이 표시 됩니다. 문서화 된 모든 REST Api 및 SDK Api는 일반적으로 인터페이스가 서비스 해제 되기 전에 3 년 이상 경고를 발생 시킵니다.
- SDK 어셈블리를 최신 부 버전으로 업데이트 하기 전에 적어도 1 년 이상 알림이 표시 됩니다. 이러한 필수 업데이트는 동일한 주 버전에 있기 때문에 코드를 변경할 필요가 없습니다. 보안 및 성능 업데이트가 자주 필요한 실시간 구성 요소를 포함 하는 호출 및 채팅 라이브러리의 경우 특히 그렇습니다. 통신 서비스 Sdk를 계속 업데이트 하는 것이 좋습니다.

### <a name="api-and-sdk-decommissioning-examples"></a>API 및 SDK 서비스 해제 예제

**V24 버전의 SMS REST API를 응용 프로그램에 통합 했습니다. Azure Communication은 v25을 릴리스 합니다.**

이러한 Api의 작동이 중지 되 고 v25로 강제로 업데이트 되기 전에 3 년 동안 경고가 발생 합니다. 이 업데이트를 수행 하려면 코드를 변경 해야 할 수 있습니다.

**호출 SDK의 v 2.02 버전을 응용 프로그램에 통합 했습니다. Azure Communication은 v 2.05을 릴리스 합니다.**

V 2.05의 릴리스 12 개월 이내에 호출 SDK의 v 2.05 버전으로 업데이트 해야 할 수 있습니다. 이는 v 2.05가 v2 주 버전에 있고 주요 변경 내용이 없기 때문에 코드를 변경 하지 않고도 아티팩트를 간단히 대체 해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 SDK 개요를 참조 하세요.

- [SDK 호출 개요](../concepts/voice-video-calling/calling-sdk-features.md)
- [채팅 SDK 개요](../concepts/chat/sdk-features.md)
- [SMS SDK 개요](../concepts/telephony-sms/sdk-features.md)

Azure 통신 서비스를 시작 하려면 다음을 수행 하세요.

- [Azure 통신 리소스 만들기](../quickstarts/create-communication-resource.md)
- [사용자 액세스 토큰](../quickstarts/access-tokens.md) 생성
