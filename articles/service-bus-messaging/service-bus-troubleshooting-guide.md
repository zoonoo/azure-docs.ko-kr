---
title: Azure Service Bus에 대 한 문제 해결 가이드 | Microsoft Docs
description: 이 문서에서는 예외 발생 시 수행할 수 있는 Azure Service Bus 메시징 예외 및 제안 된 작업 목록을 제공 합니다.
ms.topic: article
ms.date: 07/15/2020
ms.openlocfilehash: 6071aae85daa1852c9384656d7caf5e2deffd84e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071306"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure Service Bus에 대 한 문제 해결 가이드
이 문서에서는 Azure Service Bus를 사용할 때 표시 될 수 있는 몇 가지 문제에 대 한 문제 해결 팁과 권장 사항을 제공 합니다. 

## <a name="connectivity-certificate-or-timeout-issues"></a>연결, 인증서 또는 시간 제한 문제
다음 단계는 *. servicebus.windows.net의 모든 서비스에 대 한 연결/인증서/시간 제한 문제를 해결 하는 데 도움이 될 수 있습니다. 

- 또는 [wget](https://www.gnu.org/software/wget/) 으로 이동 `https://<yournamespace>.servicebus.windows.net/` 합니다. Java SDK를 사용 하는 경우 일반적으로 사용 되는 IP 필터링 또는 가상 네트워크 또는 인증서 체인 문제가 있는지 여부를 확인 하는 데 도움이 됩니다.

    성공적인 메시지의 예:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    오류 메시지의 예는 다음과 같습니다.

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 다음 명령을 실행 하 여 방화벽에서 차단 된 포트가 있는지 확인 합니다. 사용 되는 포트는 443 (HTTPS), 5671 (AMQP) 및 9354 (Net Messaging/SBMP)입니다. 사용 하는 라이브러리에 따라 다른 포트도 사용 됩니다. 다음은 5671 포트가 차단 되었는지 여부를 확인 하는 샘플 명령입니다. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux에서:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 간헐적 연결 문제가 있는 경우 다음 명령을 실행 하 여 삭제 된 패킷이 있는지 확인 합니다. 이 명령은 서비스와 1 초 마다 25 가지 TCP 연결을 설정 하려고 시도 합니다. 그런 다음 성공/실패 횟수를 확인 하 고 TCP 연결 대기 시간도 확인할 수 있습니다. 이 `psping` 도구는 [여기](/sysinternals/downloads/psping)에서 다운로드할 수 있습니다.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    , 등의 다른 도구를 사용 하는 경우에는 동일한 명령을 사용할 수 있습니다 `tnc` `ping` . 
- 이전 단계에서 [Wireshark](https://www.wireshark.org/)와 같은 도구를 사용 하 여 도움을 주지 않고 분석 하는 경우 네트워크 추적을 가져옵니다. 필요한 경우 [Microsoft 지원](https://support.microsoft.com/) 에 문의 하세요. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>서비스 업그레이드/다시 시작 시 발생할 수 있는 문제

### <a name="symptoms"></a>증상
- 요청이 일시적으로 제한 될 수 있습니다.
- 들어오는 메시지/요청이 삭제 될 수 있습니다.
- 로그 파일에는 오류 메시지가 포함 될 수 있습니다.
- 몇 초 동안 응용 프로그램의 서비스에서 연결을 끊을 수 있습니다.

### <a name="cause"></a>원인
백 엔드 서비스 업그레이드 및 다시 시작으로 인해 응용 프로그램에서 이러한 문제가 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법
응용 프로그램 코드에서 SDK를 사용 하는 경우 다시 시도 정책이 이미 내장 되어 있고 활성 상태입니다. 응용 프로그램/워크플로에 상당한 영향 없이 응용 프로그램이 다시 연결 됩니다.

## <a name="unauthorized-access-send-claims-are-required"></a>무단 액세스: 클레임 보내기가 필요 합니다.

### <a name="symptoms"></a>증상 
전송 권한이 있는 사용자 할당 관리 id를 사용 하 여 온-프레미스 컴퓨터의 Visual Studio에서 Service Bus 항목에 액세스 하려고 하면이 오류가 표시 될 수 있습니다.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>원인
Id에 Service Bus 항목에 액세스할 수 있는 권한이 없습니다. 

### <a name="resolution"></a>해결 방법
이 오류를 해결 하려면 [Microsoft. Azure](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .  자세한 내용은 [로컬 개발 인증](..\key-vault\service-to-service-authentication.md#local-development-authentication)을 참조 하세요. 

역할에 사용 권한을 할당 하는 방법에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 관리 Id 인증을 참조 하 여 Azure Service Bus 리소스에 액세스](service-bus-managed-service-identity.md)합니다.

## <a name="service-bus-exception-put-token-failed"></a>Service Bus 예외: Put 토큰이 실패 했습니다.

### <a name="symptoms"></a>증상
동일한 Service Bus 연결을 사용 하 여 1000 개 이상의 메시지를 전송 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다. 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>원인
Service Bus 네임 스페이스에 대 한 단일 연결을 사용 하 여 메시지를 보내고 받는 데 사용 되는 토큰 수에 제한이 있습니다. 1000입니다. 

### <a name="resolution"></a>해결 방법
더 많은 메시지를 전송 하려면 Service Bus 네임 스페이스에 대 한 새 연결을 엽니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [예외를 Azure Resource Manager](service-bus-resource-manager-exceptions.md)합니다. Azure Resource Manager (템플릿 또는 직접 호출을 통해)를 사용 하 여 Azure Service Bus와 상호 작용할 때 생성 되는 예외를 나열 합니다.
- [메시징 예외](service-bus-messaging-exceptions.md). Azure Service Bus에 대해 .NET Framework에서 생성 된 예외 목록을 제공 합니다. 

