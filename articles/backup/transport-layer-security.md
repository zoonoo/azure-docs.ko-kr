---
title: Azure Backup의 전송 계층 보안
description: 암호화 프로토콜 TLS(전송 계층 보안)를 사용하여 네트워크를 통해 전송될 때 데이터를 안전하게 유지하도록 Azure Backup을 설정하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327120"
---
# <a name="transport-layer-security-in-azure-backup"></a>Azure Backup의 전송 계층 보안

TLS(전송 계층 보안)는 네트워크를 통해 전송될 때 데이터를 안전하게 유지하는 암호화 프로토콜입니다. Azure Backup은 전송 계층 보안을 사용하여 전송되는 백업 데이터의 개인 정보를 보호합니다. 이 문서에서는 이전 버전보다 향상된 보안을 제공하는 TLS 1.2 프로토콜을 사용하도록 설정하는 단계를 설명합니다.

## <a name="earlier-versions-of-windows"></a>이전 버전 Windows

컴퓨터에서 이전 버전의 Windows를 실행하는 경우 아래에 명시된 해당 업데이트를 설치하고 KB 문서에 설명된 레지스트리 변경 내용을 적용해야 합니다.

|운영 체제  |기술 자료 문서 |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>업데이트 시 필요한 프로토콜 구성 요소가 설치됩니다. 설치 후 필요한 프로토콜을 제대로 사용하려면 위의 KB 문서에 언급된 레지스트리 키를 변경해야 합니다.

## <a name="verify-windows-registry"></a>Windows 레지스트리 확인

### <a name="configuring-schannel-protocols"></a>SChannel 프로토콜 구성

다음 레지스트리 키는 SChannel 구성 요소 수준에서 TLS 1.2 프로토콜을 사용하도록 설정했는지 확인합니다.

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>표시된 값은 Windows Server 2012 R2 이상 버전에 기본적으로 설정되어 있습니다. 이러한 버전의 Windows에서는 레지스트리 키가 없는 경우 만들 필요가 없습니다.

### <a name="configuring-net-framework"></a>.NET Framework 구성

다음 레지스트리 키는 강력한 암호화를 지원하도록 .NET Framework를 구성합니다. [여기에서 .NET Framework 구성](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)에 대해 자세히 알아볼 수 있습니다.

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-enable-tls-12"></a>왜 TLS 1.2를 사용해야 하나요?

TLS 1.2는 SSL 2.0, SSL 3.0, TLS 1.0 및 TLS 1.1 등 이전의 암호화 프로토콜에 비해 더욱 안전합니다. Azure Backup 서비스는 이미 TLS 1.2를 완전히 지원합니다.

### <a name="what-determines-the-encryption-protocol-used"></a>사용되는 암호화 프로토콜을 결정하는 요소는 무엇인가요?

클라이언트와 서버에서 모두 지원되는 가장 높은 프로토콜 버전은 암호화된 대화를 설정하기 위해 협상합니다. TLS 핸드셰이크 프로토콜에 대한 자세한 내용은 [TLS를 사용한 보안 세션 설정](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)을 참조하세요.

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>TLS 1.2를 사용하지 않을 경우의 어떤 영향이 있나요?

프로토콜 다운그레이드 공격에 대한 보안 향상을 위해 Azure Backup은 단계별 방식으로 1.2 보다 오래된 TLS 버전을 사용하지 않도록 설정하기 시작합니다. 이는 레거시 프로토콜 및 암호 그룹 연결을 허용하지 않도록 서비스 전반에 걸친 장기 이동의 일환입니다. Azure Backup 서비스와 구성 요소는 TLS 1.2을 완전히 지원합니다. 하지만 필수 업데이트 또는 특정 사용자 지정된 구성이 없는 Windows 버전으로 인해 여전히 TLS 1.2 프로토콜이 제공하지 못할 수 있습니다. 이로 인해 다음 중 하나 이상을 포함한 오류가 발생할 수 있습니다.

- 백업 및 복원 작업에 실패할 수 있습니다.
- 오류 10054가 표시되면서 백업 구성 요소 연결에 실패합니다(기존 연결이 원격 호스트에 의해 강제로 끊겼습니다).
- Azure Backup 관련된 서비스는 평소와 같이 중지되거나 시작되지 않습니다.

## <a name="additional-resources"></a>추가 리소스

- [전송 계층 보안 프로토콜](/windows/win32/secauthn/transport-layer-security-protocol)
- [배포된 운영 체제에서 TLS 1.2 지원 보장](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework에 대한 TLS(전송 계층 보안) 모범 사례](/dotnet/framework/network-programming/tls)