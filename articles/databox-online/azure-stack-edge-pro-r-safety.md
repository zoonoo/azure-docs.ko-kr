---
title: Azure Stack Edge Pro R 안전 가이드 | Microsoft Docs
description: 안전 규칙, 지침, 고려 사항 및 Azure Stack Edge Pro R 장치를 안전 하 게 설치 하 고 작동 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: dacc9ecc28ffa482b60d1e48735fe3620b5b7558
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363069"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge Pro R 안전 지침

![경고 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![안전성 고지 읽기 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**안전성 및 상태 정보 읽기**

Azure Stack Edge Pro R 장치를 사용 하기 전에이 문서의 모든 안전 정보를 읽으십시오. 지침을 따르지 않으면 속성에 대 한 화재, 감전, 부상 또는 손상이 발생할 수 있습니다. Azure Stack Edge Pro R을 사용 하기 전에 아래의 모든 안전 정보를 읽으십시오.

## <a name="safety-icon-conventions"></a>안전성 아이콘 표시 규칙

위험 경고 기호에 대 한 다음 신호 단어는 다음과 같습니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![위험물 기호](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **위험:** 피해를 주지 않는 경우에는 유해 또는 심각한 부상이 발생 하는 위험한 상황을 나타냅니다. <br> **경고:** 피해를 주지 않는 경우에는 유해 또는 심각한 부상이 발생할 수 있는 유해 상황을 나타냅니다. <br> **주의:** 피해를 주지 않는 경우 사소한 또는 보통 부상을 일으킬 수 있는 위험한 상황을 나타냅니다.|
|

다음 위험 아이콘은 Azure Stack Edge Pro R Edge 장치를 설정 하 고 실행할 때 관찰 됩니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![모든 지침을 먼저 읽을 것](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | 모든 지침을 먼저 읽을 것 |
| ![참고 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **고지:** | 중요하지만 위험과 관련되지 않은 것으로 간주되는 정보를 나타냅니다. || ![위험물 기호](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | 위험물 기호 |
| ![위험 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | 위험|
| ![무거움 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | 높은 무게 위험|
| ![감전 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | 전기 충격 위험 |
| ![사용자 서비스 가능 부품 없음 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | 사용자가 서비스할 필요가 없습니다. 제대로 교육을 받지 않은 경우 액세스하지 마세요. |
| ![여러 전원 소스 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | 여러 개의 전원 소스. 모든 전원 코드를 분리 하 여 장비에서 모든 기능을 제거 합니다. |
| ![집기 Points 아이콘](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | 집기 점이 있습니다. |
| ![핫 구성 요소 또는 표면 아이콘](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | 핫 구성 요소 또는 표면을 나타냅니다. |
|

## <a name="handling-precautions-and-site-selection"></a>예방 조치 및 사이트 선택 처리

![경고 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **경고:**

* 예를 들어, 제공 되는 장치를 이동 하 고 처리할 때 적절 한 장비 (예를 들어, 팔레트 잭) 및 개인 보호 장비 (PPE)를 사용 해야 합니다.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ 팁 위험 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **경고:**

* 잠재적 팁 또는 crushing 위험을 방지 하려면 장비를 평평 하 고 안정적이 고 안정 된 표면에 두십시오.
* 장치를 2 개 이상 스택에 추가 하지 마십시오.
* 스택 전에 시스템이 안전한 지 확인 합니다.
* 누적 장치를 이동 하거나 이동 하지 않습니다.
* 외부 케이블로 통해 강화 된 장치를 stack 하지 마십시오.
* 스택 작업 중에 전원 코드가 crushed 또는 손상 되지 않았는지 확인 합니다.
* 장치는 테이블 또는 작업 영역으로 사용 되지 않습니다.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 전기 충격 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ 사용자가 서비스할 필요가 없는 파트 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **주의:**

* *받은* 디바이스의 손상을 검사합니다. 장치 엔클로저가 손상 되 면 [Microsoft 지원에 문의](azure-stack-edge-contact-microsoft-support.md) 하 여 교체품을 얻으십시오. 디바이스를 작동하려 하지 마세요.
* 장치가 제대로 작동 하지 않는 것으로 의심 되 면 [Microsoft 지원에 문의](azure-stack-edge-contact-microsoft-support.md) 하 여 교체품을 구하십시오. 디바이스를 정비하려 하지 마세요.
* 디바이스에 사용자 서비스 가능 부품이 없습니다. 내부에 위험한 수준의 전압, 전류 및 에너지가 있습니다. 열지 마세요. 디바이스를 Microsoft로 보내서 점검을 받으세요.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 무거운 가중치 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **경고:**

* UPS의 전원 공급 장치 모듈을 제거 하면 UPS 내의 통해 강화 파트가 노출 됩니다. 전원 공급 모듈 구획 내에 외부 개체를 삽입 하지 마십시오.
* Azure Stack Edge Pro R Edge 장치를 직접 리프트 하지 마십시오. 인클로저는 52 kg 및 93 kg (115, 205,) 사이에서 평가 될 수 있습니다. 장비를 이동 하 고 떼는 경우 기계적 지원 또는 기타 적절 한 지원을 사용 합니다. 장비를 이동 하 고 떼는 경우 로컬 occupational 상태 및 안전 요구 사항을 준수 합니다.
* 적절 한 기계적 지원 없이 장비를 떼는 시도 하지 마십시오. 이 가중치를 리프트 하려고 하면 심각한 부상 발생할 수 있습니다.

![경고 아이콘](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)  **경고:**

* 시스템은 제어 된 환경에서 작동 하도록 설계 되었습니다. 다음 사이트를 선택 합니다.
  * Ventilated 및 radiators를 포함 하 여 열 원본에서 잘 작동 합니다.
  * 습기 또는 비에 노출 되지 않습니다.
  * 진동 및 물리적인 충격을 최소화 하는 공간에 있습니다.  시스템은 MIL-표준-810G에 따라 충격 및 진동 용으로 설계 되었습니다.
  * 전기 장치로 생성 된 강력한 전자기 필드에서 격리 됩니다.
  * 올바르게 접지 된 콘센트가 제공 됩니다.
  * 전원 공급 장치 코드에 액세스할 수 있는 충분 한 공간이 제품의 기본 전원 연결 해제로 제공 됩니다.
* 이더넷 케이블은 제품과 함께 제공 되지 않습니다. 전자기 간섭을 줄이려면 Cat 6 차폐 꼬아진 쌍 (STP) 케이블을 사용 하는 것이 좋습니다.
* 장비에 대 한 적절 한 공기 배치를 허용 하는 작업 영역에서 장비를 설정 합니다. 장치를 실행 하는 동안 front 및 back 커버를 완전히 제거 해야 합니다.
* 이더넷 케이블은 제품과 함께 제공 되지 않습니다. 전자기 간섭을 줄이려면 Cat 6 차폐 (STP) 케이블을 사용 하는 것이 좋습니다.
* Conductive contaminants를 사용 하 여 온도 제어 영역에서 장비를 설치 하 고 장비 주위에 적절 한 공기를 수 있습니다.
* 액체와 과도 하 게 humid 환경의 원본에서 장비를 떨어진 곳으로 유지 하세요.
* 액체 또는 외래 개체가 시스템에 들어가지 못하게 합니다. 음료 나 기타 액체 컨테이너를 시스템 또는 근처에 두지 마십시오.

## <a name="heater-precautions"></a>여는 예방 조치

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 핫 구성 요소 또는 표면 아이콘 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:** 

* 시스템 전원이 켜져 있는 동안 자동으로 인 한 작업은 여는 어셈블리 커버의 높은 표면 온도 때문에 터치 위험을 만들 수 있습니다. 시스템 전원이 켜져 있는 동안에는이 화면을 건드리지 마세요. 시스템이 꺼진 후 10 분 동안 휴지 기간을 허용 합니다.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 집기 points icon 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **warning:** 

* 시스템 전원이 켜져 있으면 후면 plenum 도어의 자동 actuation가 손가락으로 인 한 위험을 만들 수 있습니다. 시스템이 켜져 있으면이 영역을 계속 해 서 명확 하 게 유지 합니다.

## <a name="electrical-precautions"></a>전기 주의 사항

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 감전 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:**

* 전원 공급 코드에 안전한 전기 접지 연결을 제공하세요. 대체 전류 (AC) 코드에는 3 선 접지 플러그 (접지 핀이 있는 플러그)가 있습니다. 이 플러그는 접지된 AC 콘센트에만 적합합니다. 접지 핀의 용도를 무시하지 마세요.
* 전원 공급 디바이스 코드의 플러그가 기본 차단 디바이스라면, 소켓 콘센트가 디바이스 근처에 있고 쉽게 액세스할 수 있는지 확인하세요.
* 다음 조건 중 하나에 해당 하는 경우 전원 코드를 분리 하 고, 코드가 아닌 플러그를 당겨 분리 하 고, 모든 케이블의 연결을 끊습니다.

  * 전원 코드 또는 플러그가 해어지거나 손상되었습니다.
  * 디바이스 케이스에 무언가를 쏟았습니다.
  * 디바이스가 비 또는 과도한 습기에 노출되었습니다.
  * 디바이스가 바닥에 떨어져서 디바이스 케이스가 손상되었습니다.
  * 디바이스를 점검하거나 수리를 받아야 할 것 같습니다.
* 이동하기 전이나 어떤 방식으로든 손상될 것이라고 판단되는 장치 플러그를 영구적으로 뽑으십시오.
* 높은 누출 전류를 방지 하려면 단일 전송 사례에 하나 이상의 중단 없는 전원 공급 장치 (UPS)가 있는 경우 각 UPS를 독립 분기 회로에 연결 하는 것이 좋습니다. 그러나 각 UPS의 안전 접지에서 PDU의 단일 공급 장치 접지 컨덕터를 사용 하는 경우 PDU (전원 분배 장치) 또는 기타 장치를 사용 하는 경우 각 UPS의 외부에 있는 접지 터미널도 보조 건물 접지와 함께 사용 해야 합니다.

  > [!NOTE]
  > 추가 접지 컨덕터를 이미 가진 PDU를 사용 하는 경우 UPS에서 추가 접지 터미널을 사용 하지 않아도 됩니다.

* 다음 전원 사양을 충족하도록 전기 과부하 방지 기능이 있는 적절한 전원을 제공하세요.

  * 전압: 100 ~ 240 볼트 AC
  * 현재: 20 A, 전원 코드 당 최대값 전원 코드가 제공 됩니다.
  * 빈도: 50 ~ 60 Hz

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 전기 충격 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ 여러 전원 소스 ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **경고:**

* 무정전 전원 공급 장치 (UPS)가 없는 시스템의 경우 모든 AC 전원 코드를 분리 하 여 장비에서 AC 전원을 완전히 제거 합니다.
* UPS를 사용 하는 시스템의 경우 모든 AC 전원 코드를 분리 하 고 UPS 전원 스위치를 사용 하 여 시스템의 energize를 취소 합니다. UPS에는 위험한 AC 및 DC 전압이 포함 되어 있습니다.
* 시스템이 UPS를 포함 하는 경우 UPS는 차폐 입력 전원 케이블로 제공 됩니다. 보호 된 입력 전원 케이블을 사용 해야 하며, 코드를 바꾸거나 수정 하지 마십시오.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 감전 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:**

* UPS와 함께 장착 된 시스템의 경우 AC 및/또는 DC 전압은 항상 배터리 또는 유틸리티에서 생성 된 UPS 출력의 AC 전압에 잠재적 위험이 수반 됩니다. 장비 손상이 나 부상을 방지 하려면 기본 전원에서 분리 된 경우에도 UPS 출력에 전압이 있을 수 있다고 가정 합니다.
* UPS가 장착 된 시스템의 경우 모든 UPS 통해 강화 외부 연결은 여성입니다. 대/소문자를 제거 하거나 UPS의 커넥터에 아무것도 삽입 하지 마십시오.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 감전 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **경고:**

* 장치와 함께 제공 되는 것 외에는 AC 전원 코드를 수정 하거나 사용 하지 마세요.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 사용자가 서비스할 필요가 없는 파트 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **주의:**

* 이 장비에는 리튬 동전 셀 및/또는 리튬 아이언 phosphate 배터리가 포함 되어 있습니다. 장비 서비스를 시도 하지 마세요. 이 장비의 배터리는 사용자가 사용할 수 없습니다. 배터리가 잘못 된 유형으로 대체 된 경우 폭발의 위험.
* UPS의 배터리 모듈을 제거 하면 UPS 내의 통해 강화 파트가 노출 됩니다. 배터리 모듈 구획 내부에 외부 개체를 삽입 하지 마십시오.

## <a name="regulatory-information"></a>규정 정보

이 섹션에는 edge Pro R 장치에 대 한 규정 정보, 규정 모델 번호 (Azure Stack Edge Pro R)가 포함 되어 Azure Stack.

Azure Stack Edge Pro R Edge 장치는 NRTL (UL, CSA, ETL 등) 및 IEC/EN 60950-1 또는 IEC/EN 62368-1 규격 (표시 된) 정보 기술 장비와 함께 사용 하도록 설계 되었습니다.

장치는 다음과 같은 환경에서 작동 하도록 설계 되었습니다.

| 환경 | 사양 |
|:--- |:--- |
|온도 사양 | <ul><li>저장소 온도: – 33 &deg; c – 63 &deg; c (– 28 &deg; f-145 &deg; f) </li><li>연속 작업: 5 &deg; c – 43 &deg; c (41 &deg; f – 110 &deg; f)</li><li>최대 온도 그라데이션 (운영 및 저장소): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|상대 습도 사양 | <ul><li>저장소: 5% ~ 95% RH 33 &deg; C (91 &deg; F) 최대 dew 지점 대기는 항상 비응축이 아니어야 합니다.</li><li>운영: 29 &deg; C (84.2 &deg; F) 최대 dew point의 5% ~ 85% 상대 습도</li></ul> |
| 최대 고도 사양 | <ul><li>운영 (UPS 제외): 15000 ft (4572 미터)</li><li>운영 (UPS 포함): 1만 ft (3048 미터)</li><li>저장소: 4만 ft (12192 미터)</li></ul> |

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


> ![주의 아이콘-2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **통지:** &nbsp; Microsoft에서 명시적으로 승인 하지 않은 장비에 대 한 변경 또는 수정은 사용자의 장비 운영에 대 한 사용자의 권한을 무효로 만들 수 있습니다.

#### <a name="canada-and-usa"></a>캐나다 및 미국:

> ![알림 아이콘 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **통지:** &nbsp; 이 장비는 테스트 되었으며, FCC 규칙의 15 부분에 따라 클래스의 디지털 장치에 대 한 제한을 준수 하기 위해 발견 되었습니다. 이러한 제한은 장비가 상용 환경에서 작동할 때 유해한 간섭을 적절하게 차단할 수 있도록 설계되었습니다. 이 장비는 무선 주파수 에너지를 생성 및 사용하고 방출할 수 있으며, 사용 설명서에 따라 설치하고 사용하지 않을 경우 무선 통신에 해로운 간섭을 일으킬 수 있습니다. 상설 지역에서이 장비를 운영 하는 경우 사용자가 자신의 비용에 간섭을 수정 해야 하는 경우 유해한 간섭을 일으킬 수 있습니다.

이 디바이스는 FCC 규칙 파트 15 및 Industry Canada 라이선스 면제 RSS 표준을 따릅니다. 작동 시 다음 두 조건이 적용됩니다. (1) 이 디바이스는 해로운 간섭을 일으키지 않을 것입니다. (2) 이 디바이스는 오작동을 일으킬 수 있는 간섭을 포함하여 모든 간섭을 받습니다.

![규정 정보 경고 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


CAN SERVICES-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft 방법, Redmond, WA 98052, 미국 미국: (800) 426-9400 캐나다: (800) 933-4750

#### <a name="european-union"></a>유럽 연합:

EU 규정 준수 선언의 복사본을 요청 합니다. [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com)에 이메일을 보냅니다.

![경고 아이콘 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **경고!**

이 제품은 클래스 A 제품입니다. 국내 환경에서 이 제품은 무선 간섭을 일으킬 수 있으며 이 경우 사용자는 적절한 조치를 취해야 합니다.

폐 배터리와 전기 및 전자 장비의 폐기:

![경고 아이콘 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

제품, 제품의 배터리 또는 제품 패키지에 표시된 이 기호는 제품과 배터리를 일반 쓰레기와 함께 폐기하면 안 된다는 것을 의미합니다. 사용자에게는 배터리와 전기 및 전자 제품을 재활용할 수 있도록 해당 수거 지점에 가져다 놓을 책임이 있습니다. 이렇게 별도로 수거 및 재활용하면 자연 리소스를 절약할 수 있으며, 적절하게 폐기하지 않을 시 배터리와 전기 및 전자 제품에 함유된 유해 물질이 사람의 건강과 환경에 미칠 수 있는 악영향을 차단할 수 있습니다. 폐 배터리와 전기 및 전자 폐기물을 수거하는 위치는 현지 시청/지방 자치 사무실, 가정 폐기물 폐기 서비스 부서 또는 제품을 구입한 상점에 문의하세요. WEEE에 대한 추가 정보는 erecycle@microsoft.com에 문의하세요.

이 제품은 코인 셀을 포함하고 있습니다.

Microsoft 아일랜드 Sandyford Ind Est 더블린 D18 KX32 IRL 전화 번호: + 353 1 295 3826 팩스 번호: + 353 1 706 4110


## <a name="next-steps"></a>다음 단계

- [Edge Pro R Edge Azure Stack 배포 준비](azure-stack-edge-pro-r-deploy-prep.md)
