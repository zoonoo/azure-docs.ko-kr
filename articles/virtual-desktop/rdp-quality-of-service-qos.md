---
title: Windows Virtual Desktop (미리 보기)에 대 한 QoS (서비스 품질) 구현
titleSuffix: Azure
description: Windows 가상 데스크톱에 대 한 QoS (미리 보기)를 설정 하는 방법
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639377"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Windows Virtual Desktop (미리 보기)에 대 한 QoS (서비스 품질) 구현

> [!IMPORTANT]
> Windows 가상 데스크톱에 대 한 QoS (서비스 품질) 정책 지원은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[RDP Shortpath](./shortpath.md) 는 원격 데스크톱 클라이언트와 세션 호스트 간에 직접 UDP 기반 전송을 제공 합니다. RDP Shortpath는 RDP 데이터에 대 한 QoS (서비스 품질) 정책을 구성할 수 있습니다.
Windows 가상 데스크톱의 QoS를 사용 하면 네트워크 지연에 영향을 주는 실시간 RDP 트래픽이 중요 한 트래픽 앞에서 "줄에서 잘라내기"로 제한 됩니다. 이러한 비교적 중요 하지 않은 트래픽의 예는 새 앱을 다운로드 하는 것입니다 .이 경우 다운로드 하는 데 더 많은 초가 발생 하지 않습니다. QoS는 Windows 그룹 정책 개체를 사용 하 여 실시간 스트림의 모든 패킷을 식별 하 고 표시 하 고 네트워크에서 RDP 트래픽을 전용 대역폭으로 제공할 수 있도록 지원 합니다.

이 문서에서 설명 하는 문제가 발생 하는 많은 사용자 그룹을 지 원하는 경우 QoS를 구현 해야 할 것입니다. 사용자가 많지 않은 중소기업은 QoS가 필요 하지 않을 수 있지만,이 경우에도 도움이 됩니다.

특정 형식의 QoS가 없으면 다음과 같은 문제가 나타날 수 있습니다.

* 지터 – RDP 패킷이 서로 다른 속도로 도착 하 여 시각적 및 오디오 결함을 초래할 수 있습니다.
* 패킷 손실 – 패킷이 삭제 되어 추가 시간이 필요한 재전송이 발생 합니다.
* 지연 된 왕복 시간 (RTT) – 대상에 도달 하는 데 오랜 시간이 걸리는 RDP 패킷 때문에 원격 응용 프로그램의 입력 및 반응 사이에는 상당한 지연이 발생 합니다.

이러한 문제를 해결 하는 가장 복잡 한 방법은 내부적으로 인터넷으로 데이터 연결의 크기를 늘리는 것입니다. 이는 비용이 많이 들지 않으므로 QoS는 대역폭을 보다 효율적으로 추가 하는 대신 리소스를 관리 하는 방법을 제공 합니다. 품질 문제를 해결 하려면 먼저 QoS를 사용 하 고 필요한 경우에만 대역폭을 추가 하는 것이 좋습니다.

QoS를 적용 하려면 조직 전체에서 일관 된 QoS 설정을 적용 해야 합니다. QoS 우선 순위를 지원 하지 않는 경로의 모든 부분은 품질 RDP 세션을 저하 시킬 수 있습니다.

## <a name="introduction-to-qos-queues"></a>QoS 큐 소개

QoS를 제공 하려면 네트워크 장치에 트래픽을 분류 하는 방법이 있어야 하 고 RDP를 다른 네트워크 트래픽과 구별할 수 있어야 합니다.

네트워크 트래픽이 라우터로 들어가면 트래픽이 큐에 배치 됩니다. QoS 정책이 구성 되지 않은 경우에는 큐가 하나 뿐 이며 모든 데이터는 우선 순위가 같은 선입 first로 처리 됩니다. 즉, RDP 트래픽이 트래픽 뒤에서 중단 될 수 있습니다 .이 경우 몇 초 지연 시간이 더 이상 문제가 되지 않습니다.

QoS를 구현할 때 Cisco의 우선 순위 큐 및 [클래스 기반 가중치 양호 큐 (CBWFQ)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) 및 정체 방지 기능 (예: 가중치가 적용 된 [임의 조기 검색](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html))과 같은 여러 정체 관리 기능 중 하나를 사용 하 여 여러 큐를 정의 합니다.

간단한 비유는 QoS가 데이터 네트워크에 가상 "carpool 레인"을 만드는 것입니다. 따라서 일부 데이터 형식은 지연이 발생 하거나 거의 발생 하지 않습니다. 이러한 레인을 만든 후에는 조직의 사용자에 대 한 비즈니스 수준 환경을 제공 하면서도 상대적 크기를 조정 하 고 연결 대역폭을 훨씬 더 효과적으로 관리할 수 있습니다.

## <a name="qos-implementation-checklist"></a>QoS 구현 검사 목록

높은 수준에서 다음을 수행 하 여 QoS를 구현 합니다.

1. [네트워크가 준비 되었는지 확인](#make-sure-your-network-is-ready)
2. [RDP Shortpath 사용](./shortpath.md) -QoS 정책이 역방향 연결 전송에 대해 지원 되지 않는지 확인 합니다.
3. 세션 호스트에 [DSCP 마커 삽입 구현](#insert-dscp-markers)

QoS를 구현할 준비가 되 면 다음 지침을 염두에 두어야 합니다.

* 가장 짧은 세션 호스트 경로
* 프록시 또는 패킷 검사 장치와 같은 간의 장애물은 권장 되지 않습니다.

## <a name="make-sure-your-network-is-ready"></a>네트워크가 준비 되었는지 확인

QoS 구현을 고려 하는 경우 대역폭 요구 사항 및 기타 [네트워크 요구 사항을](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context)이미 결정 해야 합니다.
  
네트워크를 통한 트래픽 정체는 미디어 품질에 크게 영향을 줍니다. 대역폭이 부족 하면 성능이 저하 되 고 사용자 환경이 저하 될 수 있습니다. Windows 가상 데스크톱 채택 및 사용이 증가 함에 따라 [Log Analytics](./diagnostics-log-analytics.md) 를 사용 하 여 문제를 식별 한 다음 QoS 및 선택적 대역폭 추가를 사용 하 여 조정을 수행 합니다.

### <a name="vpn-considerations"></a>VPN 고려 사항

QoS는 클라이언트와 세션 호스트 간의 모든 링크에서 구현 될 경우 예상 대로 작동 합니다. 내부 네트워크에 QoS를 사용 하 고 사용자가 원격 위치에서 로그인 하는 경우 내부 관리 되는 네트워크 내 에서만 우선 순위를 지정할 수 있습니다. 원격 위치에서 VPN (가상 사설망)을 구현 하 여 관리 되는 연결을 수신할 수 있지만 기본적으로 VPN은 패킷 오버 헤드를 추가 하 고 실시간 트래픽에 대 한 지연을 만듭니다.

대륙에 걸친 관리 링크를 포함 하는 글로벌 조직에서는 이러한 링크에 대 한 대역폭이 LAN에 비해 제한 되므로 QoS를 사용 하는 것이 좋습니다.

## <a name="insert-dscp-markers"></a>DSCP 표식 삽입

그룹 정책 개체 (GPO)를 사용 하 여 QoS를 구현 하 여 세션 호스트가 특정 유형의 트래픽으로 식별 하는 IP 패킷 헤더에 DSCP 표식을 삽입 하도록 지시할 수 있습니다. 라우터 및 기타 네트워크 장치를 구성 하 여 이러한 표시를 인식 하 고 트래픽을 우선 순위가 높은 별도의 큐에 배치할 수 있습니다.

DSCP 표시를 우편 번호를 나타내는 우표 스탬프와 비교할 수 있습니다 .이는 배달에 대 한 긴급도와 신속한 배달에 맞게 정렬 하는 방법을 보여 줍니다. RDP 스트림에 우선 순위를 부여 하도록 네트워크를 구성한 후에는 손실 된 패킷과 지연 패킷이 현저 하 게 감소 해야 합니다.

모든 네트워크 장치가 동일한 분류, 표시 및 우선 순위를 사용 하는 경우 지연, 삭제 된 패킷 및 지터를 줄이거나 제거할 수 있습니다. RDP 관점에서 필수 구성 단계는 패킷 분류 및 표시입니다. 그러나 종단 간 QoS를 성공적으로 수행 하려면 기본 네트워크 구성에 RDP 구성을 신중 하 게 정렬 해야 합니다.
DSCP 값은 구성 된 네트워크에 패킷 또는 스트림에 제공할 우선 순위를 알려 줍니다.

**EF (긴급 전달)** dscp 클래스에 매핑되는 dscp 값 46를 사용 하는 것이 좋습니다.

### <a name="implement-qos-on-session-host-using-group-policy"></a>그룹 정책를 사용 하 여 세션 호스트에 QoS 구현

그룹 정책 내에서 정책 기반 QoS (서비스 품질)를 사용 하 여 미리 정의 된 DSCP 값을 설정할 수 있습니다.

도메인에 가입 된 세션 호스트에 대 한 QoS 정책을 만들려면 먼저 그룹 정책 관리가 설치 된 컴퓨터에 로그인 합니다. 그룹 정책 관리 (시작을 선택 하 고 관리 도구를 가리킨 다음 그룹 정책 관리를 선택)를 열고 다음 단계를 완료 합니다.

1. 그룹 정책 관리에서 새 정책을 만들어야 하는 컨테이너를 찾습니다. 예를 들어 모든 세션이 컴퓨터를 호스트 하는 OU **"세션 호스트"** 에 있는 경우 세션 호스트 ou에 새 정책을 만들어야 합니다.

2. 적절 한 컨테이너를 마우스 오른쪽 단추로 클릭 하 고 **이 도메인에서 GPO를 만들어 여기에 연결을** 선택 합니다.

3. **새 GPO** 대화 상자의 **이름** 상자에 새 그룹 정책 개체의 이름을 입력 하 고 **확인** 을 선택 합니다.

4. 새로 만든 정책을 마우스 오른쪽 단추로 클릭 한 다음 **편집** 을 선택 합니다.

5. 그룹 정책 관리 편집기에서 **컴퓨터 구성** , **Windows 설정** 을 차례로 확장 하 고 **정책 기반 QoS** 를 마우스 오른쪽 단추로 클릭 한 다음 **새 정책 만들기** 를 선택 합니다.

6. **정책 기반 QoS** 대화 상자의 열기 페이지에서 **이름** 상자에 새 정책의 이름을 입력 합니다. **DSCP 값 지정** 을 선택 하 고 값을 **46** 으로 설정 합니다. **아웃 바운드 스로틀 시간 지정** 을 선택 하지 않은 채로 두고 **다음** 을 선택 합니다.

7. 다음 페이지에서 **이 실행 파일 이름이 있는 응용 프로그램만** 을 선택 하 고 **svchost.exe** 이름을 입력 한 후 **다음** 을 선택 합니다. 이 설정은 원격 데스크톱 서비스와 일치 하는 트래픽의 우선 순위를 지정 하도록 정책에 지시 합니다.

8. 세 번째 페이지에서 **원본 ip 주소** 와 **대상 ip 주소가** 모두 선택 되어 있는지 확인 하 고 **다음** 을 선택 합니다. 이러한 두 설정은 패킷을 전송 하는 컴퓨터 (IP 주소)와 패킷 수신 컴퓨터 (IP 주소)에 관계 없이 패킷이 관리 되는지 확인 합니다.

9. 4 페이지의 **이 QoS 정책이 적용 되는 프로토콜 선택** 드롭다운 목록에서 **UDP** 를 선택 합니다.

10. **원본 포트 번호 지정** 제목 아래에서 **이 원본 포트 또는 범위를** 선택 합니다. 해당 텍스트 상자에 **3390** 을 입력 합니다. **마침** 을 선택합니다.

사용자가 만든 새 정책은 세션 호스트 컴퓨터에서 그룹 정책 새로 고쳐질 때까지 적용 되지 않습니다. 그룹 정책 정기적으로 새로 고칠 수 있지만 다음 단계를 수행 하 여 즉시 새로 고침을 강제로 수행할 수 있습니다.

1. 그룹 정책를 새로 고칠 각 세션 호스트에서 관리자 권한 ( *관리자 권한으로 실행* )으로 명령 프롬프트를 엽니다.

1. 명령 프롬프트에서 다음을 입력 합니다.

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>PowerShell을 사용 하 여 세션 호스트에 QoS 구현

아래 PowerShell cmdlet을 사용 하 여 RDP Shortpath에 대 한 QoS를 설정할 수 있습니다.

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>관련 문서

* [QoS (서비스 품질) 정책](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>다음 단계

* Windows 가상 데스크톱의 대역폭 요구 사항에 대 한 자세한 내용은 [Windows 가상 데스크톱에 대 한 RDP (원격 데스크톱 프로토콜) 대역폭 요구 사항 이해](rdp-bandwidth.md)를 참조 하세요.
* Windows 가상 데스크톱 네트워크 연결에 대 한 자세한 내용은 [Windows 가상 데스크톱 네트워크 연결 이해](network-connectivity.md)를 참조 하세요.
