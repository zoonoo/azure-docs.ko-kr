---
title: 포함 파일
description: 포함 파일
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4063751a71cd9cecc424dfe3daddaecfd9ea4071
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422149"
---
스팟 VM을 사용하면 사용되지 않는 용량을 크게 절감할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스팟 VM을 제거합니다. 따라서 스팟 VM은 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같은 중단을 처리할 수 있는 워크로드에 적합합니다.

사용 가능한 용량의 양은 크기, 지역, 시간대 등에 따라 달라질 수 있습니다. 스팟 VM을 배포할 때 Azure는 사용 가능한 용량이 있는 경우 VM을 할당하지만 이러한 VM에 대한 SLA는 없습니다. 스팟 VM은 고가용성 보장을 제공하지 않습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 30초 전에 스팟 VM을 제거합니다. 


## <a name="eviction-policy"></a>퇴거 정책

VM은 용량 또는 설정한 최대 가격에 따라 제거될 수 있습니다. 가상 시스템의 경우 제거 정책은 *제거된* VM을 중지된 할당 할당 상태로 이동하여 나중에 제거된 VM을 다시 배포할 수 있도록 할당 할당 할당으로 설정됩니다. 그러나 스팟 VM을 재할당하는 것은 사용 가능한 스팟 용량에 따라 달라집니다. 할당 된 VM은 스팟 vCPU 할당량에 계산되며 기본 디스크에 대한 요금이 부과됩니다. 

사용자는 [Azure 예약 이벤트를](../articles/virtual-machines/linux/scheduled-events.md)통해 VM 내 알림을 수신하도록 옵트인할 수 있습니다. 이렇게 하면 VM이 제거되고 있는지 알 수 있으며 30초 동안 작업을 완료하고 제거전에 종료 작업을 수행할 수 있습니다. 


| 옵션 | 결과 |
|--------|---------|
| 최대 가격은 현재 가격인 >로 설정됩니다. | 용량 및 할당량을 사용할 수 있는 경우 VM이 배포됩니다. |
| 최대 가격은 현재 가격을 < 설정됩니다. | VM이 배포되지 않습니다. 최대 가격이 >= 현재 가격이어야 한다는 오류 메시지가 나타납니다. |
| 최대 가격이 >경우 정지/거래 할당 VM을 다시 시작 = 현재 가격 | 용량과 할당량이 있으면 VM이 배포됩니다. |
| 최대 가격이 현재 가격< 경우 정지/거래 할당 VM을 다시 시작 | 최대 가격이 >= 현재 가격이어야 한다는 오류 메시지가 나타납니다. | 
| VM의 가격은 올라갔고 이제 최대 가격>. | VM이 제거됩니다. 실제 퇴거 하기 전에 30 s 알림을 받을. | 
| 제거 후 VM의 가격은 최대 가격< 되 고 다시 간다. | VM이 자동으로 다시 시작되지 않습니다. VM을 직접 다시 시작할 수 있으며 현재 가격으로 요금이 부과됩니다. |
| 최대 가격을`-1` | 가격 상의 이유로 VM은 제거되지 않습니다. 최대 가격은 표준 VM의 가격까지 현재 가격이 됩니다. 표준 가격 보다 높은 요금이 부과되지 않습니다.| 
| 최대 가격 변경 | 최대 가격을 변경하려면 VM을 할당 할당해야 합니다. VM을 할당 할당하고 t를 새 최대 가격을 설정한 다음 VM을 업데이트합니다. |

## <a name="limitations"></a>제한 사항

스팟 VM에는 다음 VM 크기가 지원되지 않습니다.
 - B 시리즈
 - 모든 크기의 프로모션 버전 (Dv2, NV, NC, H 프로모션 크기 등)

스팟 VM은 현재 임시 OS 디스크를 사용할 수 없습니다.

스팟 VM은 Microsoft Azure China 21Vianet을 제외한 모든 지역에 배포할 수 있습니다.

## <a name="pricing"></a>가격 책정

스팟 VM의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows용](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)VM 가격을 참조하십시오. 


가변 가격을 사용하면 최대 소수점 이하 장소를 사용하여 최대 가격(USD)으로 설정할 수 있습니다. 예를 들어 값은 `0.98765`시간당 0.98765 USD의 최대 가격입니다. 최대 가격을 `-1`설정하면 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 현재 현물 가격 또는 표준 VM의 가격이 될 것이며, 용량과 할당량이 있는 한 더 적습니다.


##  <a name="frequently-asked-questions"></a>질문과 대답

**Q:** 일단 생성되면 스팟 VM은 일반 표준 VM과 동일합니까?

**A:** 예. 스팟 VM에 대한 SLA가 없으며 언제든지 제거 할 수 있습니다.


**Q:** 퇴거당할 때 어떻게 해야 하나요, 하지만 여전히 용량이 필요합니까?

**A:** 용량이 필요한 경우 스팟 VM 대신 표준 VM을 사용하는 것이 좋습니다.


**Q:** 스팟 VM에 대한 할당량은 어떻게 관리하나요?

**A:** 스팟 VM에는 별도의 할당량 풀이 있습니다. 스팟 할당량은 VM과 배율 집합 인스턴스 간에 공유됩니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)을 참조하세요.


**Q:** 스팟에 대한 추가 할당량을 요청할 수 있나요?

**A:** 예. [표준 할당량 요청 프로세스를](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)통해 스팟 VM에 대한 할당량을 늘리는 요청을 제출할 수 있습니다.


**Q:** 스팟 VM을 지원하는 채널은 무엇입니까?

**A:** 스팟 VM 가용성은 아래 표를 참조하십시오.

<a name="channel"></a>

| Azure 채널               | Azure 스팟 VM 가용성       |
|------------------------------|-----------------------------------|
| 기업 계약         | 예                               |
| 종량제 통화 요금                | 예                               |
| CSP(클라우드 서비스 공급자) | [파트너에게 문의하기](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Microsoft 고객 계약 | 예                               |
| 이점                     | 사용할 수 없음                     |
| 스폰서                    | 사용할 수 없음                     |
| 평가판                   | 사용할 수 없음                     |


**Q:** 어디에서 질문을 게시할 수 있습니까?

**A:** Q&`azure-spot` [A.](https://docs.microsoft.com/answers/topics/azure-spot.html) 



