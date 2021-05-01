---
title: 가격 책정 이해 - Azure Application Gateway
description: 이 문서에서는 v1에서 v2 SKU용 Azure Application Gateway 및 Web Application Firewall의 청구 프로세스를 설명합니다.
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601614"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Azure Application Gateway 및 Web Application Firewall의 가격 책정 이해

>[!NOTE]
>이 문서에 표시된 가격은 예제이며 예시용으로만 사용됩니다. 해당 지역에 따른 가격 책정 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조하세요.

Azure Application Gateway는 Azure에서 확장 가능하고, 가용성이 높고, 보안이 유지되는 웹 애플리케이션을 제공할 수 있는 계층 7 부하 분산 솔루션입니다.

Application Gateway와 관련된 초기 비용 또는 종료 비용은 없습니다.
실제 시간별 사용량에 따라 사전 프로비전되고 활용된 리소스에 대해서만 요금이 청구됩니다. Application Gateway와 관련된 비용은 고정 비용과 가변 비용의 두 가지 구성 요소로 분류됩니다. 각 구성 요소 내의 실제 비용은 활용되는 SKU에 따라 달라집니다.

이 문서에서는 각 SKU와 관련된 비용을 설명합니다. 사용자는 이 문서를 활용하여 Azure Application Gateway와 관련된 비용을 계획하고 관리하는 것이 좋습니다.

## <a name="v1-skus"></a>V1 SKU

표준 Application Gateway 및 WAF V1 SKU는 다음의 합계로 청구됩니다.

* 고정 비용

    특정 유형의 Application Gateway/WAF가 프로비전되고 요청을 처리하기 위해 실행하는 시간을 기준으로 한 비용입니다.
    고정 비용 구성 요소는 다음 요인을 고려합니다.
    * 계층 - 표준 Application Gateway 또는 WAF
    * 크기 - 소, 중간, 대
    * 인스턴스 수 - 배포할 인스턴스 수

    N 인스턴스의 경우 관련 비용은 특정 계층(표준 및 WAF)과 크기(소, 중간, 대)를 조합한 하나의 인스턴스의 N * 비용이 됩니다.

* 가변 비용

    Application Gateway/WAF에서 처리하는 데이터의 양을 기준으로 한 비용입니다. Application Gateway에서 처리되는 요청 및 응답 바이트는 모두 청구용으로 간주됩니다.

총 비용 = 고정 비용 + 가변 비용

### <a name="standard-application-gateway"></a>표준 Application Gateway

고정 비용 및 가변 비용은 Application Gateway 유형에 따라 계산됩니다.
다음 표는 미국 동부 가격 책정에 대한 스냅샷을 기반으로 하는 예제 가격을 보여 주며, 예시용으로만 사용됩니다.

#### <a name="fixed-cost-east-us-region-pricing"></a>고정 비용(미국 동부 지역 가격 책정)

|              Application Gateway 유형             |  비용($/시간)  |
| ------------------------------------------------- | ---------------|
|                     소                         |    $0.025      |
|                     중간                        |    $0.07       |
|                     대                         |    $0.32       |

월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

#### <a name="variable-cost-east-us-region-pricing"></a>가변 비용(미국 동부 지역 가격 책정)

|              처리된 데이터             |  소($/GB)  |  중간($/GB) |  대($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| 처음 10TB/월                       |     $0.008     |      무료      |     무료      |
| 다음 30TB(10-40TB)/월             |     $0.008     |     $0.007     |     무료      |
| 40TB 초과/월                        |     $0.008     |     $0.007     |     $0.0035   |

해당 지역에 따른 가격 책정 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조하세요.

### <a name="waf-v1"></a>WAF V1

고정 비용 및 가변 비용은 프로비전된 Application Gateway 유형에 따라 계산됩니다.

다음 표는 미국 동부 가격 책정에 대한 스냅샷을 기반으로 하는 예제 가격을 보여 주며, 예시용으로만 사용됩니다.

#### <a name="fixed-cost-east-us-region-pricing"></a>고정 비용(미국 동부 지역 가격 책정)

|              Application Gateway 유형             |  비용($/시간)  |
| ------------------------------------------------- | ---------------|
|                     소                         |       해당 없음       |
|                     중간                        |     $0.126     |
|                     대                         |     $0.448     |

월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

#### <a name="variable-cost-east-us-region-pricing"></a>가변 비용(미국 동부 지역 가격 책정)

|              처리된 데이터             |  소($/GB)  |  중간($/GB) |  대($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| 처음 10TB/월                       |     $0.008     |      무료      |     무료      |
| 다음 30TB(10-40TB)/월             |     $0.008     |     $0.007     |     무료      |
| 40TB 초과/월                        |     $0.008     |     $0.007     |     $0.0035   |

해당 지역에 따른 가격 책정 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조하세요.

> [!NOTE]
> 아웃바운드 데이터 전송 - Application Gateway의 Azure 데이터 센터에서 나가는 데이터는 표준 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구됩니다.

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>예제 1(a) – 인스턴스가 1개인 표준 Application Gateway

인스턴스가 1개인 중간 유형의 표준 Application Gateway를 프로비전했고 한 달에 500GB를 처리한다고 가정합니다. 위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

고정 가격 = $0.07 * 730(시간) = $51.1. 월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

가변 비용 = 무료(중간 계층에는 월별 처리되는 처음 10TB에 대한 비용이 없음). 총 비용 = $51.1 + 0 = $51.1 

> [!NOTE]
> 고가용성 시나리오를 지원하려면 V1 SKU에 대해 최소 인스턴스 2개를 설정해야 합니다. [Application Gateway용 SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)를 참조하세요.

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>예제 1(b) – 인스턴스가 2개 이상인 표준 Application Gateway

인스턴스가 5개인 중간 유형의 표준 Application Gateway를 프로비전했고 한 달에 500GB를 처리한다고 가정합니다. 위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

고정 가격 = 5(인스턴스 수) * $0.07 * 730(시간) = $255.5. 월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

가변 비용 = 무료(중간 계층에는 월별 처리되는 처음 10TB에 대한 비용이 없음). 총 비용 = $255.5 + 0 = $255.5 

### <a name="example-2--waf-application-gateway"></a>예제 2 - WAF Application Gateway

소형 유형의 표준 Application Gateway를 프로비전하고 해당 월의 처음 15일 동안에는 대형 유형의 WAF를 프로비전했다고 가정합니다. 소형 애플리케이션 게이트웨이는 활성 상태인 동안 15TB를 처리하고, 대형 WAF 애플리케이션 게이트웨이는 활성 상태인 동안 100TB를 처리합니다. 위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다. 

###### <a name="small-instance-standard-application-gateway"></a>소형 인스턴스 표준 Application Gateway

24시간 * 15일 = 360시간

고정 가격 = $0.025 * 360(시간) = $9

가변 비용 = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

총 비용 = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>대형 인스턴스 WAF Application Gateway
24시간 * 15일 = 360시간

고정 가격 = $0.448 * 360(시간) = $161.28

가변 비용 = 60 * 1000 * $0.0035/GB = $210(대형 계층에는 월별 처리되는 처음 40TB에 대한 비용이 없음)

총 비용 = $161.28 + $210 = $371.28

## <a name="v2-skus"></a>V2 SKU  

Application Gateway V2 및 WAF V2 SKU는 자동 크기 조정을 지원하고 기본적으로 고가용성을 보장합니다.

### <a name="key-terms"></a>주요 용어

##### <a name="capacity-unit"></a>용량 단위 
용량 단위는 여러 매개 변수에 걸쳐 Application Gateway의 용량 활용도를 측정한 값입니다.

단일 용량 단위는 다음 매개 변수로 구성됩니다.
* 영구 연결 2500개
* 2.22Mbps 처리량
* 컴퓨팅 단위 1개

이 매개 변수들 중 하나라도 초과하는 경우 다른 두 매개 변수가 이 단일 용량 단위 제한을 초과하지 않는 경우에도 다른 n개의 용량 단위가 필요합니다.
위의 세 매개 변수 중에서 활용도가 가장 높은 매개 변수는 내부적으로 용량 단위를 계산하는 데 사용되며 이는 차례로 청구됩니다.

##### <a name="compute-unit"></a>컴퓨팅 단위
컴퓨팅 단위는 사용한 컴퓨팅 용량을 측정한 값입니다. 컴퓨팅 단위 사용량에 영향을 주는 요인은 TLS 연결/초, URL 다시 쓰기 계산 및 WAF 규칙 처리입니다. 컴퓨팅 단위에서 처리할 수 있는 요청 수는 TLS 인증서 키 크기, 키 교환 알고리즘, 헤더 다시 쓰기 및 WAF 수신 요청 크기 등의 다양한 기준에 따라 달라집니다.

컴퓨팅 단위 지침:
* Standard_v2 - 각 컴퓨팅 단위는 RSA 2048비트 키 TLS 인증서를 사용하여 초당 약 50개의 연결을 사용할 수 있습니다.

* WAF_v2 - 각 컴퓨팅 단위는 70% 요청은 2 KB GET/POST보다 작고 나머지는 더 높은 70-30%의 트래픽 혼합에서 초당 약 10개의 동시 요청을 지원할 수 있습니다. WAF 성능은 현재 응답 크기의 영향을 받지 않습니다.

##### <a name="instance-count"></a>인스턴스 수 
Application Gateway V2 SKU에 대한 리소스의 사전 프로비전은 인스턴스 수 측면에서 정의됩니다. 각 인스턴스는 처리 기능 측면에서 최소 10개의 용량 단위를 보장합니다. 동일한 인스턴스는 용량 단위 매개 변수에 따라 서로 다른 트래픽 패턴에 대해 잠재적으로 10개 이상의 용량 단위를 지원할 수도 있습니다.

자동 크기 조정(최소 또는 최대)에 대해 수동으로 정의된 크기 조정 및 제한 집합은 인스턴스 수 측면에서 설정됩니다. 자동 크기 조정 구성에서 인스턴스 수 및 최소 인스턴스 수에 대한 크기 조정을 수동으로 설정하면 10개의 용량 단위/인스턴스가 예약됩니다. 이러한 예약된 CU는 실제 리소스 사용량에 관계 없이 Application Gateway가 활성 상태인 경우에만 청구됩니다. 실제 사용량이 10개의 용량 단위/인스턴스 임계값을 초과하는 경우 변수 구성 요소에서 추가 용량 단위가 청구됩니다.

V2 SKU는 사용량에 따라 청구되며 다음 두 부분으로 구성됩니다.

* 고정 비용

    Application Gateway V2/WAF V2가 프로비전되고 요청을 처리하는 데 사용할 수 있는 시간에 따른 비용입니다. 이렇게 하면 자동 크기 조정의 일부로 최소 인스턴스 수에 0을 지정하여 0개의 인스턴스가 예약되어도 고가용성이 보장됩니다. 
    
    고정 비용에는 Application Gateway에 연결된 공용 IP에 관한 비용도 포함됩니다.

    특정 시점에 실행되는 인스턴스의 수는 V2 SKU의 고정 비용 요인으로 간주되지 않습니다. Standard_V2 또는 WAF_V2의 고정 비용은 동일한 Azure 지역 내에서 실행되는 인스턴스 수에 관계 없이 시간당 동일합니다.

* 용량 단위 비용 

    수신한 요청을 처리하기 위해 필요에 따라 예약하거나 추가로 활용한 용량 단위 수에 따른 비용입니다.  사용량 기반 비용은 시간별로 계산됩니다.

총 비용 = 고정 비용 + 용량 단위 비용

> [!NOTE]
> 부분 시간은 전체 시간으로 청구됩니다.

다음 표는 미국 동부 가격 책정에 대한 스냅샷을 기반으로 하는 예제 가격을 보여 주며, 예시용으로만 사용됩니다.

#### <a name="fixed-costs-east-us-region-pricing"></a>고정 비용(미국 동부 지역 가격 책정)

|              V2 SKU             |  비용($/시간)  |
| ------------------------------- | ---------------|
|            Standard_v2          |     $0.246     |
|              WAF_v2             |     $0.443     |

월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

#### <a name="variable-costs-east-us-region-pricing"></a>가변 비용(미국 동부 지역 가격 책정)

|        용량 단위         |  Standard_V2($/시간)  |  WAF_V2($/시간) |
| ---------------------------- | -------------------- | -------------- |
|             CU 1개             |        $0.008        |     $0.0144    |

해당 지역에 따른 가격 책정 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조하세요.

> [!NOTE]
> 아웃바운드 데이터 전송 - Application Gateway의 Azure 데이터 센터에서 나가는 데이터는 표준 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구됩니다.

### <a name="example-1-a--manual-scaling"></a>예제 1(a) – 수동 크기 조정 
전체 한 달 동안 수동 크기 조정이 8개 인스턴스로 설정된 Standard_V2 Application Gateway를 프로비전했다고 가정합니다. 이 시간 동안에는 평균 88.8Mbps 데이터 전송을 수신합니다.

위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

CU 1개는 2.22Mbps 처리량을 처리할 수 있습니다.

88.8Mbps = 88.8/2.22 = CU 40개를 처리하는 데 필요한 CU 

사전 프로비전된 CU = 8(인스턴스 수) * 10 = 80 

80(예약된 용량) > 40(필요한 용량)이므로 추가 CU가 필요하지 않습니다. 

고정 가격 = $0.246 * 730(시간) = $179.58

가변 비용 = $0.008 * 8(인스턴스 단위) * 10(용량 단위) * 730(시간) = $467.2

총 비용 = $179.58 + $467.2 = $646.78

![수동 크기 조정 다이어그램 1.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>예제 1(b) – 프로비전된 용량을 초과하는 트래픽으로 수동 크기 조정

전체 한 달 동안 수동 크기 조정이 3개 인스턴스로 설정된 Standard_V2 Application Gateway를 프로비전했다고 가정합니다. 이 시간 동안에는 평균 88.8Mbps 데이터 전송을 수신합니다.

위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

CU 1개는 2.22Mbps 처리량을 처리할 수 있습니다.

88.8Mbps = 88.8/2.22 = 40을 처리하는 데 필요한 CU 

사전 프로비전된 CU = 3(인스턴스 수) * 10 = 30 

40(예약된 용량) > 30(필요한 용량)이므로 추가 CU가 필요하지 않습니다.
추가로 활용된 CU 수는 각 인스턴스에서 사용 가능한 여유 용량에 따라 달라집니다.

10개의 추가 CU에 해당하는 처리 용량을 3개의 예약된 인스턴스 내에서 사용할 수 있는 경우.

고정 가격 = $0.246 * 730(시간) = $179.58

가변 비용 = $0.008 * (3(인스턴스 단위) * 10(용량 단위) + 10(추가 용량 단위)) * 730(시간) = $233.6

총 비용 = $179.58 + $233.6 = $413.18

하지만 7개의 추가 CU에 해당하는 처리 용량만 3개의 예약된 인스턴스 내에서 사용할 수 있는 경우.
이 시나리오에서 Application Gateway 리소스는 크기가 작게 조정되며 잠재적으로 대기 시간이 늘어나거나 요청이 삭제될 수 있습니다.

고정 가격 = $0.246 * 730(시간) = $179.58

가변 비용 = $0.008 * (3(인스턴스 단위) * 10(용량 단위) + 7(추가 용량 단위)) * 730(시간) = $216.08

총 비용 = $179.58 + $216.08 = $395.66


![수동 크기 조정 다이어그램 2.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> 수동 크기 조정을 수행하는 경우 예약된 인스턴스의 최대 처리 용량을 초과하는 추가 요청은 애플리케이션 가용성에 영향을 줄 수 있습니다. 부하가 많은 상황에서 예약된 인스턴스는 수신 요청의 구성 및 유형에 따라 10개 이상의 처리 용량 단위를 제공할 수 있습니다. 그러나 트래픽 요구 사항에 따라 인스턴스 수를 프로비전하는 것이 좋습니다.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>예제 2 – 자동 크기 조정을 사용하는 WAF_V2 인스턴스

자동 크기 조정을 사용하는 WAF_V2를 프로비전하고 전체 한 달 동안 최소 인스턴스 수를 6으로 설정했다고 가정합니다. 요청 부하로 인해 전체 한 달 동안 WAF 인스턴스는 65개의 용량 단위(5개가 스케일 아웃되고 60개가 예약됨)를 스케일 아웃하여 활용하게 되었습니다.
위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

고정 가격 = $0.443 * 730(시간) = $323.39

가변 비용 = $0.0144 * 65(용량 단위) * 730(시간) = $683.28

총 비용 = $323.39 + $683.28 = $1006.67

![자동 크기 조정 다이어그램 2.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Application Gateway에 대해 관찰되는 실제 트래픽은 거의 트래픽 패턴이 일정하지 않으며, Application Gateway에서 관찰된 부하는 실제 사용량에 따라 변동됩니다.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>예제 3(a) – 자동 크기 조정 및 0개의 최소 크기 조정이 구성된 WAF_V2 인스턴스

자동 크기 조정을 사용하는 WAF_V2를 프로비전하고 전체 한 달 동안 최소 인스턴스 수를 0으로 설정했다고 가정합니다. WAF에 대한 요청 부하는 최소이지만 전체 한 달 동안 시간당 지속적으로 존재합니다. 부하는 단일 용량 단위의 용량보다 낮습니다.
위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

고정 가격 = $0.443 * 730(시간) = $323.39

가변 비용 = $0.0144 * 1(용량 단위) * 730(시간) = $10.512

총 비용 = $323.39 + $10.512 = $333.902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>예제 3(b) – 자동 크기 조정을 사용하는 WAF_V2 인스턴스(최소 0개의 인스턴스 수)

자동 크기 조정을 사용하는 WAF_V2를 프로비전하고 전체 한 달 동안 최소 인스턴스 수를 0으로 설정했다고 가정합니다. 그러나 전체 한 달 동안 WAF 인스턴스로 전달되는 트래픽이 0개 있습니다.
위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

고정 가격 = $0.443 * 730(시간) = $323.39

가변 비용 = $0.0144 * 0(용량 단위) * 730(시간) = $0

총 비용 = $323.39 + $0 = $323.39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>예제 3 (C) - 수동 크기 조정이 인스턴스 1개로 설정된 WAF_V2 인스턴스

WAF_V2를 프로비전했고 전체 한 달 동안 허용되는 최소 인스턴스 1개 값으로 WAF_V2를 수동 확장으로 설정했다고 가정합니다. 그러나 전체 한 달 동안 WAF로 전달되는 트래픽이 0개 있습니다.
위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

고정 가격 = $0.443 * 730(시간) = $323.39

가변 비용 = $0.0144 * 1(인스턴스 수) * 10(용량 단위) * 730(시간) = $105.12

총 비용 = $323.39 + $105.12 = $428.51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>예제 4 – 자동 크기 조정, 용량 단위 계산을 사용하는 WAF_V2

자동 크기 조정을 사용하는 WAF_V2를 프로비전하고 전체 한 달 동안 최소 인스턴스 수를 0으로 설정했다고 가정합니다. 이 기간 동안 평균 8.88Mbps의 데이터 전송 속도로 25개의 새 TLS 연결/초가 수신됩니다.
위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

월별 가격 추정치는 월 730시간의 사용량을 기준으로 합니다.

고정 가격 = $0.443 * 730(시간) = $323.39

가변 비용 = $0.0144 * 730(시간) * {최대값(25/50, 8.88/2.22)} = $23.36(8.88Mbps를 처리하는 데 필요한 용량 단위 4개)

총 비용 = $323.39 + $23.36 = $346.75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>예제 5(a) – 자동 크기 조정, 시간 기반 계산을 사용하는 Standard_V2

자동 크기 조정을 사용하는 Standard_V2를 프로비전하고 최소 인스턴스 수를 0으로 설정하고 이 애플리케이션 게이트웨이가 2시간 동안 활성 상태라고 가정합니다.
처음 1시간 동안 용량 단위 10개로 처리할 수 있는 트래픽을 수신하고, 다음 1시간 동안 부하를 처리하는데 용량 단위 20개가 필요한 트래픽을 수신합니다.
위에서 언급한 가격 책정을 사용하여 Application Gateway 비용을 계산하면 다음과 같습니다.

고정 가격 = $0.246 * 2(시간) =  $0.492

가변 비용 = $0.008 * 10(용량 단위) * 1(시간) + $0.008 * 20(용량 단위) * 1(시간) = $0.24

총 비용 = $0.492 + $0.24 = $0.732


## <a name="monitoring-billed-usage"></a>청구된 사용량 모니터링

**모니터링** 섹션에서는 Application Gateway 메트릭의 일부로 활용되는 용량 단위 뿐만 아니라 여러 매개 변수(컴퓨팅 단위, 처리량 및 영구 연결)에 대한 사용량을 볼 수 있습니다.

![메트릭 섹션의 다이어그램.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>비용 예측에 유용한 메트릭

* 현재 용량 단위

    세 개의 매개 변수(현재 연결, 처리량 및 컴퓨팅 단위)에서 트래픽 부하를 분산하는 데 사용된 용량 단위 수

* 고정 청구 가능 용량 단위

    Application Gateway 구성에서 최소 인스턴스 수 설정(인스턴스 1개는 용량 단위 10개로 변환)에 따라 프로비전된 최소 용량 단위 수입니다.

* 예상 청구 용량 단위

    예상 청구 용량 단위는 청구 비용을 산정하는 데 사용되는 용량 단위의 수를 나타냅니다. 이 값은 현재 용량 단위(트래픽 부하를 분산하는 데 필요한 용량 단위)와 고정 청구 가능 용량 단위(프로비저닝된 최소 용량 단위) 중에서 더 큰 값으로 계산됩니다.

처리량, 현재 연결 및 컴퓨팅 단위와 같이 더 다양한 메트릭은 병목 상태를 파악하고 필요한 용량 단위 수를 예측하는 데에도 사용할 수 있습니다. 자세한 정보는 [Application Gateway 메트릭](application-gateway-metrics.md)에서 확인하세요.

#### <a name="example---estimating-capacity-units-being-utilized"></a>예제 - 활용 중인 용량 단위 예측

**관찰된 메트릭:**

* 컴퓨팅 단위 = 17.38
* 처리량 = 1.37M바이트/초 - 10.96Mbps
* 현재 연결 = 123.08K
* 계산된 용량 단위 = 최대값(17.38, 10.96/2.22, 123.08K/2500) = 49.232

메트릭의 관찰된 용량 단위 = 49.23

## <a name="next-steps"></a>다음 단계

Azure Application Gateway에서 가격 책정을 작동하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Application Gateway 가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Azure Application Gateway 가격 책정 계산기](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
