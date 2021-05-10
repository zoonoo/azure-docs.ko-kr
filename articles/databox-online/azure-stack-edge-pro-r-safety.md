---
title: Azure Stack Edge Pro R 안전 가이드 | Microsoft Docs
description: 안전 규칙, 참고 자료, 고려 사항을 설명하고 Azure Stack Edge Pro R 디바이스를 안전하게 설치하고 작동하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: 7b3589349feed2e20711aa756d8be8ebc433ac0b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077604"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge Pro R 안전 지침

![경고 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![안전성 고지 읽기 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**안전성 및 상태 정보 읽기**

Azure Stack Edge Pro R 디바이스를 사용하기 전에 문서의 모든 안전 정보를 읽으세요. 지침을 따르지 않으면 화재, 감전, 부상 또는 속성 손상이 발생할 수 있습니다. Azure Stack Edge Pro R을 사용하기 전에 아래의 모든 안전 정보를 읽으세요.

## <a name="safety-icon-conventions"></a>안전성 아이콘 표시 규칙

위험 경고 기호 표시는 다음과 같습니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![위험 기호](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **위험:** 피하지 않을 경우 사망 또는 심각한 상처를 입는 위험한 상황을 나타냅니다. <br> **경고:** 피하지 않을 경우 사망 또는 심각한 상처를 입을 수 있는 위험한 상황을 나타냅니다. <br> **주의:** 피하지 않을 경우 최소 또는 보통 수준의 상처를 입을 수 있는 위험한 상황을 나타냅니다.|
|

다음 위험 아이콘은 Azure Stack Edge Pro R Edge 디바이스를 설정하고 실행할 때 나타납니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![모든 지침을 먼저 읽을 것](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | 모든 지침을 먼저 읽을 것 |
| ![참고 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **고지:** | 중요하지만 위험과 관련되지 않은 것으로 간주되는 정보를 나타냅니다. |
| ![위험 기호](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | 위험 기호 |
| ![위험 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | 위험|
| ![무거움 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | 무거움 무게 위험|
| ![감전 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | 감전 위험 |
| ![사용자 서비스 가능 부품 없음 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | 사용자 서비스 가능 부품 없음 제대로 교육을 받지 않은 경우 액세스하지 마세요. |
| ![복수의 전원 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | 복수의 전원 모든 전원 코드를 분리하여 장비에서 모든 전원을 제거합니다. |
| ![조이는 지점 아이콘](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | 조이는 지점이 있습니다. |
| ![뜨거운 구성 요소 또는 뜨거운 표면 아이콘](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | 뜨거운 구성 요소 또는 뜨거운 표면을 나타냅니다. |


## <a name="handling-precautions-and-site-selection"></a>예방 조치 및 사이트 선택 처리

![경고 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **경고:**

* 제공되는 디바이스를 이동하고 처리하는 등의 작업에는 적절한 장비(예를 들어, 팔레트 잭) 및 PPE(개인 보호 장비)를 사용해야 합니다.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![팁 위험 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **경고:**

* 장비가 기울어지거나 눌리지 않도록 평평하고 단단하고 안정적인 곳에 배치하세요.
* 디바이스를 2개 이상 쌓지 마세요.
* 쌓기 전에 시스템이 안전한지 확인합니다.
* 쌓인 디바이스를 옮기지 않습니다.
* 외부 케이블을 통해 전원이 공급되는 디바이스를 쌓지 마세요.
* 쌓기 작업 중에 전원 코드가 눌리거나 손상되지 않았는지 확인합니다.
* 디바이스를 테이블 또는 작업 영역으로 사용하지 않습니다.

![경고 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![감전 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
![사용자 서비스 가능 부품 없음 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **주의:**

* *받은* 디바이스의 손상을 검사합니다. 디바이스 인클로저가 손상된 경우 [Microsoft 지원에 문의하여](azure-stack-edge-contact-microsoft-support.md) 교체품을 받으세요. 디바이스를 작동하려 하지 마세요.
* 디바이스 오작동이 의심되는 경우 [Microsoft 지원에 문의하여](azure-stack-edge-contact-microsoft-support.md) 교체품을 받으세요. 디바이스를 정비하려 하지 마세요.
* 디바이스에 사용자 서비스 가능 부품이 없습니다. 내부에 위험한 수준의 전압, 전류 및 에너지가 있습니다. 열지 마세요. 디바이스를 Microsoft로 보내서 점검을 받으세요.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 무거운 무게 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **경고:**

* UPS의 전원 공급 모듈을 제거하면 UPS 내의 전원이 공급되는 파트가 노출됩니다. 전원 공급 모듈 구획 내에 외부 개체를 삽입하지 마세요.
* Azure Stack Edge Pro R Edge 디바이스를 직접 들지 마세요. 인클로저의 무게는 52~93kg(115~205lbs) 입니다. 장비를 이동하고 드는 경우 기계를 사용하거나 기타 적절한 도움을 받습니다. 장비를 이동하고 드는 경우 지역 산업 보건 및 안전 요구 사항을 준수합니다.
* 장비를 들 때는 반드시 적합한 기계를 사용하세요. 해당 무게를 들어 올리려 시도할 경우 상처를 입을 수 있습니다.

![경고 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)  **경고:**

* 시스템은 제어된 환경에서 작동하도록 설계되었습니다. 다음 사이트를 선택합니다.
  * 환기가 잘 되고 직사광선과 방사선을 포함한 열원에서 멀리 떨어져 있습니다.
  * 습기 또는 비에 노출되지 않습니다.
  * 진동 및 물리적인 충격을 최소화하는 공간에 있습니다.  시스템은 MIL-STD-810G에 따라 충격 및 진동을 견딜 수 있게 설계되었습니다.
  * 전기 디바이스에서 생성되는 강력한 전자기장에서 격리되었습니다.
  * 올바르게 접지된 콘센트가 포함되었습니다.
  * 전원 공급 코드에 액세스할 수 있는 충분한 공간(제품이 전원이 끊기는 주요 원인)이 있습니다.
* 이더넷 케이블은 제품과 함께 제공되지 않습니다. 전자기 간섭을 줄이려면 Cat 6 STP(Shielded Twisted-pair) 케이블을 사용하는 것이 좋습니다.
* 장비 주위에 적절한 공기 순환이 가능한 작업 영역에서 장비를 설정합니다. 디바이스를 실행하는 동안 전면 커버 및 후면 커버를 완전히 제거되어야 합니다.
* 이더넷 케이블은 제품과 함께 제공되지 않습니다. 전자기 간섭을 줄이려면 Cat 6 차폐(STP) 케이블을 사용하는 것이 좋습니다.
* 온도가 조절되고 전도성 오염 물질이 없는 영역에 장비를 설치하고, 장비 주변에 공기가 적절하게 순환되게 해 주세요.
* 액체가 나오는 곳과 습기가 과도하게 높은 환경에서 장비를 사용하지 마세요.
* 액체 또는 외부 개체가 시스템에 들어가지 못하게 하세요. 음료나 기타 액체 용기를 시스템에 또는 시스템 근처에 두지 마세요.

## <a name="heater-precautions"></a>히터 예방 조치

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![뜨거운 구성 요소 또는 뜨거운 표면 아이콘 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:** 

* 시스템 전원이 켜져 있는 동안 자동 히터 작업이 이루어지면 히터 어셈블리 커버의 높은 표면 온도 때문에 접촉 위험이 발생할 수 있습니다. 시스템 전원이 켜져 있는 동안에는 표면을 건드리지 마세요. 시스템이 꺼진 후 10분간 냉각 기간을 가집니다.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![조이는 지점 아이콘 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:** 

* 시스템 전원이 켜져 있으면 후면 플리넘 도어의 자동 작용으로 조이는 지점 위험이 발생할 수 있습니다. 시스템이 켜져 있으면 해당 영역에 접근하지 않습니다.

## <a name="electrical-precautions"></a>전기 주의 사항

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![감전 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:**

* 전원 공급 코드에 안전한 전기 접지 연결을 제공하세요. AC 코드에는 삼선 접지 플러그(접지 핀이 있는 플러그)가 있습니다. 이 플러그는 접지된 AC 콘센트에만 적합합니다. 접지 핀의 용도를 무시하지 마세요.
* 전원 공급 디바이스 코드의 플러그가 기본 차단 디바이스라면, 소켓 콘센트가 디바이스 근처에 있고 쉽게 액세스할 수 있는지 확인하세요.
* 다음 조건 중 하나에 해당하면 전원 코드를 분리하고(코드가 아닌 플러그를 당겨서) 모든 케이블의 연결을 끊으세요.

  * 전원 코드 또는 플러그가 해어지거나 손상되었습니다.
  * 디바이스 케이스에 무언가를 쏟았습니다.
  * 디바이스가 비 또는 과도한 습기에 노출되었습니다.
  * 디바이스가 바닥에 떨어져서 디바이스 케이스가 손상되었습니다.
  * 디바이스를 점검하거나 수리를 받아야 할 것 같습니다.
* 이동하기 전이나 어떤 방식으로든 손상될 것이라고 판단되는 장치 플러그를 영구적으로 뽑으십시오.
* 높은 누출 전류를 방지하려면 단일 전송 사례에 UPS(무정전 전원 공급 장치)가 여러 개 있는 경우 각 UPS를 독립 분기 회로에 연결하는 것이 좋습니다. 그러나 각 UPS의 안전 접지에서 PDU의 단일 공급 접지 컨덕터를 사용할 때 PDU(전력 배포 장치) 또는 기타 디바이스를 사용하는 경우 각 UPS의 외부에 있는 접지 터미널도 반드시 보조 건물 접지와 함께 사용해야 합니다.

  > [!NOTE]
  > 이미 추가 접지 컨덕터가 있는 PDU를 사용하는 경우 UPS에서 추가 접지 터미널을 사용하지 않아도 됩니다.

* 다음 전원 사양을 충족하도록 전기 과부하 방지 기능이 있는 적절한 전원을 제공하세요.

  * 전압: 100~240V AC
  * 전류: 20A, 전원 코드 당 최댓값. 전원 코드는 제공됩니다.
  * 주파수: 50~60Hz

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![감전 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![복수의 전원](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **경고:**

* UPS(무정전 전원 공급 장치)가 없는 시스템의 경우 모든 AC 전원 코드를 분리하여 장비에서 AC 전원을 완전히 제거합니다.
* UPS를 사용하는 시스템의 경우 모든 AC 전원 코드를 분리하고 UPS 전원 스위치를 사용하여 시스템의 전원 공급을 차단합니다. UPS에는 위험한 AC 및 DC 전압이 포함되어 있습니다.
* 시스템이 UPS를 포함하는 경우 UPS에는 차폐 입력 전원 케이블이 제공됩니다. 반드시 차폐 입력 전원 케이블을 사용해야 하며, 코드를 교체하거나 바꾸지 마세요.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![감전 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:**

* UPS가 장착된 시스템의 경우 AC 및/또는 DC 전압에는 항상 배터리 또는 유틸리티에서 생성된 UPS 출력의 AC 전압에 의한 잠재적 위험이 있습니다. 장비 손상이나 부상을 방지하려면 기본 전원에서 분리된 경우에도 UPS 출력에 전압이 있을 수 있다고 가정하세요.
* UPS가 장착된 시스템의 경우 UPS로 전원을 공급받는 모든 외부 연결은 암(female)입니다. 대/소문자를 제거하거나 UPS의 커넥터에 아무것도 삽입하지 마세요.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![감전 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:**

* 장치와 함께 제공되는 AC 전원 코드만 사용하시고 해당 코드를 바꾸지 마세요.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![사용자 서비스 가능 부품 없음 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **주의:**

* 이 장비에는 리튬 동전 셀 및/또는 리튬인산철 배터리가 포함되어 있습니다. 장비를 수리하지 마세요. 해당 장비의 배터리는 사용자가 수리할 수 없습니다. 배터리가 잘못된 형식으로 대체된 경우 폭발 위험.
* UPS의 배터리 모듈을 제거하면 UPS 내의 전원이 공급되는 파트가 노출됩니다. 배터리 모듈 구획 내부에 외부 개체를 삽입하지 마세요.

## <a name="regulatory-information"></a>규정 정보

이 섹션에는 Azure Stack Edge Pro R 디바이스의 규정 정보, 규정 모델 번호(Azure Stack Edge Pro R)가 포함되어 있습니다.

Azure Stack Edge Pro R 디바이스는 NRTL Listed(UL, CSA, ETL 등) 및 IEC/EN 60950-1 또는 IEC/EN 62368-1 규격(CE 마크 획득) 정보 기술 장비에 사용하도록 설계되었습니다.

디바이스는 다음과 같은 환경에서 작동하도록 설계되었습니다.

| 환경 | 사양 |
|:--- |:--- |
|온도 사양 | <ul><li>스토리지 온도: –33&deg;C–63&deg;C(–28&deg;F-145&deg;F) </li><li>연속 작업: 5&deg;C–43&deg;C(41&deg;F–110&deg;F)</li><li>최대 온도 기울기(운영 및 스토리지): 20&deg;C/h(68&deg;F/h)</li></ul> |
|상대 습도 사양 | <ul><li>스토리지: 5%~95% RH 및 최대 이슬점 33&deg;C(91&deg;F) 대기는 항상 비응축 상태여야 합니다.</li><li>운영: 상태 습도 5%~85% 및 최대 이슬점 29&deg;C(84.2&deg;F)</li></ul> |
| 최대 고도 사양 | <ul><li>운영(UPS 제외): 4,572미터(15,000피트)</li><li>운영(UPS 포함): 3,048미터(10,000피트)</li><li>스토리지: 12,192미터(40,000피트)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![알림 아이콘 - 2](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **알림:** &nbsp;Microsoft의 명시적 승인 없이 장비를 변경 또는 수정할 경우 사용자의 장비 작동 권한이 무효화될 수 있습니다.

#### <a name="canada-and-usa"></a>캐나다 및 미국:

> ![알림 아이콘 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **알림:** &nbsp; 이 장비는 테스트되었으며, FCC 규칙의 파트 15에 따른 클래스 A 디지털 디바이스 관련 제한을 준수하는 것으로 나타났습니다. 이러한 제한은 장비가 상용 환경에서 작동할 때 유해한 간섭을 적절하게 차단할 수 있도록 설계되었습니다. 이 장비는 무선 주파수 에너지를 생성 및 사용하고 방출할 수 있으며, 사용 설명서에 따라 설치하고 사용하지 않을 경우 무선 통신에 해로운 간섭을 일으킬 수 있습니다. 주거 지역에서 장비를 작동하면 유해한 간섭이 발생할 수 있으며, 이때 사용자는 자신의 비용으로 간섭을 바로잡아야 합니다.

이 디바이스는 FCC 규칙 파트 15 및 Industry Canada 라이선스 면제 RSS 표준을 따릅니다. 작동 시 다음 두 조건이 적용됩니다. (1) 이 디바이스는 해로운 간섭을 일으키지 않을 것입니다. (2) 이 디바이스는 오작동을 일으킬 수 있는 간섭을 포함하여 모든 간섭을 받습니다.

![규정 정보 경고 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA 미국: (800) 426-9400 캐나다: (800) 933-4750

#### <a name="european-union"></a>유럽 연합:

EU 적합성 선언문의 복사본 요청. [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com)에 이메일을 보냅니다.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **경고!**

이 제품은 클래스 A 제품입니다. 국내 환경에서 이 제품은 무선 간섭을 일으킬 수 있으며 이 경우 사용자는 적절한 조치를 취해야 합니다.

폐 배터리와 전기 및 전자 장비의 폐기:

![경고 아이콘 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

제품, 제품의 배터리 또는 제품 패키지에 표시된 이 기호는 제품과 배터리를 일반 쓰레기와 함께 폐기하면 안 된다는 것을 의미합니다. 사용자에게는 배터리와 전기 및 전자 제품을 재활용할 수 있도록 해당 수거 지점에 가져다 놓을 책임이 있습니다. 이렇게 별도로 수거 및 재활용하면 자연 리소스를 절약할 수 있으며, 적절하게 폐기하지 않을 시 배터리와 전기 및 전자 제품에 함유된 유해 물질이 사람의 건강과 환경에 미칠 수 있는 악영향을 차단할 수 있습니다. 폐 배터리와 전기 및 전자 폐기물을 수거하는 위치는 현지 시청/지방 자치 사무실, 가정 폐기물 폐기 서비스 부서 또는 제품을 구입한 상점에 문의하세요. WEEE에 대한 추가 정보는 erecycle@microsoft.com에 문의하세요.

이 제품은 코인 셀을 포함하고 있습니다.

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL 전화번호: +353 1 295 3826 팩스 번호: +353 1 706 4110


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro R 배포 준비](azure-stack-edge-pro-r-deploy-prep.md)
