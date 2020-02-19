---
title: 파트너 센터의 상업 Marketplace 분석에 대 한 질문과 대답
description: 상업적 Marketplace 분석에 대해 자주 묻는 질문을 해결 하는 방법을 알아봅니다. 분석 용어에 대 한 데이터 사전을 포함 합니다.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 081109c2208e2006eb1628fbf0cfb99b1e6bd8f9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462142"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>상업적 Marketplace 분석에 대 한 질문과 대답

이 문서에서는 파트너 센터의 분석 메시지에 대 한 일반적인 질문과 대답을 다룹니다. 또한 분석 용어 사전을 제공 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

이 섹션에서는 파트너 센터에서 **아직 분석 되지 않은** 메시지에 대해 자주 묻는 질문에 대 한 대답을 제공 합니다.

**파트너 센터에서 분석 데이터를 볼 수 없습니다. 이러한 페이지에 액세스할 때 아래 메시지가 표시 됩니다. 이것은 무엇 인가요?**

![제품에 대 한 데이터가 아직 없음](./media/analytics-faq-no-data.png)

이 메시지가 표시 되는 이유는 다음과 같습니다.

- Marketplace에 게시 된 제품에 대 한 가져오기가 현재 없습니다. 이는 제품이 marketplace에 살고 있고 제품 표시 페이지의 고객 으로부터 보기를 획득 하는 것을 의미할 수 있습니다. 하지만 고객은 아직 구매 및 배포 작업을 수행 하지 않았습니다.
- 제품의 게시가 진행 중이 고 아직 활성 상태가 아닐 수 있습니다. 고객은 라이브 제공만 얻을 수 있습니다. 제품의 상태를 확인 하려면 [분석 대시보드의](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)개요를 참조 하세요. 자세한 내용은 [상용 Marketplace 분석의 요약 대시보드](./summary-dashboard.md)를 참조 하세요.
- 귀하의 제안은 목록 전용 제품 이며 marketplace의 고객이 구매할 수 없는 **연락처**로 표시 될 수 있습니다. 이러한 혜택은 잠재 고객을 생성 하 고 사용자와 공유 되기는 하지만 구매할 수 없기 때문에 이러한 제품에 대 한 주문은 생성 되지 않습니다. 제품 목록 유형을 확인 하려면 설정 페이지로 이동 합니다.

**분석 데이터가 있지만 아래 메시지가 표시 됩니다.**

![지정 된 날짜 범위에 대 한 데이터가 없습니다.](./media/analytics-faq-data-range.png)

이 메시지를 수신 하는 경우 분석 데이터가 있지만 선택한 날짜 범위에 대 한 데이터가 없습니다. 2010 이후의 데이터를 보려면 다른 날짜 범위 또는 사용자 지정 날짜 범위를 선택 합니다. 자세한 내용은 [상용 Marketplace 분석에서 요약 대시보드의](./summary-dashboard.md)날짜 범위 섹션을 참조 하세요.

## <a name="dictionary-of-data-terms"></a>데이터 용어 사전

| 특성 이름 | 보고서 | 정의|
|---|---|---|
| Azure 라이선스 유형 | 고객, 주문 | Azure 구매 고객이 체결하는 라이선싱 계약 유형입니다. 채널 라고도 함 |
| Azure 라이선스 유형: 클라우드 솔루션 공급자 | 고객, 주문 | 재판매인 역할을 하는 클라우드 솔루션 공급자를 통해 최종 고객이 Azure 및 Marketplace 제품을 조달받습니다.|
| Azure 라이선스 유형: 기업 | 고객, 주문 | Microsoft와 직접 체결하는 기업계약을 통해 최종 고객이 Azure 및 Marketplace 제품을 조달받습니다.|
| Azure 라이선스 유형: 기업(재판매인 있음)  | 고객, 주문 | 최종 고객은 Microsoft와의 기업계약을 용이 하 게 하는 재판매인을 통해 Azure 및 Marketplace 제품을 procures 합니다.|  |
| Azure 라이선스 유형: 종량제| 고객, 주문 | 최종 고객은 Microsoft에 직접 서명 하는 "종 량 제" 계약을 통해 Azure 및 Marketplace 제품을 procures.||
| 클라우드 인스턴스 이름| 주문| VM이 배포된 Microsoft Cloud입니다.||
| 클라우드 인스턴스 이름: Azure Global| 주문| 공용 글로벌 Microsoft 클라우드.|| |
| 클라우드 인스턴스 이름: Azure Government | 주문| 중국, 독일 또는 미국의 정부 중 하나인 정부 관련 Microsoft 클라우드.| |
| 고객 구/군/시| Customer| 고객이 제공한 도시 이름입니다. 도시는 고객의 Azure 구독에서 구/군/시와 다를 수 있습니다.||
| 고객 통신 언어  | Customer| 고객이 통신용으로 선택한 기본 설정 언어입니다.||
| 고객 회사 이름 | 고객, 주문 | 고객이 제공한 회사 이름입니다. 이름은 고객의 Azure 구독에서 구/군/시와 다를 수 있습니다.|  |
| 고객 국가 | 고객, 주문 | 고객이 제공한 국가 이름입니다. Country는 고객의 Azure 구독에 있는 국가와 다를 수 있습니다.|  |
| 고객 이메일| Customer| 최종 고객이 제공한 전자 메일 주소입니다. 전자 메일은 고객의 Azure 구독에 있는 전자 메일 주소와 다를 수 있습니다.||
| 고객 이름| Customer| 고객이 제공한 이름입니다. 이름은 고객의 Azure 구독에 제공 된 이름과 다를 수 있습니다.| |
| 고객 ID | 고객, 주문 | 고객에 게 할당 된 고유 식별자입니다. 고객은 Azure Marketplace 구독이 0 개 이상 있을 수 있습니다.|  |
| 고객 우편 번호  | Customer| 고객이 제공한 우편 번호입니다. 코드는 고객의 Azure 구독에서 제공 되는 우편 번호와 다를 수 있습니다.| |
| 고객 시/도| Customer| 고객이 제공한 상태 (주소)입니다. 상태는 고객의 Azure 구독에 제공 된 상태와 다를 수 있습니다.| |
| 취득일| Customer| 고객이 게시 한 제품을 처음 구입한 날짜입니다.| |
| 취소일| Customer| 고객이 이전에 구매한 모든 제품의 마지막 날짜를 취소 한 마지막 날짜입니다.||
| 새 고객  | 주문| 이 값은 하나 이상의 제품을 처음으로 획득 한 새 고객을 식별 합니다. "취득일"에 대해 동일한 월 내에 값이 "예"로 나타납니다. 고객이 보고 한 월 이전에 제품을 구매한 경우 값은 "아니요"입니다. |
| 미리 보기 SKU| 주문| 이 값은 SKU를 "preview"로 태그가 지정 되었는지 여부를 알려 주는 데 사용 됩니다. SKU가 그에 따라 태그가 지정 되 고이 이미지를 배포 하 고 사용할 수 있는 Azure 구독만이 이미지를 사용할 수 있는 경우 값은 "예"입니다. SKU가 "preview"로 식별 되지 않은 경우 값은 "No"입니다.  |
| 판촉 연락처 옵트인| Customer| 이 값은 고객이 게시자의 판촉 담당자를 사전에 옵트인 했는지 여부를 알려 주는 데 사용 됩니다. 현재는 고객에게 해당 옵션이 제공되지 않으므로 보드 전체에 “아니요”가 표시됩니다. 이 기능이 배포되면 그에 따라 업데이트를 시작할 예정입니다.|
| Marketplace 라이선스 유형| 주문| Marketplace 제품의 청구 방법입니다.||
| Marketplace 라이선스 유형: Azure를 통해 청구됨| 주문| Microsoft가 이 Marketplace 제품의 대리인으로 게시자 대신 고객에게 대금을 청구합니다. (종량제 신용 카드 또는 기업 송장)||
| Marketplace 라이선스 유형: BYOL(사용자 라이선스 필요) | 주문| VM을 배포 하려면 고객이 제공한 라이선스 키가 필요 합니다. Microsoft는 marketplace를 통해 귀하의 제품을 나열 하기 위해 고객에 게 요금을 청구 하지 않습니다.||
| Marketplace 라이선스 유형: 무료| 주문| 제품은 모든 사용자에 게 무료로 구성 됩니다. Microsoft는이 제품 사용에 대해 고객에 게 요금을 청구 하지 않습니다.||
| Marketplace 라이선스 유형: Microsoft - 재판매인  | 주문| Microsoft가 이 Marketplace 제품의 재판매인 역할을 합니다.|  |
| Marketplace 구독 ID | 고객, 주문 | 고객이 Marketplace 제품을 구매 하는 데 사용한 Azure 구독과 연결 된 고유 식별자입니다. ID는 이전에 Azure 구독 GUID 였습니다.||
| 제품 이름  | 주문| Marketplace 제품의 이름입니다.|| |
| 제품 유형  | 주문| Microsoft Marketplace 제공 형식입니다.|||
| 제품 유형: 관리형 애플리케이션  | 주문을 | 다음 조건에 해당 하는 경우 Azure 앱: 관리 되는 앱 제안 유형 사용: VM 또는 전체 IaaS 기반 솔루션을 사용 하 여 고객을 위한 구독 기반 솔루션을 배포 합니다. 사용자 또는 고객은 파트너가 솔루션을 관리 해야 합니다. |
| 제안 유형: Azure 애플리케이션| 주문을 | 솔루션에 간단한 VM 이외의 추가 배포 및 구성 자동화가 필요한 경우 Azure 애플리케이션 솔루션 템플릿 제품 유형을 사용 합니다.||
| 제안 유형: 컨설팅 서비스| 주문| Azure Marketplace의 컨설팅 서비스는 고객과 서비스를 연결하여 고객의 Azure 사용을 지원하고 확대하는 데 유용합니다.| |
| 제안 유형: 컨테이너 | 주문| 솔루션이 Kubernetes 기반 Azure Container Service로 프로비전된 Docker 컨테이너 이미지인 경우 컨테이너 제품 유형을 사용합니다.||
| 제안 유형: Dynamics 365 비즈니스 중부| 주문| 솔루션이 재무 및 운영에 대 한 Dynamics 365와 통합 된 경우이 제안 유형을 사용 합니다.| |
| 제품 유형: Dynamics 365 for Customer Engagement | 주문| 솔루션이 고객 참여를 위해 Dynamics 365와 통합 되는 경우이 제안 유형을 사용 합니다.||
| 제안 유형: IoT Edge 모듈 | 주문| Azure IoT Edge 모듈은 IoT Edge에서 관리 되는 가장 작은 계산 단위 이며 Microsoft 서비스 (예: Azure Stream Analytics), 타사 서비스 또는 고유한 솔루션 관련 코드를 포함할 수 있습니다. |
| 제안 유형: 응용 프로그램 Power BI | 주문| Power BI와 통합 된 응용 프로그램을 배포할 때 Power BI 응용 프로그램 제안 유형을 사용 합니다.|  |
| 제안 유형: SaaS 응용 프로그램| 주문| SaaS 앱 제품 유형을 사용하면 고객이 SaaS 기반, 기술 솔루션을 구독으로 구매할 수 있습니다.||
| 제품 유형: 가상 머신 | 주문| 고객과 연결된 구독에 가상 어플라이언스를 배포할 경우 가상 머신 제품 형식을 사용합니다.||
| 제안 유형: Visual Studio Marketplace 확장  | 주문| Azure DevOps 확장 개발자가 이전에 제공 하는 제품 유형입니다. Azure DevOps 확장을 전달 하는 개발자는 자신의 확장을 고객에 게 직접 판매할 수 있습니다. 내선 번호는 유료 또는 평가판을 포함 하 여 구성할 수 있습니다. |
| 주문 취소 날짜| 주문| Marketplace 주문이 취소된 날짜입니다.||
| 주문 ID| 주문| Marketplace 서비스의 고객 주문에 대 한 고유 식별자입니다. 가상 컴퓨터 사용량 기반 제안은 주문에 연결 되지 않습니다.| |
| 주문 구매 날짜| 주문| Marketplace 주문이 만들어진 날짜입니다.|||
| 주문 상태| 주문| 데이터를 마지막으로 새로 고칠 때의 Marketplace 주문 상태입니다.|     |
| 주문 상태: 활성  | 주문| 고객이 주문을 구매 했 고 주문을 취소 하지 않았습니다.|         |
| 주문 상태: 취소 됨 | 주문| 이전에 고객이 주문을 구매 하 고 이후에 주문을 취소 했습니다.||
| 공급자 전자 메일| Customer| Microsoft와 최종 고객 간의 관계와 관련 된 공급자의 전자 메일 주소입니다. 고객이 대리점을 통해 엔터프라이즈 인 경우 재판매인이 됩니다. CSP (클라우드 솔루션 공급자)가 관련 된 경우이는 CSP가 됩니다.|
| Provider Name| Customer| Microsoft와 최종 고객 간의 관계에 관련 된 공급자의 이름입니다. 고객이 대리점을 통해 엔터프라이즈 인 경우 재판매인이 됩니다. CSP (클라우드 솔루션 공급자)가 관련 된 경우이는 CSP가 됩니다.|
| SKU| 주문| 게시 하는 동안 정의 된 SKU 이름입니다. 제품에는 많은 Sku가 있을 수 있지만 SKU는 단일 제품에만 연결 될 수 있습니다.||
| 평가판 종료 날짜| 주문| 이 주문의 평가 기간이 종료되었거나 종료될 예정인 날짜입니다.||

## <a name="next-steps"></a>다음 단계

- 파트너 센터 상업적 Marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 Marketplace에 대 한 분석](./analytics.md)을 참조 하세요.
- 제품의 마켓플레이스 작업을 요약 하는 집계 데이터의 그래프, 추세 및 값은 [상용 marketplace 분석의 요약 대시보드](./summary-dashboard.md)를 참조 하세요.
- 그래픽 및 다운로드 가능한 형식으로 주문에 대 한 자세한 내용은 [상용 Marketplace 분석의 주문 대시보드](./orders-dashboard.md)를 참조 하세요.
- VM (가상 컴퓨터)은 사용량 및 요금제 청구 메트릭을 제공 합니다. [상용 Marketplace 분석의 사용량 대시보드](./usage-dashboard.md)를 참조 하세요.
- 성장 추세를 비롯 한 고객에 대 한 자세한 내용은 [상용 Marketplace 분석의 고객 대시보드](./customer-dashboard.md)를 참조 하세요.
- 최근 30 일 동안의 다운로드 요청 목록은 [상용 Marketplace 분석에서 대시보드 다운로드](./downloads-dashboard.md)를 참조 하세요.
- Azure Marketplace 및 AppSource의 제품에 대 한 고객 의견의 통합 보기를 보려면 [상용 Marketplace 분석의 등급 및 리뷰 대시보드](./ratings-reviews.md)를 참조 하세요.
