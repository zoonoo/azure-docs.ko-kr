---
title: Azure 예약 자동 갱신
description: 예약 할인을 계속 받기 위해 Azure 예약을 자동으로 갱신 하는 방법에 대해 알아봅니다.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679459"
---
# <a name="automatically-renew-reservations"></a>자동으로 예약 갱신

기존 예약이 만료 되 면 자동으로 교체를 구매 하도록 예약을 갱신할 수 있습니다. 자동 갱신은 예약 할인을 계속 하는 쉬운 방법을 제공 합니다. 또한 예약 만료를 면밀 하 게 모니터링할 필요가 없습니다. 자동 갱신을 사용 하면 수동으로 갱신 하지 않고도 절약 효과 손실을 방지할 수 있습니다. 갱신 설정은 기본적으로 해제 되어 있습니다. 언제 든 지 기존 예약의 만료까지 갱신 설정을 사용 하거나 사용 하지 않도록 설정 합니다.

예약을 갱신 하면 기존 예약이 만료 될 때 새 예약이 생성 됩니다. 기존 예약의 조건을 연장 하지 않습니다.

언제 든 지 자동 갱신을 옵트인 합니다. 갱신 가격은 기존 예약이 만료 되기까지 30 일 동안 사용할 수 있습니다. 예약 만료 전에 30 일 넘게 갱신을 사용 하도록 설정 하면 만료 전 30 일 전에 갱신 비용을 자세히 설명 하는 전자 메일이 전송 됩니다. 예약 가격은 갱신 가격 및 갱신 시간을 잠그는 시간 사이에 변경 될 수 있습니다. 그렇다면 갱신 비용은 두 비용 중 더 낮습니다. 예약 수량을 변경할 수 있습니다. 이렇게 하면 수량이 변경 될 당시에 설정 된 출시 전 가격을 사용 하도록 갱신이 업데이트 됩니다.

갱신할 의무가 없으며 기존 예약이 만료 되기 전에 언제 든 지 갱신을 옵트아웃 (opt out) 할 수 있습니다.

## <a name="set-up-renewal"></a>갱신 설정

Azure Portal > **예약**으로 이동 합니다.

1. 예약을 선택합니다.
2. **갱신**을 클릭 합니다.
3. **만료 시 새 예약 자동 구매를**선택 합니다.  
  ![예약 갱신을 보여 주는 예제](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>갱신 하지 않는 경우

서비스는 계속 정상적으로 실행 됩니다. 예약이 만료 된 후 사용에 대 한 종 량 제 요금이 청구 됩니다.

## <a name="required-renewal-permissions"></a>필수 갱신 권한

예약을 갱신 하려면 다음 조건이 충족 되어야 합니다.

- 기존 예약의 소유자 여야 합니다.
- 예약이 단일 구독 또는 리소스 그룹으로 범위가 지정 된 경우 구독의 소유자 여야 합니다.
- 공유 범위가 있는 경우 구독의 소유자 여야 합니다.

## <a name="default-renewal-settings"></a>기본 갱신 설정

기본적으로 갱신은 만료 되는 예약의 모든 속성을 상속 합니다. 예약 갱신 구매 구매는 동일한 SKU, 지역, 범위, 청구 구독, 용어 및 수량을 갖습니다.

그러나 갱신 예약 구매 수량을 업데이트 하 여 절감 액을 최적화할 수 있습니다.

## <a name="when-the-new-reservation-is-purchased"></a>새 예약이 구매 되는 경우

기존 예약이 만료 되 면 새 예약이 구매 됩니다. 두 예약 간의 지연 시간을 방지 하려고 합니다. 연속성을 통해 비용이 예측 가능 하 고 계속 해 서 할인을 받을 수 있습니다.

## <a name="changing-parent-reservation-after-setting-renewal"></a>갱신 설정 후 부모 예약 변경

만료 예약에 대해 다음과 같은 변경 작업을 수행 하면 예약 갱신이 취소 됩니다.

- 분할
- 병합
- 한 계정에서 다른 계정으로 예약 전송
- WebDirect 구독에서 EA (기업 계약) 구독 또는 기타 구매 방법으로 예약 전송
- 등록 갱신

새 예약은 갱신 중에 만료 되는 예약의 범위 및 인스턴스 크기 유연성 설정을 상속 합니다.

## <a name="new-reservation-permissions"></a>새 예약 권한

Azure는 만료 되는 예약의 권한을 새 예약에 복사 합니다. 또한 예약 구매의 구독 계정 관리자는 새 예약에 액세스할 수 있습니다.

## <a name="potential-renewal-problems"></a>잠재적 갱신 문제

다음과 같은 경우 Azure에서 갱신을 처리 하지 못할 수 있습니다.

- 지불을 수집할 수 없습니다.
- 갱신 하는 동안 시스템 오류가 발생 했습니다.
- 만료 되는 SKU는 갱신 중에 활성화 되지 않습니다.
- EA는 다른 EA로 갱신 됩니다.

위의 조건 중 하나라도 발생 하 고 갱신이 비활성화 되 면 전자 메일 알림을 받게 됩니다.

## <a name="renewal-notification"></a>갱신 알림

전자 메일은 구매 방법에 따라 다른 사람에 게 전송 됩니다.

- EA 고객-전자 메일이 EA 포털에 설정 된 알림 연락처에 전송 됩니다.
- 종 량 제 요금이 있는 개별 구독 고객-전자 메일은 계정 관리자로 설정 된 사용자에 게 전송 됩니다.
- 클라우드 솔루션 공급자 고객-이메일은 파트너 알림 담당자에 게 전송 됩니다.

## <a name="next-steps"></a>다음 단계
- Azure Reservations에 대 한 자세한 내용은 [Azure Reservations 항목](billing-save-compute-costs-reservations.md) 을 참조 하세요.
