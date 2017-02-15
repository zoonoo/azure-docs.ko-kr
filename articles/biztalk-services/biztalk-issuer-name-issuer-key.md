---
title: "BizTalk 서비스의 발급자 이름 및 발급자 키 | Microsoft Docs"
description: "BizTalk 서비스에서 서비스 버스 또는 액세스 제어(ACS)에 대한 발급자 이름 및 발급자 키를 검색하는 방법에 대해 알아봅니다. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 4fb13a158c660105a5fc8f79a92c67ba65c5356d


---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk 서비스: 발급자 이름 및 발급자 키
Azure BizTalk 서비스는 서비스 버스 발급자 이름 및 발급자 키와 액세스 제어 발급자 이름 및 발급자 키를 사용합니다. 구체적으로 살펴보면 다음과 같습니다.

| 작업 | 발급자 이름 및 발급자 키 |
| --- | --- |
| Visual Studio에서 응용 프로그램 배포 |액세스 제어 발급자 이름 및 발급자 키 |
| Azure BizTalk 서비스 포털 구성 |액세스 제어 발급자 이름 및 발급자 키 |
| Visual Studio에서 BizTalk 어댑터 서비스로 LOB 릴레이 만들기 |서비스 버스 발급자 이름 및 발급자 키 |

이 토픽에서는 발급자 이름 및 발급자 키를 검색하는 단계를 나열합니다. 

## <a name="access-control-issuer-name-and-issuer-key"></a>액세스 제어 발급자 이름 및 발급자 키
액세스 제어 발급자 이름 및 발급자 키는 다음에 사용됩니다.

* Visual Studio에서 만든 Azure BizTalk 서비스 응용 프로그램: Visual Studio의 BizTalk 서비스 응용 프로그램을 Azure에 배포하려면 액세스 제어 발급자 이름 및 발급자 키를 입력합니다. 
* Azure BizTalk 서비스 포털: BizTalk 서비스를 만들고 BizTalk 서비스 포털에 로그인하면 배포를 위해 액세스 제어 발급자 이름 및 액세스 제어 발급자 키가 동일한 액세스 제어 값으로 자동으로 등록됩니다.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>액세스 제어 발급자 이름 및 발급자 키 가져오기

인증을 위해 ACS를 사용하고 발급자 이름 및 발급자 키 값을 가져오는 전체 단계는 다음과 같습니다.

1. [Azure PowerShell cmdlet](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)을 설치합니다.
2. Azure 계정을 추가합니다. `Add-AzureAccount`
3. 구독 이름을 반환합니다. `get-azuresubscription`
4. 사용 중인 구독을 선택합니다. `select-azuresubscription <name of your subscription>` 
5. 새 네임스페이스를 만듭니다. `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    예:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. 새 ACS 네임스페이스를 만들면(몇 분 정도 걸릴 수 있음) 발급자 이름 및 발급자 키 값이 연결 문자열에 나열됩니다. 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

요약하면  
발급자 이름 = SharedSecretIssuer  
발급자 키 = SharedSecretKey

[New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx) cmdlet에 대한 추가 정보 

## <a name="service-bus-issuer-name-and-issuer-key"></a>서비스 버스 발급자 이름 및 발급자 키
서비스 버스 발급자 이름 및 발급자 키는 BizTalk 어댑터 서비스에 사용됩니다. Visual Studio의 BizTalk 서비스 프로젝트에서 BizTalk 어댑터 서비스를 사용하여 온-프레미스 LOB(기간 업무) 시스템에 연결합니다. 연결하려면 LOB 릴레이를 만들고 LOB 시스템 세부 정보를 입력합니다. 이 작업을 수행할 때에도 서비스 버스 발급자 이름 및 발급자 키를 입력합니다.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>서비스 버스 발급자 이름 및 발급자 키를 검색하려면
1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **Service Bus**를 선택합니다.
3. 네임스페이스를 선택합니다. 작업 표시줄에서 **연결 정보**를 선택합니다. 그러면 **기본 발급자**(발급자 이름) 및 **기본 키**(발급자 키)가 표시됩니다. 이 값은 복사할 수 있습니다.  

요약하면  
발급자 이름 = 기본 발급자  
발급자 키 = 기본 키

## <a name="next"></a>다음
추가 Azure BizTalk 서비스 항목:

* [Azure BizTalk 서비스 SDK 설치](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [자습서: Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>참고 항목
* [방법: ACS 관리 서비스를 사용하여 서비스 ID 구성](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk 서비스: Azure 클래식 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk 서비스: 프로비저닝 상태 차트](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk 서비스: 백업 및 복원](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk 서비스: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>




<!--HONumber=Nov16_HO3-->


