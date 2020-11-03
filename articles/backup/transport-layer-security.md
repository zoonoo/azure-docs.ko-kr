---
title: Azure Backup의 전송 계층 보안
description: 암호화 프로토콜 TLS (전송 계층 보안)를 사용 하 여 네트워크를 통해 전송 될 때 데이터를 안전 하 게 유지 하도록 Azure Backup 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: 9881fd3532cbc7d67c7d5adbce75e02fc62e0bcf
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280682"
---
# <a name="transport-layer-security-in-azure-backup"></a>Azure Backup의 전송 계층 보안

TLS (전송 계층 보안)는 네트워크를 통해 전송 될 때 데이터를 안전 하 게 유지 하는 암호화 프로토콜입니다. Azure Backup 전송 계층 보안을 사용 하 여 전송 되는 백업 데이터의 개인 정보를 보호 합니다. 이 문서에서는 이전 버전 보다 향상 된 보안을 제공 하는 TLS 1.2 프로토콜을 사용 하도록 설정 하는 단계를 설명 합니다.

## <a name="earlier-versions-of-windows"></a>이전 버전 Windows

컴퓨터에서 이전 버전의 Windows를 실행 하는 경우 아래에 명시 된 해당 업데이트를 설치 하 고 KB 문서에 설명 된 레지스트리 변경 내용을 적용 해야 합니다.

|운영 체제  |기술 자료 문서 |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>업데이트 시 필요한 프로토콜 구성 요소가 설치 됩니다. 설치 후 필요한 프로토콜을 제대로 사용 하려면 위의 KB 문서에 언급 된 레지스트리 키를 변경 해야 합니다.

## <a name="verify-windows-registry"></a>Windows 레지스트리 확인

### <a name="configuring-schannel-protocols"></a>SChannel 프로토콜 구성

다음 레지스트리 키는 SChannel 구성 요소 수준에서 TLS 1.2 프로토콜이 사용 하도록 설정 되었는지 확인 합니다.

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>표시 된 값은 Windows Server 2012 R2 이상 버전에서 기본적으로 설정 되어 있습니다. 이러한 버전의 Windows에서는 레지스트리 키가 없는 경우 만들 필요가 없습니다.

### <a name="configuring-net-framework"></a>.NET Framework 구성

다음 레지스트리 키는 강력한 암호화를 지원 하도록 .NET Framework를 구성 합니다. [여기에서 .NET Framework 구성](https://docs.microsoft.com/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)에 대해 자세히 알아볼 수 있습니다.

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-enable-tls-12"></a>TLS 1.2을 사용 하도록 설정 하는 이유

TLS 1.2는 SSL 2.0, SSL 3.0, TLS 1.0 및 TLS 1.1과 같은 이전 암호화 프로토콜 보다 더 안전 합니다. Azure Backup 서비스는 이미 TLS 1.2을 완벽 하 게 지원 합니다.

### <a name="what-determines-the-encryption-protocol-used"></a>사용 되는 암호화 프로토콜은 무엇 인가요?

클라이언트와 서버 모두에서 지원 되는 가장 높은 프로토콜 버전은 암호화 된 대화를 설정 하기 위해 협상 됩니다. TLS 핸드셰이크 프로토콜에 대 한 자세한 내용은 TLS를 [사용 하 여 보안 세션 설정](https://docs.microsoft.com/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)을 참조 하세요.

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>TLS 1.2을 사용 하지 않을 경우의 영향은 무엇 인가요?

프로토콜 다운 그레이드 공격의 보안 향상을 위해 Azure Backup는 단계별 방식으로 1.2 보다 오래 된 TLS 버전을 사용 하지 않도록 설정 하기 시작 합니다. 레거시 프로토콜 및 암호 그룹 연결을 허용 하지 않도록 서비스 전반의 장기 이동의 일부입니다. Azure Backup 서비스와 구성 요소는 TLS 1.2을 완벽 하 게 지원 합니다. 그러나 필수 업데이트 또는 사용자 지정 된 특정 구성이 없는 Windows 버전에서는 여전히 TLS 1.2 프로토콜이 제공 되는 것을 방지할 수 있습니다. 이로 인해 오류가 발생 하는 경우는 다음 중 하나 이상으로 제한 될 수 있습니다.

- 백업 및 복원 작업이 실패할 수 있습니다.
- 백업 구성 요소 연결 실패 오류 10054 (기존 연결이 원격 호스트에 의해 강제로 끊겼습니다.)
- Azure Backup 관련 된 서비스는 정상적으로 중지 되거나 시작 되지 않습니다.

## <a name="additional-resources"></a>추가 리소스

- [전송 계층 보안 프로토콜](https://docs.microsoft.com/windows/win32/secauthn/transport-layer-security-protocol)
- [배포 된 운영 체제에서 TLS 1.2에 대 한 지원 보장](https://docs.microsoft.com/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework에 대한 TLS(전송 계층 보안) 모범 사례](https://docs.microsoft.com/dotnet/framework/network-programming/tls)
