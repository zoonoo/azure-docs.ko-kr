---
title: Azure Stack Edge Mini R 안전 가이드 | Microsoft Docs
description: 안전 규칙, 참고 자료, 고려 사항을 설명하고 Azure Stack Edge Mini R 디바이스를 안전하게 설치하고 작동하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382630"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack Edge Mini R 안전 지침

![경고 아이콘 1](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![안전성 고지 읽기 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
**안전성 및 상태 정보 읽기**

Azure Stack Edge Mini R 디바이스(구성: 배터리 팩 1개, AC/DC 전원 공급 장치 1개, 모듈 전원 어댑터 1개, 서버 모듈 1개)를 사용하기 전에 이 문서에 있는 모든 안전 정보를 읽으세요. 지침을 따르지 않으면 화재, 감전, 부상 또는 속성 손상이 발생할 수 있습니다. Azure Stack Edge Mini R을 사용하기 전에 아래의 모든 안전 정보를 읽으세요.

## <a name="safety-icon-conventions"></a>안전성 아이콘 표시 규칙

위험 경고 기호 표시는 다음과 같습니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![위험 기호](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **위험:** 피하지 않을 경우 사망 또는 심각한 상처를 입는 위험한 상황을 나타냅니다. <br> **경고:** 피하지 않을 경우 사망 또는 심각한 상처를 입을 수 있는 위험한 상황을 나타냅니다. <br> **주의:** 피하지 않을 경우 최소 또는 보통 수준의 상처를 입을 수 있는 위험한 상황을 나타냅니다.|
|

다음 위험 아이콘은 Azure Stack Edge Mini R 디바이스를 설정하고 실행할 때 나타납니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![모든 지침을 먼저 읽을 것](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | 모든 지침을 먼저 읽을 것 |
| ![참고 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **고지:** | 중요하지만 위험과 관련되지 않은 것으로 간주되는 정보를 나타냅니다. |
| ![위험 기호](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | 위험 기호 |
| ![감전 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | 감전 위험 |
| ![실내 전용](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | 실내 전용 |
| ![사용자 서비스 가능 부품 없음 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | 사용자 서비스 가능 부품 없음. 제대로 교육을 받지 않은 경우 액세스하지 마세요. |
|

## <a name="handling-precautions-and-site-selection"></a>취급 주의 사항 및 사이트 선택

Azure Stack Edge Mini R 디바이스에는 다음과 같은 취급 주의 사항 및 사이트 선택 기준이 있습니다.

![경고 아이콘 2](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![감전 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![사용자 서비스 가능 부품 없음 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **주의:**

* *받은* 디바이스의 손상을 검사합니다. 디바이스 인클로저가 손상된 경우 [Microsoft 지원에 문의](azure-stack-edge-placeholder.md)하여 교체품을 받으세요. 디바이스를 작동하려 하지 마세요.
* 디바이스 오작동이 의심되는 경우 [Microsoft 지원에 문의](azure-stack-edge-placeholder.md)하여 교체품을 받으세요. 디바이스를 정비하려 하지 마세요.
* 디바이스에 사용자 서비스 가능 부품이 없습니다. 내부에 위험한 수준의 전압, 전류 및 에너지가 있습니다. 열지 마세요. 디바이스를 Microsoft로 보내서 점검을 받으세요.

![경고 아이콘 3](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

시스템은 다음과 같이 작동하는 것이 좋습니다.

* 직사광선 및 라디에이터를 포함한 열원에서 멀리 떨어져 있어야 합니다.
* 습기 또는 강우에 노출되지 않는 위치에 있어야 합니다.
* 진동 및 물리적 충격이 최소화되는 공간에 배치해야 합니다.  시스템은 MIL-STD-810G에 따라 충격 및 진동을 견딜 수 있게 설계되었습니다.
* 전기 장치에서 생성되는 강력한 전자기장에서 격리되어 있어야 합니다.
* 액체 또는 이물질이 시스템에 들어가지 못하게 하세요. 음료나 기타 액체 용기를 시스템에 또는 시스템 근처에 두지 마세요.

![경고 아이콘 4](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![사용자 서비스 가능 부품 없음 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **주의:**

* 이 장비에는 리튬 배터리가 포함되어 있습니다. 배터리 팩을 정비하려 하지 마세요. 이 장비의 배터리 팩은 사용자가 수리할 수 없습니다. 배터리를 잘못된 유형으로 교체할 경우 폭발 위험이 있습니다.

![경고 아이콘 5](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

배터리 팩은 Azure Stack Edge Mini R 디바이스에 장착된 상태에서만 충전하고 분리된 상태에서는 충전하지 마세요.

![경고 아이콘 6](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

* 배터리 팩의 ON/OFF 스위치는 배터리를 서버 모듈로 방전하기 위한 용도일 뿐입니다. 전원 어댑터가 배터리 팩에 연결되어 있으면 스위치가 OFF 위치에 있더라도 서버 모듈에 전원이 전달됩니다.

![경고 아이콘 7](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

* 배터리 팩을 태우거나 단락시키지 마세요. 재활용하거나 올바로 폐기해야 합니다.

![경고 아이콘 8](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

* 이 시스템에는 제공된 AC/DC 전원 공급 장치를 사용하는 대신 현장에서 제공하는 타입 2590 배터리를 사용할 수 있는 옵션도 제공됩니다. 이 경우 최종 사용자는 적용 가능한 모든 안전, 교통, 환경 및 기타 국가/지역 규정을 충족하는지 확인해야 합니다.
* 타입 2590 배터리를 사용하여 시스템을 작동하는 경우 배터리 제조업체에서 지정한 사용 조건 내에서 배터리를 작동합니다.

![경고 아이콘 9](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

이 디바이스에는 광학 트랜시버에 사용할 수 있는 두 개의 SFP+ 포트가 있습니다. 유해한 레이저 방사를 방지하려면 클래스 1 트랜시버만 사용하세요.

## <a name="electrical-precautions"></a>전기 주의 사항

Azure Stack Edge Mini R 디바이스에는 다음과 같은 전기 주의 사항이 있습니다.

![경고 아이콘 10](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![감전 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **경고:**

전원 공급 어댑터와 함께 사용하는 경우:

* 전원 공급 코드에 안전한 전기 접지 연결을 제공하세요. AC 코드에는 삼선 접지 플러그(접지 핀이 있는 플러그)가 있습니다. 이 플러그는 접지된 AC 콘센트에만 적합합니다. 접지 핀의 용도를 무시하지 마세요.
* 전원 공급 디바이스 코드의 플러그가 기본 차단 디바이스라면, 소켓 콘센트가 디바이스 근처에 있고 쉽게 액세스할 수 있는지 확인하세요.
* 다음 조건 중 하나에 해당하면 전원 코드를 분리하고(코드가 아닌 플러그를 당겨서) 모든 케이블의 연결을 끊으세요.

  * 전원 코드 또는 플러그가 해어지거나 손상되었습니다.
  * 디바이스가 비, 과도한 습기 또는 기타 액체에 노출되었습니다.
  * 디바이스가 떨어져 케이스가 손상되었습니다.
  * 디바이스를 점검하거나 수리를 받아야 할 것 같습니다.
* 이동하기 전이나 어떤 방식으로든 손상될 것이라고 판단되는 장치 플러그를 영구적으로 뽑으십시오.

* 다음 전원 사양을 충족하도록 전기 과부하 방지 기능이 있는 적절한 전원을 제공하세요.

* 전압: 100~240V AC
* 전류: 1.7A
* 주파수: 50~60Hz

![경고 아이콘 11](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![감전 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **경고:**

* 장비와 함께 제공되는 AC 전원 코드를 개조하려 하거나 다른 코드를 사용하지 마세요.

![경고 아이콘 12](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![감전 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![실내 전용](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **경고:**

* 이 기호가 부착된 전원 공급 장치는 실내 전용 등급입니다.

## <a name="regulatory-information"></a>규정 정보

다음은 Azure Stack Edge Mini R 디바이스(규정 모델 번호: TMA01)에 대한 규정 정보입니다.

Azure Stack Edge Mini R 디바이스는 NRTL Listed(UL, CSA, ETL 등) 및 IEC/EN 60950-1 또는 IEC/EN 62368-1 규격(CE 마크 획득) 정보 기술 장비에 사용하도록 설계되었습니다.

미국 및 캐나다 이외의 국가에서 네트워크 케이블(장비와 함께 제공되지 않음)은 길이가 3미터를 초과하는 경우 이 장비와 함께 설치하면 안 됩니다.

이 장비는 다음과 같은 환경에서 작동하도록 설계되었습니다.

| 환경 | 사양 |
|:---  |:--- |
| 시스템 온도 사양 | <ul><li>보관 온도: –20&deg;C~50&deg;C(–4&deg;F~122&deg;F)</li><li>연속 작동: 0&deg;C~40&deg;C(32&deg;F~104&deg;F)</li></ul> |
| 상대 습도(RH) 사양 | <ul><li>보관: 5~95% 상대 습도</li><li>작동: 10~90% 상대 습도</li></ul>|
| 최대 고도 사양 | <ul><li>작동: 15,000피트(4,572미터)</li><li>비작동: 40,000피트(12,192미터)</li></ul>|

> ![알림 아이콘 - 2](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **알림:** &nbsp;Microsoft의 명시적 승인 없이 장비를 변경 또는 수정할 경우 사용자의 장비 작동 권한이 무효화될 수 있습니다.

#### <a name="canada-and-usa"></a>캐나다 및 미국:

> ![알림 아이콘 - 3](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **알림:** &nbsp; 이 장비는 테스트되었으며, FCC 규칙의 파트 15에 따른 클래스 A 디지털 디바이스 관련 제한을 준수하는 것으로 나타났습니다. 이러한 제한은 장비가 상용 환경에서 작동할 때 유해한 간섭을 적절하게 차단할 수 있도록 설계되었습니다. 이 장비는 무선 주파수 에너지를 생성 및 사용하고 방출할 수 있으며, 사용 설명서에 따라 설치하고 사용하지 않을 경우 무선 통신에 해로운 간섭을 일으킬 수 있습니다. 주거 지역에서 장비를 작동하면 유해한 간섭이 발생할 수 있으며, 이 때 사용자는 자신의 비용으로 간섭을 바로잡아야 합니다.

이 장비와 함께 제공되는 Netgear A6150 WiFi USB Adapter는 인체 가까이 작동하기 위한 용도이며 인체 착용형 SAR(전자파 흡수율) 규정 준수에 대해 테스트되었습니다. FCC에 규정된 SAR 제한은 조직 질량 1g 평균 1.6W/kg입니다. 제품을 휴대하거나 착용한 상태로 사용하는 경우 RF 노출 요구 사항을 준수하도록 신체에서 10mm 거리를 유지하세요.

Netgear A6150 WiFi USB Adapter는 ANSI/IEEE C95.1-1999를 준수하며 OET Bulletin 65 Supplement C에 지정된 측정 방법 및 절차에 따라 테스트되었습니다.

Netgear A6150 SAR(전자파 흡수율): 조직 질량 1g 평균 1.18W/kg

Netgear A6150 WiFi USB Adapter에는 승인된 안테나만 사용해야 합니다. 이 디바이스 및 해당 안테나는 FCC 다중 송신기 제품 절차를 준수하는 경우를 제외하고 다른 안테나 또는 송신기와 함께 배치하거나 작동하지 않아야 합니다. 미국 시장에서 사용할 수 있는 제품의 경우 채널 1 ~ 11만 작동할 수 있습니다. 다른 채널은 선택할 수 없습니다.

5,150~5,250MHz 대역 작동은 공동 채널 모바일 위성 시스템에 유해한 간섭이 발생할 가능성을 줄이기 위해 실내 전용으로 제한됩니다.

![규정 정보 경고 - 실내 전용](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

고출력 레이더가 5,250~5,350MHz 및 5,650~5,850MHz의 기본 사용자(우선 순위 사용자)로 할당되고 이러한 레이더로 인해 LE-LAN 디바이스에 간섭 및/또는 손상이 발생할 수 있습니다.

이 장비는 무선 주파수 에너지를 생성 및 사용하고 방출할 수 있으며, 지침에 따라 설치하고 사용하지 않을 경우 무선 통신에 유해한 간섭을 일으킬 수 있습니다. 그러나 특정 설치에서 간섭이 발생하지 않는다는 보장은 없습니다.

이 장비가 라디오 또는 텔레비전 수신에 유해한 간섭을 일으키는 경우(장비를 껐다가 켜서 확인할 수 있음) 사용자는 다음 중 하나 이상의 조치를 통해 간섭을 교정하는 것이 좋습니다.

- 수신 안테나의 방향 또는 위치를 변경합니다.
- 장비와 수신기 사이의 거리를 늘립니다.
- 장비를 수신기가 연결된 것과 다른 회로의 콘센트에 연결합니다.
- 판매점 또는 숙련된 라디오/TV 기술자에게 도움을 요청합니다.

간섭 문제에 대한 자세한 내용은 FCC 웹 사이트([fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals))를 참조하세요. 또한 FCC(1-888-CALL FCC)에 전화를 걸어 간섭 및 전화 간섭 팩트 시트를 요청할 수도 있습니다.

무선 주파수 안정성에 대한 추가 정보는 FCC 웹 사이트([https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0)) 및 캐나다 산업부 웹 사이트([http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html))에서 찾을 수 있습니다.

이 제품은 규격 주변 디바이스를 사용하고 시스템 구성 요소 간에 차폐 케이블을 사용하는 조건에서 EMC 규정 준수를 입증했습니다. 라디오, 텔레비전 세트 및 기타 전자 디바이스에 간섭을 일으킬 가능성을 줄이기 위해 시스템 구성 요소 간에 규격 주변 디바이스 및 차폐 케이블을 사용하는 것이 중요합니다.

이 디바이스는 FCC 규칙 파트 15 및 Industry Canada 라이선스 면제 RSS 표준을 따릅니다. 작동 시 다음 두 조건이 적용됩니다. (1) 이 디바이스는 해로운 간섭을 일으키지 않을 것입니다. (2) 이 디바이스는 오작동을 일으킬 수 있는 간섭을 포함하여 모든 간섭을 받습니다.

![규정 정보 경고 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA

미국: (800) 426-9400

캐나다: (800) 933-4750

Netgear A6150 WiFi USB Adapter FCC ID: PY318300429
 
Netgear A6150 WiFi USB Adapter IC ID: 4054A-18300429

이 장비와 함께 제공되는 Netgear A6150 WiFi USB Adapter는 IC RSS-102의 일반인/비통제 노출 제한에 대한 SAR을 준수하며 IEEE 1528에 지정된 측정 방법 및 절차에 따라 테스트되었습니다. 신체 착용 조건에서는 최소 10mm의 거리를 유지하세요.

Netgear A6150 WiFi USB Adapter는 비통제 환경에 대해 설정된 캐나다 휴대형 RF 노출 제한을 준수하며 설명서에 기재된 대로 의도된 작업에 안전합니다. 제품을 신체에서 최대한 멀리 유지하거나 디바이스를 저출력 전원으로 설정하여(해당 기능을 사용할 수 있는 경우) RF 노출을 추가로 줄일 수 있습니다.

각 제품의 조직 질량 1g 평균 SAR(전자파 흡수율) 표는 위의 미국 섹션에서 확인할 수 있습니다.

![규정 정보 경고 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>유럽 연합:

이 장비의 EU 적합성 선언문 복사본을 요청하세요. [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com)에 이메일을 보냅니다.

이 장비와 함께 제공되는 Netgear A6150 WiFi USB Adapter는 지침 2014/53/EU를 준수하며 요청 시 이 지침을 제공받을 수 있습니다.

![경고 아이콘 13](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **경고:**

이 제품은 클래스 A 제품입니다. 국내 환경에서 이 제품은 무선 간섭을 일으킬 수 있으며 이 경우 사용자는 적절한 조치를 취해야 합니다.

폐 배터리와 전기 및 전자 장비의 폐기:

![경고 아이콘 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

제품, 제품의 배터리 또는 제품 패키지에 표시된 이 기호는 제품과 배터리를 일반 쓰레기와 함께 폐기하면 안 된다는 것을 의미합니다. 사용자에게는 배터리와 전기 및 전자 제품을 재활용할 수 있도록 해당 수거 지점에 가져다 놓을 책임이 있습니다. 이렇게 별도로 수거 및 재활용하면 자연 리소스를 절약할 수 있으며, 적절하게 폐기하지 않을 시 배터리와 전기 및 전자 제품에 함유된 유해 물질이 사람의 건강과 환경에 미칠 수 있는 악영향을 차단할 수 있습니다. 폐 배터리와 전기 및 전자 폐기물을 수거하는 위치는 현지 시청/지방 자치 사무실, 가정 폐기물 폐기 서비스 부서 또는 제품을 구입한 상점에 문의하세요. WEEE에 대한 추가 정보는 erecycle@microsoft.com에 문의하세요.

이 제품은 코인 셀을 포함하고 있습니다.

이 장비와 함께 제공되는 Netgear A6150 WiFi USB Adapter는 인체 가까이 작동하기 위한 용도이며 인체 착용형 SAR(전자파 흡수율) 규정 준수에 대해 테스트되었습니다(아래 값 참조). 제품을 휴대하거나 착용한 상태로 사용하는 경우 RF 노출 요구 사항을 준수하도록 신체에서 10mm 거리를 유지하세요.

**Netgear A6150 SAR(전자파 흡수율):** 조직 질량 10g 평균 0.54W/kg

 
이 디바이스는 모든 EU 회원국에서 작동할 수 있습니다. 디바이스가 사용되는 국가 및 지역 규정을 준수하세요. 이 디바이스는 다음 국가에서 5,150~5,350MHz 주파수 범위로 작동하는 경우 실내 전용으로 제한됩니다.  

![실내 전용을 요구하는 EU 국가](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

다음 표에서는 RED의 10.8(a) 및 10.8(b) 조항에 따라 EU에서 판매되는 Netgear 무선 제품의 사용 주파수 대역 및 최대 RF 송신 출력에 대한 정보를 제공합니다.

**WiFi**

| 주파수 범위(MHz) | 사용되는 채널 | 최대 송신 출력(dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2,400~2,483.5 | 1~13    | ODFM: 19.9dBm(97.7mW) <br> CCK: 17.9dBm(61.7mW) |
| 5,150~5,320   | 36~48   | 22.9dBm(195mW) |
| 5,250~5,350   | 52~64   | 22.9dBm(195mW), TPC <br> 19.9dBm(97.7mW), 비 TPC |
| 5,470~5,725   | 100~140 | 29.9dBm(977mW), TPC <br> 29.6dBm(490mW), 비 TPC |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

전화 번호: +353 1 295 3826

팩스 번호: +353 1 706 4110

#### <a name="singapore"></a>싱가포르:

이 장비와 함께 제공되는 Netgear A6150 WiFi USB Adapter는 IMDA 표준을 준수합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Mini R 배포 준비](azure-stack-edge-mini-r-deploy-prep.md)
