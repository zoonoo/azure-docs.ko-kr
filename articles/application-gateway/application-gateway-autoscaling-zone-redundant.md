---
title: 자동 크기 조정 및 Azure에서 영역 중복 응용 프로그램 게이트웨이
description: 이 문서에서는 Azure 응용 프로그램 Standard_v2 및 자동 크기 조정 및 영역 중복 기능을 포함 하는 WAF_v2 SKU를 소개 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: 40564e52cbcde0e835ed97132196bf7ed084f5b7
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431206"
---
# <a name="autoscaling-and-zone-redundant-application-gateway"></a>자동 크기 조정 및 영역 중복 응용 프로그램 게이트웨이 

Application Gateway 및 웹 응용 프로그램 방화벽 (WAF) Standard_v2 및 WAF_v2 SKU에서 사용할 수 있습니다. V2 SKU에는 자동 크기 조정, 영역 중복 및 정적 Vip에 대 한 지원 같은 중요 한 새로운 기능에 대 한 지원을 추가 및 성능 향상을 제공 합니다. 표준 및 WAF SKU 아래에서 기존 기능에 나열 된 몇 가지 예외를 사용 하 여 새 v2 SKU에서 지원 되는 데 계속 [비교](#differences-with-v1-sku) 섹션입니다.

새 v2 SKU에는 다음과 같은 향상 기능이 포함 됩니다.

- **자동 크기 조정**: 자동 크기 조정 SKU의 Application Gateway 또는 WAF 배포는 트래픽 부하 패턴의 변화에 따라 강화하거나 축소할 수 있습니다. 또한 자동 크기 조정을 사용하면 프로비전 시 배포 크기 또는 인스턴스 수를 선택할 필요가 없습니다. 이 SKU는 true 탄력성을 제공합니다. Standard_v2 및 WAF_v2 SKU에 Application Gateway에 고정 된 용량 (사용 하지 않도록 설정 자동 크기 조정)와 사용 하도록 설정 하는 자동 크기 조정 모드에서 작동할 수 있습니다. 고정 용량 모드는 워크로드가 일관적이고 예측 가능한 시나리오에 유용합니다. 자동 크기 조정 모드 응용 프로그램 트래픽에서 분산을 참조 하는 응용 프로그램에 유용 합니다.
- **영역 중복**: Application Gateway 또는 WAF 배포를 확장할 수 있습니다 여러 가용성 영역을 Traffic Manager와 함께 각 영역에서 별도 Application Gateway 인스턴스를 프로 비전 할 필요성을 제거. 선택할 수 있습니다 단일 영역 또는 Application Gateway 인스턴스 배포 되는 위치, 여러 영역 영역 오류 복원 력이 있습니다. 애플리케이션에 대한 백 엔드 풀을 가용성 영역 전반에 유사하게 배포할 수 있습니다.

  영역 중복은 Azure 영역 사용할 수 있는 사용할 수 있습니다. 다른 지역의 다른 모든 기능이 지원 됩니다. 자세한 내용은 참조 하세요. [azure에서 가용성 영역은 무엇입니까?](../availability-zones/az-overview.md#services-support-by-region)
- **정적 VIP**: Application gateway v2 SKU에서 정적 VIP를 단독으로 입력합니다. 이 응용 프로그램 게이트웨이에 연결 된 VIP를 다시 시작 후에 배포 수명 주기 동안 변경 되지 않는지 확인 합니다.
- **헤더 재작성**: Application Gateway를 사용 하면 추가, 제거 또는 v2 SKU를 사용 하 여 HTTP 요청 및 응답 헤더를 업데이트할 수 있습니다. 자세한 내용은 참조 하세요. [Application Gateway를 사용 하 여 다시 작성 하는 HTTP 헤더](rewrite-http-headers.md)
- **Key Vault 통합 (미리 보기)** : 응용 프로그램 게이트웨이 v2 사용 하도록 설정 하는 HTTPS 수신기에 연결 된 서버 인증서 (공개 미리 보기)에서 Key Vault를 사용 하 여 통합을 지원 합니다. 자세한 내용은 [Key Vault 인증서를 사용 하 여 SSL 종료](key-vault-certs.md)합니다.
- **Azure Kubernetes Service 수신 컨트롤러 (미리 보기)** : Application Gateway v2 수신 컨트롤러는 Azure Application Gateway를 Kubernetes Service (AKS (Azure) AKS 클러스터 라고는 수신으로 사용 하도록 허용 합니다. 자세한 내용은 참조는 [설명서 페이지](https://azure.github.io/application-gateway-kubernetes-ingress/)합니다.
- **성능 향상**: SKU는 최대 5 X 더 나은 SSL 제공 v2 표준/WAF SKU에 비해 성능 오프 로드 합니다.
- **배포 및 업데이트 시간을 단축** v2 SKU 표준/WAF SKU에 비해 더 빠르게 배포 및 업데이트 시간을 제공 합니다. 또한 WAF 구성 변경 내용을 포함 됩니다.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>지원되는 지역

Standard_v2 및 WAF_v2 SKU는 다음 지역에서 제공 됩니다. 미국 중북부, 미국 중남부, 미국 서부, 미국 서부 2, 미국 동부, 미국 동부 2, 미국 중부, 북유럽, 서유럽, 동남 아시아, 프랑스 중부, 영국 서부, 일본 동부, 일본 서부 추가 지역 나중에 추가 됩니다.

## <a name="pricing"></a>가격

V2 SKU를 사용 하 여 가격 책정 모델을 소비에 의해 좌우 됩니다 및 인스턴스 수 또는 크기에 더 이상 연결 되어 없습니다. V2 SKU 가격의 두 구성 요소:

- **고정된 가격** -이 매시간 (또는 부분 시간 동안) Standard_v2 또는 WAF_v2 게이트웨이 프로 비전 하는 가격입니다.
- **용량 단위 가격은** -고정된 비용 외에 청구 되는 사용량 기반 비용입니다. 용량 단위 요금도 계산 된 시간 또는 부분 매시간. 용량 단위에 3 차원-단위, 영구 연결 및 처리량을 계산 합니다. 단위는 사용 하는 프로세서 용량이의 측정값을 계산 합니다. 계산 단위에 영향을 주는 요인은 TLS 연결/sec, URL 다시 쓰기 계산 및 WAF 규칙 처리입니다. 영구 연결 지정 된 청구 기간에 application gateway에 설정 된 TCP 연결의 측정값을입니다. 처리량은 지정 된 청구 기간에 자동으로 처리 하는 평균 메가 비트/초입니다.

각 용량 단위는 최대 구성 됩니다. 1은 2.22 5mbps 처리량 단위 또는 2500 영구 연결을 계산합니다.

계산 단위 지침:

- **Standard_v2** -각 계산 단위는 RSA 2048 비트 키 TLS 인증서를 사용 하 여 초당 약 50 개의 연결 가능 합니다.
- **WAF_v2** -각 계산 단위 70%를 사용 하 여 트래픽 70 ~ 30% 혼합 2KB 가져오기/게시 하는 보다 작은 요청에 대 한 약 초당 10 개의 동시 요청을 지원할 수 있습니다 하 고 더 높은 남아 있습니다. WAF 성능이 저하 되지 응답 크기에 따라 현재 합니다.

> [!NOTE]
> 현재 각 인스턴스에 약 10 명의 용량 단위로 지원.
> 계산 단위에서 처리할 수 있는 요청 수가 TLS 인증서 키 크기, 키 교환 알고리즘, 헤더 재작성 및 WAF 들어오는 요청 크기의 경우 다양 한 조건에 따라 달라 집니다. 계산 단위 당 요청 속도 파악 하기 위한 응용 프로그램 테스트를 수행 하는 것이 좋습니다. 용량 단위 및 계산 단위를 모두 제공 될 예정를 기준으로 청구 시작 하기 전에 합니다.

다음 표에서 예제 가격을 표시 하 고 용도로 사용 됩니다.

**미국 동부에서 가격**:

|              SKU 이름                             | 고정된 가격 ($/ 시간)  | 용량 단위 가격 ($/ CU-시간)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

추가 가격 정보에 대 한 참조를 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)합니다. 청구는 2019 년 7 월 1 일에 시작 되도록 예약 됩니다.

**예제 1**

응용 프로그램 게이트웨이 Standard_v2 고정된 용량의 5 개의 인스턴스를 사용 하 여 수동 크기 조정 모드에서 자동 크기 조정 없이 프로 비전 됩니다.

고정된 가격 744(hours) = * 0.20 달러 $148.8 = <br>
용량 단위 = 인스턴스당 744 (시간) 10 용량 단위 * 5 개 인스턴스 * $0.008 용량 단위 시간당 $297.6 =

총 요금 = $148.8 + $297.6 $446.4 =

**예제 2**

한 달에 대 한 프로 비전 되는 Application Gateway standard_v2 및 25 새로운 SSL 연결/sec 8.88-Mbps 데이터 전송의 평균을 받으면이 시간 동안입니다. 연결은 수명이 짧으며 가정 하 고, 가격을 사용할 수도 있습니다.

고정된 가격 744(hours) = * 0.20 달러 $148.8 =

용량 단위 가격은 744(hours) = * Max (연결/sec에 대 한 25/50 개 계산 단위, 처리량에 대 한 8.88/2.22 용량 단위) * 0.008 = 744 * 4 * $0.008 $23.81 =

총 요금 = $148.8 + 23.81 $172.61 =

**예 3**

응용 프로그램 게이트웨이 WAF_v2는 한 달에 대 한 프로 비전 됩니다. 이 시간 동안 25 새로운 SSL 연결/sec 8.88-Mbps 데이터 전송의 평균 받고 초당 80 요청 않습니다. 수명이 짧은 연결 가정 하며는 응용 프로그램에 대 한 계산 단위 계산을 지 원하는 10 개 RP 계산 단위 당 가격:

고정된 가격 744(hours) = * $0.36 $267.84 =

용량 단위 가격은 744(hours) = * Max (계산 단위 Max(25/50 for connections/sec, 80/10 WAF RPS), 처리량에 대 한 8.88/2.22 용량 단위) * $0.0144 = 744 * 8 * 0.0144 $85.71 =

총 요금 = $267.84 + $85.71 $353.55 =

## <a name="scaling-application-gateway-and-waf-v2"></a>크기 조정 응용 프로그램 게이트웨이 및 WAF v2

Application Gateway 및 WAF를 구성할 수 있습니다 두 가지 모드 규모를 확장 합니다.

- **자동 크기 조정** -자동 크기 조정을 사용 하도록 설정, Sku를 늘리거나 응용 프로그램 트래픽 요구 사항에 따라 응용 프로그램 게이트웨이 및 WAF v2를 사용 하 여 합니다. 이 모드에는 응용 프로그램 게이트웨이 크기 또는 인스턴스 수를 추측 하지 않아도 및 응용 프로그램에 더 나은 탄력성을 제공 합니다. 이 모드를 사용 하면 예상 된 최대 트래픽 부하에 대 한 프로 비전 된 최대 용량에서 게이트웨이 실행할 수 없게 하 여 비용을 절감할 수 있습니다. 고객은 최소 및 필요에 따라 최대 인스턴스 수를 지정 해야 합니다. Application Gateway 및 WAF v2 트래픽이 없는 경우에도 지정 된 최소 인스턴스 수보다 속하지 않는 보장 하는 최소 용량입니다. 모든 트래픽이 없는 경우에도이 최소 용량에 대해 청구 됩니다. 또한 필요에 따라 지정 된 인스턴스 수를 초과 하는 응용 프로그램 게이트웨이의 확장 되지 않습니다는 보장 하는 최대 인스턴스 수를 지정할 수 있습니다. 게이트웨이에서 제공 하는 트래픽 양에 요금이 계속 합니다. 인스턴스 수를 0에서 125로 까지입니다. 최대 인스턴스 수에 대 한 기본값은 20 이며 지정 하지 않으면.
- **수동** -게이트웨이 자동으로 조정 되지는 수동 모드 또는 선택할 수 있습니다. 이 모드에서는 응용 프로그램 게이트웨이 또는 WAF 처리할 수 있는 것에 비해 트래픽을 많이 있으면 트래픽 손실에서 될 수 있습니다. 수동 모드를 사용 하 여 인스턴스 수를 지정 하는 필수입니다. 인스턴스 수는 1에서 125 인스턴스에 달라질 수 있습니다.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>SKU v1 및 v2 SKU 기능 비교

다음 표에서 각 SKU를 사용 하 여 사용할 수 있는 기능을 비교 합니다.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 자동 확장                                       |          | &#x2713; |
| 영역 중복                                   |          | &#x2713; |
| 정적 VIP                                        |          | &#x2713; |
| Azure Kubernetes Service (AKS) 수신 컨트롤러 |          | &#x2713; |
| Azure Key Vault 통합                       |          | &#x2713; |
| HTTP (S) 헤더를 다시 작성                           |          | &#x2713; |
| URL 기반 라우팅                                 | &#x2713; | &#x2713; |
| 다중 사이트 호스팅                             | &#x2713; | &#x2713; |
| 트래픽 리디렉션                               | &#x2713; | &#x2713; |
| WAF(웹 애플리케이션 방화벽)                    | &#x2713; | &#x2713; |
| SSL(Secure Sockets Layer) 종료            | &#x2713; | &#x2713; |
| 종단 간 SSL 암호화                         | &#x2713; | &#x2713; |
| 세션 선호도                                  | &#x2713; | &#x2713; |
| 사용자 지정 오류 페이지                                | &#x2713; | &#x2713; |
| WebSocket 지원                                 | &#x2713; | &#x2713; |
| HTTP/2 지원                                    | &#x2713; | &#x2713; |
| 연결 드레이닝                               | &#x2713; | &#x2713; |

> [!NOTE]
> SKU는 이제 자동 크기 조정 v2 [기본 상태 프로브](application-gateway-probe-overview.md#default-health-probe) 를 자동으로 해당 백 엔드 풀의 모든 리소스의 상태를 모니터링 하 고 비정상으로 간주 되는 백 엔드 멤버만 강조 표시 합니다. 기본 상태 프로브는 사용자 지정 프로브 구성이 없는 백 엔드에 대 한 자동으로 구성 됩니다. 자세한 내용은 참조 하세요 [application gateway의 상태 프로브](application-gateway-probe-overview.md)합니다.

## <a name="differences-with-v1-sku"></a>V1 SKU와의 차이점

|차이점|세부 정보|
|--|--|
|인증 인증서|지원되지 않습니다.<br>자세한 내용은 [Application Gateway의 엔드투엔드 SSL 개요](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)를 참조하세요.|
|동일한 서브넷에서 Standard_v2와 표준 애플리케이션 게이트웨이 혼합|지원되지 않음|
|Application Gateway 서브넷의 UDR(사용자 정의 경로)|지원되지 않음|
|인바운드 포트 범위에 대한 NSG| - 65200 ~ 65535(Standard_v2 SKU)<br>- 65503 ~ 65534(Standard SKU)<br>자세한 내용은 [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)을 참조하세요.|
|Azure 진단의 성능 로그|지원되지 않습니다.<br>Azure 메트릭을 사용해야 합니다.|
|결제|청구 2019 년 7 월 1 일에 시작 하도록 예약 합니다.|
|FIPS 모드|현재는 지원되지 않습니다.|
|ILB 전용 모드|현재는 지원되지 않습니다. 공용 및 ILB 모드가 함께 지원됩니다.|
|Netwatcher 통합|지원되지 않습니다.|
|Azure 지원 센터 통합|아직 사용할 수 없습니다.

## <a name="migrate-from-v1-to-v2"></a>v1에서 v2로 마이그레이션

Azure PowerShell 스크립트에서 v1 응용 프로그램 게이트웨이/WAF v2 자동 크기 조정 SKU에 마이그레이션할 수 있도록 PowerShell 갤러리에서 제공 됩니다. 이 스크립트를 사용 하면 v1 게이트웨이에서 구성을 복사 합니다. 트래픽 마이그레이션 사용자의 책임입니다. 자세한 내용은 참조 하세요. [마이그레이션할 Azure Application Gateway v1에서 v2로](migrate-v1-v2.md)합니다.
## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure Portal](quick-create-portal.md)
- [Azure PowerShell을 사용하여 예약된 가상 IP 주소로 자동 크기 조정, 영역 중복 애플리케이션 게이트웨이 만들기](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md)에 대해 자세히 알아보세요.
- [Azure Firewall](../firewall/overview.md)에 대해 자세히 알아보세요.