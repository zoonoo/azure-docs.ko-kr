---
title: 가격 책정 이해-Azure 애플리케이션 게이트웨이
description: 이 문서에서는 v1에서 v2 Sku에 대 한 Azure 애플리케이션 게이트웨이 및 웹 응용 프로그램 방화벽에 대 한 청구 프로세스를 설명 합니다.
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 1d88379726cfb6c4218c38b9ccc87005609a9aba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460748"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Azure 애플리케이션 게이트웨이 및 웹 응용 프로그램 방화벽에 대 한 가격 책정 이해

>[!NOTE]
>이 문서에 표시 된 가격은 예제 이며 설명 목적 으로만 사용 됩니다. 해당 지역에 따라 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조 하세요.

Azure 애플리케이션 Gateway는 Azure에서 확장 가능 하 고 가용성이 높고 보안이 유지 되는 웹 응용 프로그램 배달을 가능 하 게 하는 계층 7 부하 분산 솔루션입니다.

Application Gateway와 관련 된 사전 비용이 나 종료 비용은 없습니다.
실제 시간별 사용량에 따라 미리 프로 비전 되 고 사용 되는 리소스에 대해서만 요금이 청구 됩니다. Application Gateway와 관련 된 비용은 고정 비용과 가변 비용의 두 가지 구성 요소로 분류 됩니다. 각 구성 요소 내의 실제 비용은 활용 되는 SKU에 따라 달라 집니다.

이 문서에서는 각 SKU와 관련 된 비용을 설명 하 고 사용자가이 문서를 활용 하 여 Azure 애플리케이션 게이트웨이와 관련 된 비용을 계획 하 고 관리 하는 것이 좋습니다.

## <a name="v1-skus"></a>V1 Sku

표준 Application Gateway 및 WAF V1 Sku는 다음의 조합으로 청구 됩니다.

* 고정 비용

    요청을 처리 하기 위해 특정 유형의 Application Gateway/WAF가 프로 비전 되 고 실행 되는 시간을 기준으로 하는 비용입니다.
    고정 비용 구성 요소는 다음 요소를 고려 합니다.
    * 계층 표준 Application Gateway 또는 WAF
    * 크기-작음, 보통 & 큼
    * 인스턴스 수-배포할 인스턴스 수

    N 인스턴스의 경우 관련 비용은 특정 계층 (표준 & WAF) & 크기 (낮음, 중간 & 큰) 조합의 한 인스턴스의 N * 비용이 됩니다.

* 가변 비용

    Application Gateway/WAF에서 처리 하는 데이터의 양을 기준으로 하는 비용입니다. Application Gateway에서 처리 되는 요청 및 응답 바이트는 모두 대금 청구로 간주 됩니다.

총 비용 = 고정 비용 + 가변 비용

### <a name="standard-application-gateway"></a>표준 Application Gateway

고정 비용 & 변수 비용은 Application Gateway 유형에 따라 계산 됩니다.
다음 표에서는 미국 동부 가격에 대 한 스냅숏을 기반으로 하는 예 가격을 보여 주며,이는 설명 목적 으로만 사용 됩니다.

#### <a name="fixed-cost-east-us-region-pricing"></a>고정 비용 (미국 동부 지역 가격)

|              Application Gateway 형식             |  비용 ($/시간)  |
| ------------------------------------------------- | ---------------|
|                     작음                         |    $0.025      |
|                     중간                        |    $0.07       |
|                     큰                         |    $0.32       |

예상 월별 가격은 월 730 시간의 사용량을 기준으로 합니다.

#### <a name="variable-cost-east-us-region-pricing"></a>가변 비용 (미국 동부 지역 가격)

|              처리 된 데이터             |  작음 ($/GB)  |  보통 ($/GB) |  큼 ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| 처음 10TB/월                       |     $0.008     |      무료      |     무료      |
| 다음 30tb (10 ~ 40tb)/월             |     $0.008     |     $0.007     |     무료      |
| 40 TB 초과/월                        |     $0.008     |     $0.007     |     $0.0035   |

해당 지역에 따라 더 많은 가격 책정 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조 하세요.

### <a name="waf-v1"></a>WAF V1

고정 비용 & 변수 비용은 프로 비전 된 Application Gateway 유형에 따라 계산 됩니다.

다음 표에서는 미국 동부 가격에 대 한 스냅숏을 기반으로 하는 예 가격을 보여 주며,이는 설명을 위한 목적 으로만 사용 됩니다.

#### <a name="fixed-cost-east-us-region-pricing"></a>고정 비용 (미국 동부 지역 가격)

|              Application Gateway 형식             |  비용 ($/시간)  |
| ------------------------------------------------- | ---------------|
|                     작음                         |       해당 없음       |
|                     중간                        |     $0.126     |
|                     큰                         |     $0.448     |

예상 월별 가격은 월 730 시간의 사용량을 기준으로 합니다.

#### <a name="variable-cost-east-us-region-pricing"></a>가변 비용 (미국 동부 지역 가격)

|              처리 된 데이터             |  작음 ($/GB)  |  보통 ($/GB) |  큼 ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| 처음 10TB/월                       |     $0.008     |      무료      |     무료      |
| 다음 30tb (10 ~ 40tb)/월             |     $0.008     |     $0.007     |     무료      |
| 40 TB 초과/월                        |     $0.008     |     $0.007     |     $0.0035   |

해당 지역에 따라 더 많은 가격 책정 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조 하세요.

> [!NOTE]
> 아웃 바운드 데이터 전송-application gateway의 Azure 데이터 센터에서 나가는 데이터는 표준 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구 됩니다.

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>예 1 (a) – 표준 Application Gateway 1 개 인스턴스 수

인스턴스 1 개를 포함 하는 중간 유형의 표준 Application Gateway를 프로 비전 했 고 한 달에 500 GB를 처리 한다고 가정 합니다. 위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

고정 가격 = $0.07 * 730 (시간) = $51.1 월별 예상 가격은 월의 사용에 대 한 730 시간을 기준으로 합니다.

가변 비용 = 무료 (중간 계층에는 매달 처음 10TB 처리 된 비용이 없음) 총 비용 = $51.1 + 0 = $51.1 

> [!NOTE]
> 고가용성 시나리오를 지원 하려면 V1 Sku에 대해 최소 두 개의 인스턴스를 설정 해야 합니다. [Application Gateway에 대 한 SLA를](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/) 참조 하세요.

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>예 1 (b) – > 1 인스턴스 수의 표준 Application Gateway

5 개의 인스턴스를 포함 하는 보통 형식의 표준 Application Gateway를 프로 비전 했 고 한 달에 500 GB를 처리 한다고 가정 합니다. 위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

고정 가격 = 5 (인스턴스 수) * $0.07 * 730 (시간) = $255.5 월 예상 가격은 월의 사용량에 대 한 730 시간을 기준으로 합니다.

가변 비용 = 무료 (중간 계층에는 매달 처음 10TB 처리 된 비용이 없음) 총 비용 = $255.5 + 0 = $255.5 

### <a name="example-2--waf-application-gateway"></a>예 2 – WAF Application Gateway

작은 형식 표준 Application Gateway를 프로 비전 하 고 해당 월의 처음 15 일 동안에는 작은 형식 WAF f를 프로 비전 했다고 가정 하겠습니다. 소형 응용 프로그램 게이트웨이는 활성 상태인 동안 15 TB를 처리 하 고, 초대형 WAF 응용 프로그램 게이트웨이는 활성 상태인 동안 100 TB를 처리 합니다. 위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다. 

###### <a name="small-instance-standard-application-gateway"></a>Small instance Standard Application Gateway

24 시간 * 15 일 = 360 시간

고정 가격 = $0.025 * 360 (시간) = $9

가변 비용 = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

총 비용 = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>대량 인스턴스 WAF Application Gateway
24 시간 * 15 일 = 360 시간

고정 가격 = $0.448 * 360 (시간) = $161.28

가변 비용 = 60 * 1000 * $0.0035/GB = $210 (대량 계층에는 매월 처리 된 첫 번째 40 TB에 대 한 비용이 없음)

총 비용 = $161.28 + $210 = $371.28

## <a name="v2-skus"></a>V2 Sku  

Application Gateway V2 및 WAF V2 Sku는 자동 크기 조정을 지원 하 고 기본적으로 고가용성을 보장 합니다.

### <a name="key-terms"></a>주요 용어

##### <a name="capacity-unit"></a>용량 단위 
용량 단위는 여러 매개 변수에서 Application Gateway의 용량 사용률을 측정 한 것입니다.

단일 용량 단위는 다음 매개 변수로 구성 됩니다.
* 2500 영구 연결
* 2.22-Mbps 처리량
* 계산 단위 1 개

이러한 매개 변수 중 하나를 초과 하는 경우 다른 두 매개 변수가이 단일 용량 단위 제한을 초과 하지 않는 경우에도 다른 n 개의 용량 단위가 필요 합니다.
위의 세 중에서 가장 높은 사용률을 가진 매개 변수는 용량 단위를 계산 하는 데 내부적으로 사용 되며이는 요금이 청구 됩니다.

##### <a name="compute-unit"></a>계산 단위
Compute 단위는 사용 된 계산 용량의 측정 단위입니다. 계산 단위 소비에 영향을 주는 요소는 TLS 연결/초, URL 재작성 계산 및 WAF 규칙 처리입니다. 계산 단위에서 처리할 수 있는 요청 수는 TLS 인증서 키 크기, 키 교환 알고리즘, 헤더 다시 쓰기 및 WAF 들어오는 요청 크기의 경우와 같은 다양 한 조건에 따라 달라 집니다.

컴퓨팅 단위 지침:
* Standard_v2 - 각 컴퓨팅 단위는 RSA 2048비트 키 TLS 인증서를 사용하여 초당 약 50개의 연결을 사용할 수 있습니다.

* WAF_v2 - 각 컴퓨팅 단위는 70% 요청은 2 KB GET/POST보다 작고 나머지는 더 높은 70-30%의 트래픽 혼합에서 초당 약 10개의 동시 요청을 지원할 수 있습니다. WAF 성능은 현재 응답 크기의 영향을 받지 않습니다.

##### <a name="instance-count"></a>인스턴스 수 
Application Gateway V2 Sku에 대 한 리소스의 사전 프로 비전은 인스턴스 수 측면에서 정의 됩니다. 각 인스턴스는 처리 기능 측면에서 최소 10 개의 용량 단위를 보장 합니다. 동일한 인스턴스는 용량 단위 매개 변수에 따라 서로 다른 트래픽 패턴에 대해 10 개 이상의 용량 단위를 지원할 수도 있습니다.

자동 크기 조정 (최소 또는 최대)에 대해 수동으로 정의 된 크기 조정 및 제한 집합은 인스턴스 개수를 기준으로 설정 됩니다. 자동 크기 조정 구성에서 인스턴스 수 및 최소 인스턴스 수에 대 한 크기를 수동으로 설정 하면 10 개의 용량 단위/인스턴스가 예약 됩니다. 이러한 예약 된 CUs는 실제 리소스 소비에 관계 없이 Application Gateway 활성 상태인 경우에만 청구 됩니다. 실제 소비가 10 개의 용량 단위/인스턴스 임계값을 초과 하는 경우 변수 구성 요소에서 추가 용량 단위를 청구 합니다.

V2 Sku는 다음 두 부분으로 구성 된 사용량과 그 구성에 따라 청구 됩니다.

* 고정 비용

    Application Gateway V2/WAF V2가 프로 비전 되 고 요청을 처리 하는 데 사용할 수 있는 시간을 기준으로 하는 비용입니다. 이렇게 하면 항상 자동 크기 조정의 일부로 최소 인스턴스 수에 0을 지정 하 여 0 개의 인스턴스가 예약 된 경우에도 고가용성이 보장 됩니다. 
    
    고정 비용에는 Application Gateway에 연결 된 공용 IP와 관련 된 비용도 포함 됩니다.

    언제 든 지 실행 되는 인스턴스 수는 V2 Sku에 대 한 고정 비용의 요소로 간주 되지 않습니다. Standard_V2 또는 WAF_V2의 고정 비용은 동일한 Azure 지역 내에서 실행 되는 인스턴스 수에 관계 없이 시간 마다 동일 합니다.

* 용량 단위 비용 

    들어오는 요청을 처리 하기 위해 필요에 따라 예약 하거나 사용한 용량 단위 수를 기준으로 하는 비용입니다.  소비 기반 비용은 시간별로 계산 됩니다.

총 비용 = 고정 비용 + 용량 단위 비용

> [!NOTE]
> 부분 시간은 전체 시간으로 청구 됩니다.

다음 표에서는 미국 동부 가격에 대 한 스냅숏을 기반으로 하는 예 가격을 보여 주며,이는 설명을 위한 목적 으로만 사용 됩니다.

#### <a name="fixed-costs-east-us-region-pricing"></a>고정 비용 (미국 동부 지역 가격)

|              V2 SKU             |  비용 ($/시간)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0.246     |
|              WAF_V2             |     $0.443     |

예상 월별 가격은 월 730 시간의 사용량을 기준으로 합니다.

#### <a name="variable-costs-east-us-region-pricing"></a>가변 비용 (미국 동부 지역 가격)

|        용량 단위         |  Standard_V2 ($/시간)  |  WAF_V2 ($/시간) |
| ---------------------------- | -------------------- | -------------- |
|             CU 1             |        $0.008        |     $0.0144    |

해당 지역에 따라 더 많은 가격 책정 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조 하세요.

> [!NOTE]
> 아웃 바운드 데이터 전송-application gateway의 Azure 데이터 센터에서 나가는 데이터는 표준 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구 됩니다.

### <a name="example-1-a--manual-scaling"></a>예 1 (a) – 수동 크기 조정 
수동 크기 조정이 한 달 동안 8 개의 인스턴스로 설정 된 Standard_V2 Application Gateway를 프로 비전 했다고 가정 하겠습니다. 이 시간 동안에는 평균 88.8 Mbps 데이터 전송을 수신 합니다.

위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

1 CU 2.22 Mbps 처리량을 처리할 수 있습니다.

CUs는 88.8 Mbps = 88.8/2.22 = 40 CUs를 처리 하는 데 필요 합니다. 

사전 프로 비전 된 CUs = 8 (인스턴스 수) * 10 = 80 

80 (예약 된 용량) > 40 (필요한 용량) 이므로 추가 CUs가 필요 하지 않습니다. 

고정 가격 = $0.246 * 730 (시간) = $179.58

가변 비용 = $0.008 * 8 (인스턴스 단위) * 10 (용량 단위) * 730 (시간) = $467.2

총 비용 = $179.58 + $467.2 = $646.78

![수동 크기 조정 1의 다이어그램입니다.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>예 1 (b) – 프로 비전 된 용량을 초과 하는 트래픽으로 수동 크기 조정

수동 크기 조정이 한 달 동안 3 개의 인스턴스로 설정 된 Standard_V2 Application Gateway를 프로 비전 했다고 가정 하겠습니다. 이 시간 동안에는 평균 88.8 Mbps 데이터 전송을 수신 합니다.

위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

1 CU 2.22 Mbps 처리량을 처리할 수 있습니다.

CUs는 88.8 Mbps = 88.8/2.22 = 40를 처리 하는 데 필요 합니다. 

미리 프로 비전 된 CUs = 3 (인스턴스 수) * 10 = 30 

40 (필요한 용량) > 30 (예약 된 용량) 이므로 추가 CUs가 필요 합니다.
추가 된 CUs의 수는 각 인스턴스에서 사용 가능한 사용 가능한 용량에 따라 달라 집니다.

10 개의 추가 CUs에 해당 하는 용량을 3 개의 예약 된 인스턴스 내에서 사용할 수 있습니다.

고정 가격 = $0.246 * 730 (시간) = $179.58

가변 비용 = $0.008 * (3 (인스턴스 단위) * 10 (용량 단위) + 5 (추가 용량 단위)) * 730 (시간) = $204.4

총 비용 = $179.58 + $204.4 = $383.98

![수동 확장 2의 다이어그램입니다.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> 수동 크기 조정을 수행 하는 경우 예약 된 인스턴스의 최대 처리 용량을 초과 하는 추가 요청은 응용 프로그램의 가용성에 영향을 줄 수 있습니다. 부하가 높은 상황에서 예약 된 인스턴스는 들어오는 요청의 구성과 유형에 따라 처리 용량의 10 개 이상의 용량을 제공할 수 있습니다. 그러나 트래픽 요구 사항에 따라 인스턴스 수를 프로 비전 하는 것이 좋습니다.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>예 2 – 자동 크기 조정을 사용 하는 WAF_V2 인스턴스

자동 크기 조정을 사용 하는 WAF_V2를 프로 비전 하 고 전체 월에 대해 최소 인스턴스 수를 6으로 설정 했다고 가정해 보겠습니다. 요청 로드로 인해 WAF 인스턴스가 확장 되 고 전체 월에 65 용량 단위 (5 개의 용량 단위, 60 단위는 예약 됨)를 활용 했습니다.
위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

예상 월별 가격은 월 730 시간의 사용량을 기준으로 합니다.

고정 가격 = $0.443 * 730 (시간) = $323.39

가변 비용 = $0.0144 * 65 (용량 단위) * 730 (시간) = $683.28

총 비용 = $323.39 + $683.28 = $1006.67

![자동 크기 조정 2의 다이어그램입니다.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Application Gateway에 대해 관찰 되는 실제 트래픽은 이러한 상수 패턴을 가질 가능성이 거의 없으며, Application Gateway에서 관찰 된 부하가 실제 사용량에 따라 변동 됩니다.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>예 3 (a) – 자동 크기 조정 및 0 최소 배율 구성의 WAF_V2 인스턴스

자동 크기 조정을 사용 하는 WAF_V2를 프로 비전 하 고 전체 월에 대해 최소 인스턴스 수를 0으로 설정 했다고 가정 하겠습니다. WAF에 대 한 요청 로드는 최소 이지만 매월 전체 시간에 대해 일관 되 게 제공 됩니다. 부하가 단일 용량 단위의 용량 보다 낮은 경우
위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

예상 월별 가격은 월 730 시간의 사용량을 기준으로 합니다.

고정 가격 = $0.443 * 730 (시간) = $323.39

가변 비용 = $0.0144 * 1 (용량 단위) * 730 (시간) = $10.512

총 비용 = $323.39 + $10.512 = $333.902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>예 3 (b) – 자동 크기 조정을 사용 하는 WAF_V2 인스턴스 (0 분 인스턴스 수)

자동 크기 조정을 사용 하는 WAF_V2를 프로 비전 하 고 전체 월에 대해 최소 인스턴스 수를 0으로 설정 했다고 가정 하겠습니다. 그러나 한 달 동안 WAF 인스턴스로 전달 되는 트래픽이 0 개 있습니다.
위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

고정 가격 = $0.443 * 730 (시간) = $323.39

가변 비용 = $0.0144 * 0 (용량 단위) * 730 (시간) = $0

총 비용 = $323.39 + $0 = $323.39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>예 3 (C)-수동 크기 조정이 1 인스턴스로 설정 된 WAF_V2 인스턴스

WAF_V2를 프로 비전 한 후 전체 월에 허용 되는 최소 값인 1 개 인스턴스를 사용 하 여 수동 크기 조정으로 설정 했다고 가정해 보겠습니다. 그러나 한 달 동안 WAF로 전달 된 트래픽이 0 개 있습니다.
위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

예상 월별 가격은 월 730 시간의 사용량을 기준으로 합니다.

고정 가격 = $0.443 * 730 (시간) = $323.39

가변 비용 = $0.0144 * 1 (인스턴스 수) * 10 (용량 단위) * 730 (시간) = $105.12

총 비용 = $323.39 + $105.12 = $428.51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>예제 4 – 자동 크기 조정 기능을 사용 하는 WAF_V2 용량 단위 계산

자동 크기 조정을 사용 하는 WAF_V2를 프로 비전 하 고 전체 월에 대해 최소 인스턴스 수를 0으로 설정 했다고 가정 하겠습니다. 이 시간 동안에는 평균 8.88 Mbps의 데이터 전송으로 25 개의 새로운 TLS 연결을 받습니다.
위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

예상 월별 가격은 월 730 시간의 사용량을 기준으로 합니다.

고정 가격 = $0.443 * 730 (시간) = $323.39

가변 비용 = $0.0144 * 730 (시간) * {Max (25/50, 8.88/2.22)} = $23.36 (8.88 Mbps를 처리 하는 데 필요한 용량 단위 4)

총 비용 = $323.39 + $23.36 = $346.75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>예 5 (a) – 자동 크기 조정, 시간 기반 계산 Standard_V2

자동 크기 조정을 사용 하는 standard_V2를 프로 비전 하 고 최소 인스턴스 수를 0으로 설정 하 고이 응용 프로그램 게이트웨이가 2 시간 동안 활성 상태 라고 가정해 보겠습니다.
1 시간 동안 10 개의 용량 단위로 처리할 수 있는 트래픽을 수신 하 고, 두 번째 시간 동안 부하를 처리 하는 데 20 개의 용량 단위가 필요한 트래픽을 수신 합니다.
위에서 설명한 가격 책정을 사용 하 여 Application Gateway 비용은 다음과 같이 계산 됩니다.

고정 가격 = $0.246 * 2 (시간) = $0.492

가변 비용 = $0.008 * 10 (용량 단위) * 1 (시간) + $0.008 * 20 (용량 단위) * 1 (시간) = $0.24

총 비용 = $0.492 + $0.24 = $0.732


## <a name="monitoring-billed-usage"></a>청구 사용량 모니터링

**모니터링** 섹션에서 Application Gateway 메트릭의 일부로 사용 되는 용량 단위 뿐만 아니라 다양 한 매개 변수 (compute 단위, 처리량 & 영구 연결)에 대 한 사용량의 양을 볼 수 있습니다.

![메트릭 섹션의 다이어그램입니다.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>비용 예측에 유용한 메트릭

* 현재 용량 단위

    세 개의 매개 변수 (현재 연결, 처리량 및 계산 단위)에서 트래픽 부하를 분산 하는 데 사용 된 용량 단위 수

* 고정 청구 가능 용량 단위

    Application Gateway 구성에서 최소 인스턴스 수 설정 (한 인스턴스는 10 개의 용량 단위로 변환)에 따라 프로 비전 유지 된 최소 용량 단위 수입니다.

* 예상 청구 용량 단위

    예상 청구 용량 단위는 청구 비용을 산정하는 데 사용되는 용량 단위의 수를 나타냅니다. 이 값은 현재 용량 단위(트래픽 부하를 분산하는 데 필요한 용량 단위)와 고정 청구 가능 용량 단위(프로비저닝된 최소 용량 단위) 중에서 더 큰 값으로 계산됩니다.

처리량, 현재 연결 및 계산 단위와 같은 더 많은 메트릭은 병목 상태를 이해 하 고 필요한 용량 단위 수를 예측 하는 데에도 사용할 수 있습니다. 자세한 정보는 [Application Gateway 메트릭에](application-gateway-metrics.md) 제공 됩니다.

#### <a name="example---estimating-capacity-units-being-utilized"></a>예-사용 중인 용량 단위 예상

**관찰 된 메트릭:**

* Compute 단위 = 17.38
* 처리량 = 1.37 M 바이트/초-10.96 Mbps
* 현재 연결 = 123.08 k
* 계산 된 용량 단위 = max (17.38, 10.96/2.22, 123.08 k/2500) = 49.232

메트릭의 관찰 된 용량 단위 = 49.23

## <a name="next-steps"></a>다음 단계

Azure 애플리케이션 게이트웨이에서 가격 책정에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Azure 애플리케이션 Gateway 가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Azure 애플리케이션 Gateway 가격 계산기](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
