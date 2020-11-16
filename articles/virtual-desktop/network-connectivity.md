---
title: Windows 가상 데스크톱 네트워크 연결 이해
titleSuffix: Azure
description: Windows 가상 데스크톱 네트워크 연결에 대 한 자세한 정보
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 4c0017a36d84973a4d99c49a5ea33faeb189b35f
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639341"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Windows 가상 데스크톱 네트워크 연결 이해

Windows 가상 데스크톱은 Azure에서 실행 되는 세션 호스트에서 클라이언트 세션을 호스트 하는 기능을 제공 합니다. Microsoft는 고객을 대신 하 여 서비스의 일부를 관리 하 고 클라이언트와 세션 호스트를 연결 하기 위한 보안 끝점을 제공 합니다. 아래 다이어그램은 Windows 가상 데스크톱에서 사용 되는 네트워크 연결에 대 한 개략적인 개요를 제공 합니다.

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Windows 가상 데스크톱 네트워크 연결 다이어그램" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>세션 연결

Windows 가상 데스크톱은 RDP (원격 데스크톱 프로토콜)를 사용 하 여 네트워크 연결을 통해 원격 표시 및 입력 기능을 제공 합니다. RDP는 처음에 Windows NT 4.0 터미널 서버 버전과 함께 출시 되었으며 Microsoft Windows 및 Windows Server 릴리스 마다 지속적으로 진화 하 고 있습니다. 처음부터 RDP는 기본 전송 스택과는 독립적으로 개발 되었으며 현재는 여러 유형의 전송을 지원 합니다.

## <a name="reverse-connect-transport"></a>역방향 연결 전송

Windows 가상 데스크톱은 원격 세션을 설정 하 고 RDP 트래픽을 운반 하기 위해 역방향 연결 전송을 사용 합니다. 온-프레미스 원격 데스크톱 서비스 배포와 달리, 역방향 연결 전송은 TCP 수신기를 사용 하 여 들어오는 RDP 연결을 수신 하지 않습니다. 대신 HTTPS 연결을 통해 Windows 가상 데스크톱 인프라에 대 한 아웃 바운드 연결을 사용 합니다.

## <a name="session-host-communication-channel"></a>세션 호스트 통신 채널

Windows 가상 데스크톱 세션 호스트를 시작할 때 원격 데스크톱 에이전트 로더 서비스는 Windows 가상 데스크톱 브로커의 영구 통신 채널을 설정 합니다. 이 통신 채널은 TLS (secure Transport Layer Security) 연결의 맨 위에 계층화 되며 세션 호스트와 Windows 가상 데스크톱 인프라 간의 서비스 메시지 교환에 대 한 버스 역할을 합니다.

## <a name="client-connection-sequence"></a>클라이언트 연결 시퀀스

클라이언트 연결 시퀀스는 아래에 설명 되어 있습니다.

1. 지원 되는 Windows 가상 데스크톱 클라이언트 사용자를 사용 하 여 Windows 가상 데스크톱 작업 영역 구독
2. Azure Active Directory 사용자를 인증 하 고 사용자가 사용할 수 있는 리소스를 열거 하는 데 사용 되는 토큰을 반환 합니다.
3. 클라이언트가 Windows 가상 데스크톱 피드 구독 서비스에 토큰을 전달 합니다.
4. Windows 가상 데스크톱 피드 구독 서비스에서 토큰의 유효성을 검사 합니다.
5. Windows 가상 데스크톱 피드 구독 서비스는 사용 가능한 데스크톱 및 Remoteapp의 목록을 디지털 서명 된 연결 구성 형식으로 클라이언트에 다시 전달 합니다.
6. 클라이언트는 사용할 수 있는 각 리소스에 대 한 연결 구성을 .rdp 파일 집합에 저장 합니다.
7. 사용자가 연결할 리소스를 선택 하면 클라이언트는 연결 된 .rdp 파일을 사용 하 고 가장 가까운 Windows 가상 데스크톱 게이트웨이 인스턴스에 대 한 보안 TLS 1.2 연결을 설정 하 고 연결 정보를 전달 합니다.
8. Windows 가상 데스크톱 게이트웨이에서 요청의 유효성을 검사 하 고 Windows 가상 데스크톱 브로커가 연결을 오케스트레이션 하도록 요청 합니다.
9. Windows Virtual Desktop broker는 세션 호스트를 식별 하 고 이전에 설정 된 영구 통신 채널을 사용 하 여 연결을 초기화 합니다.
10. 원격 데스크톱 스택은 클라이언트에서 사용 하는 것과 동일한 Windows 가상 데스크톱 게이트웨이 인스턴스에 대 한 TLS 1.2 연결을 시작 합니다.
11. 클라이언트 및 세션 호스트 모두에 연결 된 후 게이트웨이는 두 끝점 간에 원시 데이터 릴레이를 시작 하며,이는 RDP에 대 한 기본 역방향 연결 전송을 설정 합니다.
12. 기본 전송이 설정 된 후 클라이언트는 RDP 핸드셰이크를 시작 합니다.

## <a name="connection-security"></a>연결 보안

TLS 1.2은 클라이언트에서 시작 된 모든 연결과 Windows 가상 데스크톱 인프라 구성 요소에 대 한 세션 호스트에서 사용 됩니다.
역방향 연결 전송의 경우 클라이언트 및 세션 호스트는 모두 Windows 가상 데스크톱 게이트웨이에 연결 합니다. TCP 연결을 설정 하 고 나면 클라이언트 또는 세션 호스트에서 Windows 가상 데스크톱 게이트웨이의 인증서의 유효성을 검사 합니다.
기본 전송을 설정 하면 RDP에서 세션 호스트의 인증서를 사용 하 여 클라이언트와 세션 호스트 간에 중첩 된 TLS 연결을 설정 합니다. 기본적으로 RDP 암호화에 사용 되는 인증서는 배포 하는 동안 OS에 의해 자체 생성 됩니다. 원할 경우 고객은 엔터프라이즈 인증 기관에서 발급 한 중앙에서 관리 되는 인증서를 배포할 수 있습니다. 인증서 구성에 대 한 자세한 내용은 [Windows Server 설명서](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

* Windows 가상 데스크톱의 대역폭 요구 사항에 대 한 자세한 내용은 [Windows 가상 데스크톱에 대 한 RDP (원격 데스크톱 프로토콜) 대역폭 요구 사항 이해](rdp-bandwidth.md)를 참조 하세요.
* Windows 가상 데스크톱의 QoS (서비스 품질)를 시작 하려면 [Windows 가상 데스크톱에 대 한 qos (서비스 품질) 구현](rdp-quality-of-service-qos.md)을 참조 하세요.
