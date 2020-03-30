---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123258"
---
Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다.  이러한 가상 머신 크기는 규정 준수 및 규정 요구 사항과 같은 요소를 포함하는 작업에 대해 다른 고객으로부터 높은 수준의 격리가 필요한 작업에 가장 적합합니다.  고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다.

격리 크기를 사용하면 가상 머신이 특정 서버 인스턴스에서 하나만 실행되도록 보장합니다.  현재 격리 가상 머신 제품에는 다음이 포함됩니다.
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

사용 가능한 각 격리 크기에 대한 자세한 내용은 여기에서 확인할 수 [있습니다.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>2020년 5월 15일 D15_v2/DS15_v2 격리
**2020년 2월 10일 업데이트: "격리" 사용 일정이 2020년 5월 15일까지 연장되었습니다."**

Azure 전용 호스트는 이제 GA로, 단일 테넌트 물리적 서버에서 조직의 Linux 및 Windows 가상 컴퓨터를 실행할 수 있습니다. 격리된 Azure VM을 Azure 전용 호스트로 완전히 대체할 계획입니다. **2020년 5월 15일** 이후에는 D15_v2/DS15_v2 Azure VM이 더 이상 하드웨어로 격리되지 않습니다.

## <a name="how-does-this-affect-me"></a>이 변경 사항은 어떤 영향을 미치나요?
2020년 5월 15일 이후에는 D15_v2/DS15_v2 Azure 가상 시스템에 대해 격리 보증을 더 이상 제공하지 않습니다. 

## <a name="what-actions-should-i-take"></a>어떤 조치를 취해야 하나요?
하드웨어 격리가 필요하지 않은 경우 수행해야 할 조치가 없습니다. 

격리가 필요한 경우 2020년 5월 15일 이전에 다음 중 하나를 수행해야 합니다.

• [워크로드를](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) Azure 전용 호스트로 마이그레이션합니다.

• 동일한 가격 성능을 얻기 위해 D15i_v2 및 DS15i_v2 Azure VM에 [대한 액세스를 요청합니다.](https://aka.ms/D15iRequestAccess) 이 옵션은 종량제 및 1년 예약 인스턴스 시나리오에서만 사용할 수 있습니다.    

• [워크로드를](https://azure.microsoft.com/blog/resize-virtual-machines/) 다른 Azure 격리 가상 컴퓨터로 마이그레이션합니다. 

자세한 내용은 아래를 참조하십시오.

## <a name="timeline"></a>타임라인
| Date | 작업 | 
| --- | --- |
| 2019년 11월 18일 | D/DS15i_v2(PAYG, 1년 RI) 사용 가능 |
| 2020년 5월 14일  | D/DS15i_v2 1년 RI 구매 마지막 날 | 
| 2020년 5월 15일   | D/DS15_v2 격리 보증 제거 | 
| 2021년 5월 15일  | 은퇴 D/DS15i_v2(2019년 11월 18일 이전에 D/DS15_v2 3년 RI를 구입한 고객 제외)| 
| 2022년 11월 17일  | 3년 RI가 완료되면 D/DS15i_v2 은퇴(2019년 11월 18일 이전에 D/DS15_v2 3년 RI를 구입한 고객의 경우) | 

## <a name="faq"></a>FAQ
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>Q: 크기 D/DS15_v2 은퇴할 예정인가요?
**A**: 아니요, "격리"기능만 은퇴할 것입니다. 격리가 필요하지 않은 경우 조치를 취할 필요가 없습니다.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>Q: 크기 D/DS15i_v2 은퇴할 예정인가요?
**A**: 예, 크기는 5 월 15,2021까지 만 사용할 수 있습니다. 2019년 11월 18일 이전에 D/DS15_v2 3년 RI를 구매한 고객은 2022년 11월 17일까지 D/DS15i_v2 이용할 수 있습니다.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>Q: 포털에서 새 D/DS15i_v2 크기가 표시되지 않는 이유는 무엇입니까?
**A**: 현재 D / DS15_v2 고객이고 새 D / DS15i_v2 크기를 사용하려면이 [양식을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u) 작성하십시오.

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>Q: 새 D/DS15i_v2 크기에 대한 할당량이 표시되지 않는 이유는 무엇입니까?
**A**: 현재 D / DS15_v2 고객이고 새 D / DS15i_v2 크기를 사용하려면이 [양식을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u) 작성하십시오.

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q: 다른 격리된 크기는 언제 은퇴할 예정입니까?
**A**: 우리는 크기의 공식 해체 12 개월 전에 알림을 제공 할 것입니다.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q: vm이 격리되지 않은 하드웨어에 착륙할 때 가동 중지 시간이 있습니까?
**A**: 격리가 필요하지 않으면 조치를 취할 필요가 없으며 가동 중지 시간이 표시되지 않습니다.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>Q: 격리되지 않은 가상 컴퓨터로 이동하는 데 비용이 변경되었습니까?
**A**: 아니오 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>Q: 이미 D15_v2 또는 Ds15_v2 위해 1년 또는 3년 예약 인스턴스를 구입했습니다. VM 사용에 할인은 어떻게 적용되나요?
**A**: 2019년 11월 18일 이전에 구매한 RIs는 새로운 격리된 VM 시리즈로 적용 범위를 자동으로 확장합니다. 

| RI |  인스턴스 크기 유연성 | 혜택 자격 |   
| --- | --- | --- |
|   D15_v2  |   꺼짐     |   D15_v2 및 D15i_v2 |    
|   D15_v2  |   설정  |   D15_v2 시리즈와 D15i_v2 모두 RI 혜택을 받게 됩니다. |    
|   D14_v2  |   설정  |   D15_v2 시리즈와 D15i_v2 모두 RI 혜택을 받게 됩니다. |    
 
마찬가지로 Dsv2 시리즈.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>Q: Dv2에 대한 추가 예약 인스턴스를 구입하려고 합니다. 어떤 것을 선택해야 하나요?
**A**: 2019년 11월 18일 이후에 구입한 모든 R은 다음과 같은 동작이 있습니다. 

| RI |  인스턴스 크기 유연성 | 혜택 자격 |   
| --- | --- | --- |
| D15_v2 |  꺼짐 |   D15_v2  
| D15_v2 |  설정 |    D15_v2 시리즈는 RI 혜택을 받게 됩니다. 새 D15i_v2 이 RI 유형에서 RI 혜택을 받을 수 없습니다. | 
| D15i_v2 |     꺼짐 | D15i_v2 |  
| D15i_v2 |     설정  | D15i_v2 | 
 
인스턴스 크기 유연성은 D2_v2, D4_v2 또는 D15_v2 같은 다른 크기에 적용하는 데 사용할 수 없습니다. 마찬가지로, Dsv2 시리즈에 대 한.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>Q: D15i_v2 및 DS15i_v2 위해 새로운 3년 RI를 구입할 수 있습니까?
**A**: 불행하게도 아니, 만 1 년 RI는 새로운 구매에 사용할 수 있습니다.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>Q: 기존 D15_v2/DS15_v2 예약 인스턴스를 격리된 크기 예약 인스턴스로 이동할 수 있습니까?
**A**: 이 작업은 절연 크기와 격리되지 않은 크기에 모두 적용되므로 필요하지 않습니다. 그러나 Azure는 기존 D15_v2/DS15_v2 예약 인스턴스를 D15i_v2/DS15i_v2 변경할 수 있도록 지원합니다. 다른 모든 Dv2/Dsv2 예약 인스턴스의 경우 기존 예약 인스턴스를 사용하거나 격리된 크기에 대해 새 예약 인스턴스를 구입합니다.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q: 실버 또는 골드 내구성 계층에 의존하는 Azure 서비스 패브릭 고객입니다. 이 변경이 나에게 영향을 미나요?
**A**: 아니요. 서비스 패브릭의 [내구성 계층에서](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) 제공하는 보증은 변경 후에도 계속 작동합니다. 다른 이유로 물리적 하드웨어 격리가 필요한 경우에도 위에서 설명한 작업 중 하나를 수행해야 할 수 있습니다. 
