---
title: Azure 서비스 버스문제 해결 가이드 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Service Bus 메시징 예외 목록 및 예외가 발생할 때 수행할 권장 작업 목록을 제공합니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887776"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure 서비스 버스에 대한 문제 해결 가이드
이 문서에서는 Azure Service Bus를 사용할 때 발생할 수 있는 몇 가지 문제에 대한 문제 해결 팁과 권장 사항을 제공합니다. 

## <a name="connectivity-certificate-or-timeout-issues"></a>연결, 인증서 또는 시간 시간 시간 문제
다음 단계는 *.servicebus.windows.net 따라 모든 서비스에 대한 연결/인증서/시간 시간 문제 문제를 해결하는 데 도움이 될 수 있습니다. 

- 을 찾아보거나 [wget.](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` JAVA SDK를 사용할 때 가장 일반적인 IP 필터링 또는 가상 네트워크 또는 인증서 체인 문제가 있는지 확인하는 데 도움이 됩니다.

    성공적인 메시지의 예:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    오류 오류 메시지의 예:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 다음 명령을 실행하여 방화벽에서 포트가 차단되었는지 확인합니다. 사용되는 포트는 443(HTTPS), 5671(AMQP) 및 9354(순 메시징/SBMP)입니다. 사용하는 라이브러리에 따라 다른 포트도 사용됩니다. 다음은 5671 포트가 차단되었는지 여부를 확인하는 샘플 명령입니다. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux에서:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 간헐적인 연결 문제가 있는 경우 다음 명령을 실행하여 삭제된 패킷이 있는지 확인합니다. 이 명령은 서비스에서 1초마다 25개의 서로 다른 TCP 연결을 설정하려고 시도합니다. 그런 다음 성공/실패한 횟수를 확인하고 TCP 연결 대기 시간도 확인할 수 있습니다. 여기에서 도구를 `psping` 다운로드할 수 [있습니다.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    `tnc`.와 `ping`같은 다른 도구를 사용하는 경우 등가 명령을 사용할 수 있습니다. 
- 이전 단계가 도움이 되지 않는 경우 네트워크 추적을 가져오고 [Wireshark와](https://www.wireshark.org/)같은 도구를 사용하여 분석합니다. 필요한 경우 [Microsoft 지원에](https://support.microsoft.com/) 문의하십시오. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>서비스 업그레이드/다시 시작시 발생할 수 있는 문제
백 엔드 서비스 업그레이드 및 다시 시작하면 응용 프로그램에 다음과 같은 영향을 줄 수 있습니다.

- 요청이 일시적으로 제한될 수 있습니다.
- 들어오는 메시지/요청이 떨어질 수 있습니다.
- 로그 파일에 오류 메시지가 포함될 수 있습니다.
- 응용 프로그램의 연결이 몇 초 동안 서비스에서 분리될 수 있습니다.

응용 프로그램 코드가 SDK를 사용하는 경우 재시도 정책이 이미 빌드되어 활성화되어 있습니다. 응용 프로그램은 응용 프로그램/워크플로에 큰 영향을 주지 않고 다시 연결됩니다.

## <a name="unauthorized-access-send-claims-are-required"></a>무단 액세스: 클레임 보내기가 필요합니다.
보낸 권한이 있는 사용자 할당된 관리 되는 ID를 사용 하 여 온-프레미스 컴퓨터에서 Visual Studio에서 Service Bus 항목에 액세스 하려고 할 때이 오류가 표시 될 수 있습니다.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

이 오류를 해결하려면 [Microsoft.Azure.Services.App 인증](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) 라이브러리를 설치합니다.  자세한 내용은 [로컬 개발 인증을](..\key-vault\service-to-service-authentication.md#local-development-authentication)참조하십시오. 

역할에 권한을 할당하는 방법을 알아보려면 [Azure Active Directory를 사용하여 관리되는 ID 인증을 참조하여 Azure Service Bus 리소스에 액세스합니다.](service-bus-managed-service-identity.md)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [Azure 리소스 관리자 예외](service-bus-resource-manager-exceptions.md). 템플릿 또는 직접 호출을 통해 Azure 리소스 관리자를 사용하여 Azure Service Bus와 상호 작용할 때 생성된 예외가 나열됩니다.
- [메시징 예외](service-bus-messaging-exceptions.md). Azure Service Bus에 대한 .NET 프레임워크에서 생성된 예외 목록을 제공합니다. 

