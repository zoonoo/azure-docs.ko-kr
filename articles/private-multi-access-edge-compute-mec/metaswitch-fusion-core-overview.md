---
title: Azure의 Fusion Core 솔루션
description: Fusion Core 개요 - 5G 네트워크 운영자가 여러 무선 및 고정 액세스 기술을 통해 모든 최종 디바이스에서 데이터 트래픽을 집계할 수 있는 3GPP 표준 정의 5G 차세대 코어(5G NGC 또는 5GC)의 클라우드 네이티브 구현입니다.
ms.service: private-multi-access-edge-compute-mec
author: djrmetaswitch
ms.author: drichards
ms.topic: overview
ms.date: 06/16/2021
ms.openlocfilehash: dd813b08301da960fb13e6047e51046ac2d95aed
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112464560"
---
# <a name="what-is-fusion-core"></a>Fusion Core란?

Fusion Core는 5G 네트워크 운영자가 여러 무선 및 고정 액세스 기술을 통해 모든 최종 디바이스에서 데이터 트래픽을 집계할 수 있는 3GPP 표준 정의 5G 차세대 코어(5G NGC 또는 5GC)의 클라우드 네이티브 구현입니다. 고도로 프로그래밍 가능한 고성능 5G UPF(사용자 평면 기능), 핵심 컨트롤 플레인 기능, 서비스 기반 아키텍처 요소의 포트폴리오, 네트워크 모니터링을 위한 관리 구성 요소 등으로 구성됩니다.

Fusion Core는 순수 5G 시나리오로 배포하거나 4G 네트워크와 상호 운용할 수 있습니다.

Fusion Core는 다음 5G 네트워크 기능을 구현합니다.

|네트워크 기능  |Description  |
|---------|---------|
|**AMF**<br><br>액세스 및 모바일 관리 기능     |이 기능은 다음을 지원합니다.<br><ul> <li>gNB의 NAS 신호 종료</li><li>NAS 암호화 및 무결성 보호</li><li>등록 관리</li><li>연결 관리</li><li>모바일 관리</li><li>액세스 인증 및 권한 부여</li><li>보안 컨텍스트 관리</li></ul>         |
|**SMF**<br><br>세션 관리 기능     |SMF는 세션 설정, 수정 및 릴리스를 지원합니다.<br><br>이 기능의 일부로 SMF는 DHCP를 비롯한 UE IP 주소 할당 및 관리뿐 아니라 세션 관리와 관련된 NAS 신호 종료를 제공합니다.<br><br>또한 다운링크 데이터 트래픽에서 디바이스를 페이징하고 세션 관리의 일부로 트래픽 스티어링 구성을 제공하기 위한 지원을 제공합니다.         |
|**UPF**<br><br>사용자 평면 기능     |UPF는 패킷 데이터 트래픽을 처리해야 합니다. 여기에는 패킷 데이터 트래픽의 라우팅, 전달, 검사 및 QoS 처리가 포함됩니다.<br><br>UPF는 DN(데이터 네트워크)에 대한 트래픽과 여러 가지 라디오 네트워크 간 핸드오버를 위한 트래픽의 앵커 지점 역할을 합니다.         |
|**PCF**<br><br>정책 제어 기능     |PCF는 컨트롤 플레인 기능의 정책 규칙을 제공하고 구독 정보의 원본 역할을 하는 트래픽의 중앙 정책 프레임워크를 제공합니다.         |
|**AUSF**<br><br>인증 서버 기능     |AUSF는 5G 구독자를 위한 인증 서버입니다.         |
|**UDM**<br><br>통합 데이터 관리     |UDM은 AKA 자격 증명 생성, 사용자 식별, 액세스 권한 부여, 구독자 관리 등을 지원합니다.         |
|**UDR**<br><br>통합 데이터 리포지토리     |UDR은 모든 구독자 정보의 수렴된 리포지토리입니다.         |
|**NRF**<br><br>네트워크 리포지토리 기능     |NRF는 NF에 대한 서비스 검색을 제공합니다.         |

또한 4G 네트워크와 상호 연동될 때 다음 네트워크 기능을 구현합니다.

|네트워크 기능  |Description  |
|---------|---------|
|**MME**<br><br>모바일 관리 엔터티     |MME는 LTE 액세스 네트워크의 핵심 제어 노드이며 네트워크에 대한 UE 액세스의 모든 측면을 제어해야 합니다.         |
|**S11-IWF**<br><br>S11 상호 연동 기능 |S11-IWF는 4G MME(모바일 관리 엔터티)에 대한 S11 인터페이스를 제공하며 기능을 지원하기 위한 프로토콜 변환을 제공합니다.         |
|**UDR**<br><br>통합 데이터 리포지토리     |UDR은 모든 구독자 정보의 수렴된 리포지토리입니다. Fusion Core의 UDR은 일반적으로 4G 시나리오에서 HSS(Home Subscriber Store)에 의해 수행되는 역할을 수행합니다. S6a 인터페이스를 사용하여 MME와 상호 작용합니다.         |

다음 다이어그램에서는 이러한 각 네트워크 기능과 해당 기능이 타사 구성 요소와 상호 운용하는 데 사용하는 인터페이스를 보여 줍니다.

:::image type="content" source="./media/metaswitch-overview/fusion-core-architecture.png" alt-text="Fusion Core 아키텍처":::

Fusion Core는 Fusion Core 기본 VM이라고 하는 VM(가상 머신)에서 제공됩니다. Fusion Core 기본 VM은 ASE(Azure Stack Edge)에 Azure 관리형 애플리케이션으로 배포되도록 디자인됩니다. Fusion Core 기능을 제공하는 데 필요한 네트워크 기능과 인프라 구성 요소는 Fusion Core 기본 VM의 컨테이너로 배포되고 Kubernetes에 의해 오케스트레이션됩니다.

:::image type="content" source="./media/metaswitch-overview/fusion-core-base-vm-azure-stack-edge-with-networking.png" alt-text="Azure Stack Edge의 Fusion Core 기본 VM":::

## <a name="why-use-fusion-core"></a>Fusion Core를 사용하는 이유는 무엇인가요?

### <a name="deployment-in-private-networks"></a>개인 네트워크의 배포

Fusion Core는 Azure 전용 다중 액세스 에지 컴퓨팅의 기능을 사용하여 Azure를 통해 일관되고 중앙화된 관리를 통해 Industry 4.0 사용 사례를 지원하는 데 필요한 Edge 컴퓨팅 리소스의 짧은 대기 시간과 성능을 결합하는 엔터프라이즈용 5G 솔루션을 제공합니다. 전용 다중 액세스 에지 컴퓨팅에 관한 자세한 내용은 [Azure 전용 다중 액세스 에지 컴퓨팅이란?](overview.md)을 참조하세요.

엔터프라이즈 에지에 Fusion Core를 배포하면 서비스를 제공하는 디바이스에 최대한 가까워지므로 같은 위치에서 애플리케이션 로직과 결합되는 경우 로컬 데이터 처리를 통해 대기 시간 수준을 낮추고 역전송을 줄일 수 있습니다. 이는 다음을 포함하여 엔터프라이즈에 많은 중요한 혜택을 제공합니다.

- **자동화** - 자동화된 시스템(예: 로봇)의 명령과 제어 메시지를 실시간으로 처리하여 지연을 방지할 수 있어 생산성이 향상됩니다.
- **원격 분석** - 상태 평가와 자동화된 시스템 작업의 원격 분석 데이터를 실시간으로 처리하여 사고를 방지하고 사이트 안전을 보장할 수 있습니다.
- **분석** - 상당한 양의 운영 및 진단 데이터를 최소 비용으로 전송할 수 있어 중요한 작업이 지연되지 않습니다.

:::image type="content" source="./media/metaswitch-overview/enterprise-edge-latency.png" alt-text="프라이빗 5G 에지의 Fusion Core":::

Fusion Core는 프라이빗 5G 네트워크에서 제공하는 보안 및 고대역폭과 함께 짧은 대기 시간을 이용할 수 있으므로 다음과 같이 Industry 4.0 사용 사례를 지원하기 위한 최적의 위치에 배치됩니다.

- **제조** - 로봇을 사용하여 프로덕션 라인 분석 및 웨어하우스 자동화
- **공공 안전** - 긴급 상황 작업자와 재해 복구 운영자를 위한 모바일 및 연결
- **에너지 및 유틸리티** - 스마트 미터와 네트워크 조각화/제어를 위한 역전송 네트워크
- **방어** - 실시간 분석을 통한 연결된 명령 게시물 및 전쟁터
- **스마트 팜** - 팜 운영을 위한 연결된 장비

### <a name="pure-5g-and-4g-interworking-support"></a>순수 5G 및 4G 상호 연동 지원

Fusion Core는 순수하게 5G 시나리오로 배포할 수 있습니다. 이 경우 Fusion Core는 **5G 독립 실행형 모드** 에서 실행됩니다.

또한 Fusion Core는 4G 상호 연동을 지원하여 클라우드 네이티브 코어를 통해 4G UE에 대한 서비스를 제공합니다. 이를 **4G 모드** 라고 합니다. 개발 가능한 필수 네트워크 운영자의 경우 4G 모드는 4G 코어 유지 관리에 대한 지속적인 투자 없이도 즉시 5G로 전환할 수 있습니다.

### <a name="service-assurance-and-kpi-metrics"></a>서비스 보증 및 KPI 메트릭

Fusion Core는 **Metaswitch의 SAS(Service Assurance Server)** 와 통합되어 NGAP/NAS 메시지와 HTTP 요청 및 응답을 포함한 모든 메시지 트래픽의 실시간 사전 분석을 제공합니다.

Service Assurance Server는 Fusion Core와 관련된 신호 흐름에 관한 자세한 추적을 수집하는 데 사용할 수 있는 웹 GUI를 제공합니다. 이를 사용하여 사용자 서비스에 영향을 주는 많은 일반적인 구성, 네트워크 및 상호 운용성 문제를 진단할 수 있습니다.

:::image type="content" source="./media/metaswitch-overview/service-assurance-server-detailed-timeline.png" alt-text="Service Assurance Server 웹 GUI의 자세한 타임라인 보기":::

Fusion Core는 Prometheus 및 Grafana와 같은 업계 표준 클라우드 네이티브 모니터링 도구를 제공하는 **Metaswitch ServiceIQ Monitoring** 과도 통합되어 시스템 성능, 결함 식별, 문제 해결을 실시간으로 분석할 수 있습니다.

ServiceIQ Monitoring은 Fusion Core VM에서 실행되며 이를 통해 Fusion Core 배포와 관련된 주요 메트릭을 모니터링하는 유연한 방법을 제공하는 다양한 Fusion Core 대시보드에 액세스할 수 있습니다. 또한 경고 발생에 관한 정보를 볼 수 있으므로 새로운 문제에 신속하게 대응할 수 있습니다.


[![Fusion Core 개요 대시보드](media/metaswitch-overview/fusion-core-overview-dashboard.png)](media/metaswitch-overview/fusion-core-overview-dashboard.png#lightbox)

### <a name="other-features"></a>기타 기능

|기능  |Description  |
|---------|---------|
|**네트워크 조각화**     |여러 네트워크 조각을 제공하는 독립적인 논리 네트워크를 멀티플렉싱하도록 Fusion Core 배포를 구성할 수 있습니다. 단일 Fusion Core 배포 내의 모든 네트워크 기능은 구성된 조각을 모두 제공합니다.<br><br>허용된 조각과 기본 조각으로 구독자를 프로비저닝하도록 선택하며 구독자 그룹의 관리용 분리를 유지 관리하는 쉽고 일반적인 메커니즘을 제공하는 조각 관련 정책(QoS)을 정의할 수 있습니다.         |
|**지원되는 5G 절차**     |Fusion Core는 더 광범위한 프라이빗 5G 솔루션의 일부로 작동하는 경우 다음 절차에 대해 3GPP TS 23.502를 준수합니다.<ul><li>UE 등록/등록 취소</li><li>모바일 등록 업데이트/정기 등록 업데이트</li><li>UE 시작 서비스 요청(신호/데이터)</li><li>AN/네트워크 시작 UE 컨텍스트 릴리스</li><li>PDU 세션 설정</li><li>PDU 세션 릴리스</li><li>NG-RAN 노드 간 N2 기반 핸드오버</li><li>Xn 기반 NG-RAN 간 핸드오버</li><li>네트워크 시작 다운링크 데이터 알림/페이징</li>        |
|**UE 인증**     |<ul><li>서비스 네트워크에서 인증 기능을 제공하기 위한 SEAF(Security Anchor Function) 지원.</li><li>SUPI(Subscription Permanent Identifiers), SUCI(Subscription Concealed Identifiers), 5G-GUTI(Globally Unique Temporary Identities)를 사용한 인증.</li><li>UE에 대한 5G-GUTI의 할당 또는 재할당.</li><li>UE와 네트워크 간 상호 인증을 위한 3세대 EAP-AKA(Authentication and Key Agreement) 및 5G-AKA(5G Authentication and Key Agreement ).</li><li>4G 모드로 실행되는 배포에 대한 EPS-AKA(Evolved Packet System 기반 Authentication and Key Agreement).</li>         |
|**UE 보안 컨텍스트 관리**     |Fusion Core의 AMF는 5G NAS의 암호화와 무결성 보호를 수행합니다. UE 등록 중에 UE에는 128비트 키를 사용하는 5G NAS에 대한 보안 기능이 포함됩니다.<br><br>암호화와 무결성 보호를 위해 Fusion Core에서 지원하는 알고리즘은 다음과 같습니다.<ul><li>5GS null 암호화 알고리즘</li><li>128비트 Snow3G</li><li>128비트 AES</li><li>128비트 ZUC</li>        |
|**UE MTU 구성**     |Fusion Core의 SMF는 조각화를 방지하기 위해 PDU 세션 설정 절차의 일부로 요청 시 데이터 네트워크의 MTU를 UE에 신호로 보냅니다.         |
|**정책 제어**     |Fusion Core는 PDU 세션별 및 흐름별로 엔드투엔드 정책 제어 결정과 실행을 적용합니다. 이렇게 하면 특정 흐름이나 UE에 다양한 QoS 수준을 적용하는 데 필요한 유연성이 제공되므로, 서비스나 데이터 네트워크를 선택적으로 UE 그룹에 표시할 수 있고 네트워크를 통해 이동하는 트래픽 유형이 제어됩니다.         |
|**UPF ACL(액세스 제어 목록)**     |ACL을 사용하여 특정 IPv4 주소 범위에 대해 처리되는 트래픽을 허용하거나 차단할 수 있습니다. Fusion Core의 액세스(N3) 및 코어(N6) 인터페이스에서 ACL을 구성할 수 있습니다.         |
|**RFSP(RAT/Frequency Selection Priority)에 대한 인덱스**     |Fusion Core의 AMF는 RFSP 인덱스에 RAN을 제공할 수 있습니다. RAN이 해당 로컬 구성과 일치하면 셀 재선택이나 빈도 계층 리디렉션과 같은 특정 라디오 리소스 관리 정책을 적용할 수 있습니다.         |

## <a name="next-steps"></a>다음 단계

- [Fusion Core를 배포](deploy-metaswitch-fusion-core-solution.md)하는 방법 알아보기

