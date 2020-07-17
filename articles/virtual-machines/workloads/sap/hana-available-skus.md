---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 SKU | Microsoft Docs
description: Azure(대규모 인스턴스)의 SAP HANA에 대한 SKU입니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7a487b105c8edc34d9427de3b8ca6738da1855a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84672105"
---
# <a name="available-skus-for-hli"></a>HLI에 사용 가능한 SKU

수정 버전 3 스탬프를 기반으로 하는 Azure (Large Instances) 서비스의 SAP HANA은의 Azure 지역에서 여러 구성으로 제공 됩니다.

- 오스트레일리아 동부
- 오스트레일리아 남동부
- 일본 동부
- 일본 서부

수정 버전 4 스탬프를 기반으로 하는 Azure (Large Instances) 서비스의 SAP HANA은의 Azure 지역에서 여러 구성으로 제공 됩니다.

- 미국 서부 2
- 미국 동부
- 미국 동부 2
- 미국 중남부
- 서유럽
- 북유럽



[HANA 대규모 인스턴스의 SAP HANA 인증 SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)는 다음과 유사합니다.

| SAP 솔루션 | 모델 | 메모리 | 스토리지 | 가용성 |
| --- | --- | --- | --- | --- |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | Azure의 SAP HANA S96<br /> – 2 x Intel® Xeon® Processor E7-8890 v4 <br /> 48개 CPU 코어 및 96개 CPU 스레드 |  768 GB |  3.0 TB | 사용 가능 |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | Azure S224의 SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112 CPU 코어 및 224 CPU 스레드 |  3.0 TB |  6.3 TB | 사용 가능 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | Azure S224m의 SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112 CPU 코어 및 224 CPU 스레드 |  6.0 TB |  10.5 TB | 사용 가능 |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | Azure S384에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  4.0 TB |  16TB | 사용 가능 |
|[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080)| Azure S384xm에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  6.0 TB |  18 TB | 사용 가능 (Rev 4에만 해당) |
| TDIv5 | Azure S384m에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  12.0 TB |  28 TB | 사용 가능 |
|[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | Azure S384xm에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  8.0 TB |  22 TB |  사용 가능  |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | Azure의 SAP HANA S576m<br /> – 12 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 288 CPU 코어 및 576 CPU 스레드 |  12.0 TB |  28 TB | 사용 가능 (Rev 4에만 해당) |
| TDIv5 | Azure S576xm에서 SAP HANA<br /> – 12 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 288 CPU 코어 및 576 CPU 스레드 |  18.0TB |  41TB | 사용 가능 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | Azure의 SAP HANA S768m<br /> – 16 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 384 CPU 코어 및 768 CPU 스레드 |  16.0 TB |  36 TB | 사용 가능 (Rev 4에만 해당) |
| TDIv5 | Azure S768xm에서 SAP HANA<br /> – 16 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 384 CPU 코어 및 768 CPU 스레드 |  24.0TB |  56TB | 사용 가능 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | Azure의 SAP HANA S960m<br /> – 20 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 480 CPU 코어 및 960 CPU 스레드 |  20.0 TB |  46 TB | 사용 가능 (Rev 4에만 해당) |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | Azure S896m의 SAP HANA<br /> – 16 x Intel® Xeon® 플래티넘 8276 프로세서 <br /> 448 CPU 코어 및 896 CPU 스레드 | 24.0TB | 35.8 TB | 사용 가능 (Rev 4에만 해당) |

- CPU 코어 = 서버 단위 프로세서 합계의 비하이퍼 스레드 CPU 코어 합계
- CPU 스레드 = 서버 단위 프로세서 합계의 하이퍼 스레드된 CPU 코어에서 제공하는 컴퓨팅 스레드 합계. 대부분의 단위는 기본적으로 하이퍼 스레딩 기술을 사용하도록 구성됩니다.
- 공급업체 권고에 따라 S768m, S768xm 및 S960m은 SAP HANA 실행에 하이퍼 스레딩을 사용하도록 구성되지 않았습니다.


SAP HANA TDIv5에서 SAP는 고객 특정 크기 조정 및 고객 관련 프로젝트를 허용 하며,이로 인해에 인증 된 것으로 나열 되지 않은 서버 구성이 발생할 수 있습니다.

- [SAP HANA 인증 어플라이언스](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA 인증 된 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

대부분의 경우 이러한 고객 관련 서버 구성은 SAP로 인증 된 서버 단위 보다 많은 메모리를 사용 합니다. SAP 작업 시 고객은 SAP 지원을 받을 수 있고 고객 특정 규모 서버 구성에 대해 확인할 수 있습니다. 

또한 SAP를 사용 하 여 인증 되지 않았지만 다음의 많은 인스턴스 표준 Sku를 사용할 수 있으며 Microsoft 가격 목록에서 구매할 수 있습니다.

 | 모델 | 총 메모리 | 메모리 DRAM | 메모리 Optane | 스토리지 | 가용성 |
| --- | --- | --- | --- | --- | --- |
| Azure S224oo의 SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112 CPU 코어 및 224 CPU 스레드 | 4.5 TB |  1.5 TB |  3.0 TB | 8.4 TB | 사용 가능 |
| Azure S224om의 SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112 CPU 코어 및 224 CPU 스레드 | 6.0 TB |  3.0 TB |  3.0 TB | 10.5 TB | 사용 가능 |
| Azure S224ooo의 SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112 CPU 코어 및 224 CPU 스레드 | 7.5 TB |  1.5 TB |  6.0 TB | 12.7 TB | 사용 가능 |
| Azure S224oom의 SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112 CPU 코어 및 224 CPU 스레드 | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB | 사용 가능 |
| Azure S448의 SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224 CPU 코어 및 448 CPU 스레드 | 6.0 TB |  6.0 TB |  --- | 10.5 TB | 사용 가능 (Rev 4에만 해당) |
| Azure S448m의 SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224 CPU 코어 및 448 CPU 스레드 | 12.0 TB |  12.0 TB |  --- | 18.9 TB | 사용 가능 (Rev 4에만 해당) |
| Azure S448oo의 SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224 CPU 코어 및 448 CPU 스레드 | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S448om의 SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224 CPU 코어 및 448 CPU 스레드 | 12.0 TB |  6.0 TB |  6.0 TB | 18.9 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S448ooo의 SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224 CPU 코어 및 448 CPU 스레드 | 15.0 TB |  3.0 TB |  12.0 TB | 23.2 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S448oom의 SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224 CPU 코어 및 448 CPU 스레드 | 18.0TB |  6.0 TB |  12.0 TB | 27.4 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S672의 SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336 CPU 코어 및 672 CPU 스레드 | 9.0 TB |  9.0 TB |  --- | 14.7 TB | 사용 가능 (Rev 4에만 해당) |
| Azure S672m의 SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336 CPU 코어 및 672 CPU 스레드 | 18.0TB |  18.0TB |  --- | 27.4 TB | 사용 가능 (Rev 4에만 해당) |
| Azure S672oo의 SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336 CPU 코어 및 672 CPU 스레드 | 13.5 TB |  4.5 TB |  9.0 TB | 21.1 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S672om의 SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336 CPU 코어 및 672 CPU 스레드 | 18.0TB |  9.0 TB |  9.0 TB | 27.4 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S672ooo의 SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336 CPU 코어 및 672 CPU 스레드 | 22.5TB |  4.5 TB |  18.0TB | 33.7 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S672oom의 SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336 CPU 코어 및 672 CPU 스레드 | 27.0 TB |  9.0 TB |  18.0TB | 40.0 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S896의 SAP HANA<br /> – 16 x Intel® Xeon® 플래티넘 8276 프로세서 <br /> 448 CPU 코어 및 896 CPU 스레드 | 12.0 TB |  12.0 TB |  --- | 18.9 TB | 사용 가능 (Rev 4에만 해당) |
| Azure S896oo의 SAP HANA<br /> – 16 x Intel® Xeon® 플래티넘 8276 프로세서 <br /> 448 CPU 코어 및 896 CPU 스레드 | 18.0TB |  6.0 TB |  12.0 TB | 27.4 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S896om의 SAP HANA<br /> – 16 x Intel® Xeon® 플래티넘 8276 프로세서 <br /> 448 CPU 코어 및 896 CPU 스레드 | 24.0TB |  12.0 TB |  12.0 TB | 35.8 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S896ooo의 SAP HANA<br /> – 16 x Intel® Xeon® 플래티넘 8276 프로세서 <br /> 448 CPU 코어 및 896 CPU 스레드 | 30.0 TB |  6.0 TB |  24.0TB | 44.3 TB  | 사용 가능 (Rev 4에만 해당) |
| Azure S896oom의 SAP HANA<br /> – 16 x Intel® Xeon® 플래티넘 8276 프로세서 <br /> 448 CPU 코어 및 896 CPU 스레드 | 36.0 TB |  12.0 TB |  24.0TB | 52.7 TB  | 사용 가능 (Rev 4에만 해당) |


> [!IMPORTANT]
> S72, S72m, S144, S144m, S192 및 S192m Sku는 아직 지원 되지 않지만 더 이상 구매할 수 없습니다. 

선택한 특정 구성은 워크로드, CPU 리소스 및 원하는 메모리에 따라 달라집니다. OLTP 워크로드에서 OLAP 워크로드에 최적화된 SKU를 사용할 수 있습니다. 

고객 특정 규모 프로젝트 단위를 제외하고 제공되는 하드웨어 기준은 SAP HANA TDI 인증입니다. SKU를 구분하는 두 가지 하드웨어 클래스는 다음과 같습니다.

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 및 S224m, S224oo, S224om, S224ooo, S224oom를 Sku의 "Type I class" 라고 합니다.
- 다른 모든 Sku는 Sku의 "Type II 클래스" 라고도 합니다.
- SAP 하드웨어 디렉터리에 아직 나열 되지 않은 Sku에 관심이 있는 경우 Microsoft 계정 팀에 문의 하 여 자세한 정보를 받으세요. 


HANA 대규모 인스턴스 스탬프 전체는 단일 고객용으로만 할당되지 않습니다. 이 사실은 Azure에 배포된 네트워크 패브릭을 통해 연결된 컴퓨팅 및 스토리지 리소스 랙에도 적용됩니다. HANA 대규모 인스턴스 인프라는 Azure와 마찬가지로 다음 세 가지 수준에서 서로 격리된 서로 다른 고객 &quot;테넌트&quot;를 배포합니다.

- **네트워크**: HANA Large Instance 스탬프 내의 가상 네트워크를 통해 격리 됩니다.
- **저장소**: 저장소 볼륨이 할당 되 고 테 넌 트 간 저장소 볼륨을 격리 하는 저장소 가상 머신을 통해 격리 됩니다.
- **Compute**: 서버 단위를 단일 테 넌 트에 전용으로 할당 합니다. 서버 장치에 대한 하드 분할 또는 소프트 분할이 없습니다. 테넌트 간 단일 서버 또는 호스트 단위의 공유 없음. 

서로 다른 테넌트 간의 HANA 대규모 인스턴스 장치 배포는 상호 간에 표시되지 않습니다. 서로 다른 테넌트에 배포된 HANA 대규모 인스턴스 장치는 HANA 대규모 인스턴스 스탬프 수준에서 서로 직접 통신 할 수 없습니다. 한 테넌트 내의 HANA 대규모 인스턴스 장치만 HANA 대규모 인스턴스 스탬프 수준에서 서로 통신할 수 있습니다.

대규모 인스턴스 스탬프에 배포된 테넌트는 청구를 위해 하나의 Azure 구독에 할당됩니다. 네트워크의 경우 동일한 Azure 등록 내에 있는 다른 Azure 구독의 가상 네트워크에서 액세스할 수 있습니다. 또한 동일한 Azure 지역의 또 다른 Azure 구독을 사용하여 배포할 경우 별도의 HANA 대규모 인스턴스 테넌트를 요청하기로 선택할 수 있습니다.

HANA 대규모 인스턴스에서 SAP HANA를 실행하는 것과 Azure에 배포된 VM에서 SAP HANA를 실행하는 것 사이에는 중요한 차이가 있습니다.

- Azure(큰 인스턴스)의 SAP HANA의 경우 가상화 계층이 없습니다. 기본적인 완전 복구 하드웨어의 성능을 얻을 수 있습니다.
- Azure와 달리 Azure(큰 인스턴스) 서버에서 SAP HANA는 특정 고객이 전용으로 사용할 수 있습니다. 서버 장치 또는 호스트가 하드 또는 소프트 분할될 가능성이 없습니다. 결과적으로 HANA 대규모 인스턴스 장치는 테넌트에 전체적으로 할당된 대로 사용되며, 이에 따라 사용자에게 제공됩니다. 서버를 다시 부팅하거나 종료해도 운영 체제 및 SAP HANA가 다른 서버에 자동으로 배포되지 않습니다. (유형 I 클래스 SKU의 경우, 유일한 예외는 서버에서 문제가 발생하고 다른 서버에서 다시 배포해야 하는 경우입니다.)
- Azure와 달리 최고의 가성비를 위해 호스트 프로세서 유형을 선택한 경우 Azure(큰 인스턴스)에서 SAP HANA에 대해 선택한 프로세서 유형은 Intel E7v3 및 E7v4 프로세서 제품군에서 최고의 성능을 보입니다.

**다음 단계**
- [HLI 크기 조정](hana-sizing.md) 참조
