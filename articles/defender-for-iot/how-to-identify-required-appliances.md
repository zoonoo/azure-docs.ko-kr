---
title: 필수 어플라이언스 식별
description: IoT 센서 및 온-프레미스 관리 콘솔의 인증 된 Defender에 대 한 하드웨어 및 가상 어플라이언스에 대해 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/21/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b22f880eee1b691b6b50b8151a64a2d501b891b3
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840342"
---
# <a name="identify-required-appliances"></a>필수 어플라이언스 식별

이 문서에서는 IoT 센서 어플라이언스의 인증 된 Defender에 대 한 정보를 제공 합니다. Defender 요새 IoT는 물리적 및 가상 어플라이언스에 배포할 수 있습니다.

여기에는 필수 소프트웨어를 다운로드 하 고 설치할 수 있는 구성 된 인증 어플라이언스 뿐만 아니라 소프트웨어가 이미 설치 되어 있는 *미리 구성* 된 인증 된 어플라이언스도 포함 됩니다.

또한이 문서에서는 온-프레미스 관리 콘솔 어플라이언스에 대 한 사양을 제공 합니다. 온-프레미스 관리 콘솔은 미리 구성 된 어플라이언스로 사용할 수 없습니다.

- 미리 구성 된 센서를 구매 하려면 [센서 어플라이언스](#sensor-appliances) 섹션에서 사용할 수 있는 모델을 검토 한 후 구매를 진행 합니다.

- 자신의 어플라이언스를 구매 하려면 [센서 어플라이언스](#sensor-appliances) 섹션 및 [추가 인증 된 어플라이언스](#additional-certified-appliances) 섹션에서 사용할 수 있는 모델을 검토 합니다. 어플라이언스를 취득 한 후에는 소프트웨어를 다운로드 하 여 설치할 수 있습니다.

- 온-프레미스 관리 콘솔을 구매 하려면 온 [-프레미스 관리 콘솔 어플라이언스](#on-premises-management-console-appliance) 섹션에서 정보를 검토 합니다. 장치를 확보 한 후에는 소프트웨어를 다운로드 하 여 설치할 수 있습니다.

여기에서 작업을 완료 한 후에는 소프트웨어를 설치 하 고 네트워크를 설정할 수 있습니다.

## <a name="sensor-appliances"></a>센서 어플라이언스

IoT 용 Defender는 물리적 및 가상 배포를 모두 지원 합니다.

### <a name="physical-sensors"></a>실제 센서

이 섹션에서는 사용할 수 있는 실제 센서 모델의 개요를 제공 합니다. 미리 구성 되지 않은 소프트웨어 또는 구입 센서를 사용 하 여 센서를 구입할 수 있습니다.

| 배포 유형 | 회사 | Enterprise | SMB |
|--|--|--|--|
| 이미지 | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="회사 수준 모델입니다."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="엔터프라이즈 수준 모델입니다."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="SMB 수준 모델입니다."::: |
| 모델 | HPE 프로라이언트 DL360 | HPE 프로라이언트 DL20 | HPE 프로라이언트 DL20 |
| 포트 모니터링 | 최대 15 개의 RJ45 또는 8 옵트인 | 최대 8 개 RJ45 또는 6 옵트인 | 4 RJ45 |
| 최대 대역폭 [1](#anchortext) | 초당 3gb | 초당 1gb | 초당 200 Mb |
| 최대 보호 장치 | 30,000 | 15,000 | 1,000 |

공급 업체 세부 정보는 [어플라이언스 사양](#appliance-specifications) 을 참조 하세요.

미리 구성 된 센서 정보: Microsoft는 화살표와 협력 하 여 미리 구성 된 센서를 제공 합니다. 미리 구성 된 센서를 구매 하려면 다음 주소에서 연락처 화살표를 이용 합니다. <hardware.sales@arrow.com>

사용자 고유의 어플라이언스를 가져오는 방법: 여기에 설명 된 지원 되는 모델을 검토 합니다. 어플라이언스를 가져온 후에는 **IoT**  >  **네트워크 센서 ISO** 설치용 Defender로 이동 하 여  >   소프트웨어를 다운로드 합니다.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="네트워크 센서 ISO.":::

<a id="anchortext">1</a> 대역폭 용량은 프로토콜의 배포에 따라 달라질 수 있습니다.

### <a name="virtual-sensors"></a>가상 센서

이 섹션에서는 사용할 수 있는 가상 센서의 개요를 제공 합니다.

| 배포 유형 | 회사 | Enterprise | SMB |
|--|--|--|--|
| 최대 대역폭 | 2.5 g b/초 | 800 m b/초 | 160 m b/초 |
| 최대 보호 장치 | 30,000 | 10000 | 2,500 |

## <a name="on-premises-management-console-appliance"></a>온-프레미스 관리 콘솔 어플라이언스

관리 콘솔은 가상 배포로 사용할 수 있습니다.

| 배포 유형 | Enterprise |
|--|--|
| 어플라이언스 유형 | HPE DL20, VM |
| 관리 센서 수 | 최대 300 |

온-프레미스 관리 콘솔을 얻은 후에는 **IoT**  >  **온-프레미스 관리 콘솔**  >  **iso 설치** 용 Defender로 이동 하 여 iso를 다운로드 합니다.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="온-프레미스 관리 콘솔":::

## <a name="appliance-specifications"></a>어플라이언스 사양

이 섹션에서는 다음 어플라이언스에 대 한 하드웨어 사양을 설명 합니다.

- 회사 배포: HPE 프로라이언트 DL360

- 엔터프라이즈 배포: HPE 프로라이언트 DL20

- SMB 배포: HPE 프로라이언트 DL20

- 가상 어플라이언스 사양

## <a name="corporate-deployment-hpe-proliant-dl360"></a>회사 배포: HPE 프로라이언트 DL360

| 구성 요소 | 기술 사양 |
|--|--|
| 섀시 | 1U 랙 서버 |
| 차원 | 42.9 x 43.46 x 70.7 (cm)/1.69 "x 17.11" x 27.83 "(의 경우) |
| 무게 | 최대 16.27 kg (35.86 lb) |
| 프로세서 | Intel Xeon 실버 4215 R 3.2 GHz, 11M 캐시, 8c/16T, 130 W |
| 칩셋은 | Intel C621 |
| 메모리 | 32 GB = 2 x 16gb 2666MT/s DDR4 ECC UDIMM |
| 스토리지 | Hot-Plug 하드 드라이브-RAID 5의 6 x 1.2-TB SAS 12G Enterprise 10K SFF (2.5 in) |
| 네트워크 컨트롤러 | 온-보드: 2 x 1gb Broadcom BCM5720<br>온-보드 LOM: iDRAC 포트 카드 1-Gb Broadcom BCM5720<br><br>외부: 1 x Intel 이더넷 i350 QP 1 Gb 서버 어댑터, 낮은 프로필 |
| 관리 | HPE iLO 고급 |
| 장치 액세스 | 두 개의 후면 USB 3.0<br>전면 USB 2.0 하나<br>단일 내부 USB 3.0 |
| 전력 | 2 x HPE 500 W 플렉스 슬롯 플래티넘 핫 플러그 저 Halogen 전원 공급 장치 키트 |
| 랙 지원 | HPE 1U Gen10 SFF Easy Install 레일 키트 |

### <a name="appliance-bom"></a>어플라이언스 BOM

| PN | 설명 | 수량 |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO Server | 1 |
| P19766-B21 | 유럽-다국어 지역화 | 1 |
| P24479-L21 | DL360 G10 용 Intel Xeon-S 4215 R FIO 키트 | 1 |
| P24479-B21 | DL360 Gen10 용 Intel Xeon-S 4215 R 키트 | 1 |
| P00922-B21 | HPE 16gb 2Rx8 PC4-2933Y-R 스마트 키트 | 2 |
| 872479-B21 | HPE 1.2-TB SAS 10K SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p BASE-T I350 어댑터 | 1 |
| P02377-B21 | HPE Smart 하이브리드 콘덴서 w \_ 145 Mm 케이블 | 1 |
| 804331-B21 | HPE 스마트 배열 P408i-a SR Gen10 Controller | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 Adapter | 1 |
| 871244-B21 | HPE DL360 Gen10 고성능 팬 키트 | 1 |
| 865408-B21 | HPE 500-W FS Cross-plat 핫 플러그 LH 전원 공급 장치 키트 | 2 |
| 512485-B21 | HPE iLO 고급 1-서버 라이선스 1 년 지원 | 1 |
| 874543-B21 | HPE 1U Gen10 SFF Easy Install 레일 키트 | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>엔터프라이즈 배포: HPE 프로라이언트 DL20

| 구성 요소 | 기술 사양 |
|--|--|
| 섀시 | 1U 랙 서버 |
| 차원 (높이 x 너비 x 깊이) | 4.32 x 43.46 x 38.22 cm/1.70 x 17.11 x 15.05 인치 |
| 무게 | 7.9 kg/17.41 lb |
| 프로세서 | Intel Xeon E-2234, 3.6 GHz, 4C/8T, 71 W |
| 칩셋은 | Intel C242 |
| 메모리 | 2 x 16gb 듀얼 랭크 x8 DDR4-2666 |
| 스토리지 | 3 x 1TB SATA 6G 중간 7.2 K SFF (2.5 in) – RAID 5 (스마트 배열 P408i)-SR 컨트롤러 |
| 네트워크 컨트롤러 | 온-보드: 2 x 1gb <br>온-보드: iLO 포트 카드 1gb <br>외부: 1 x HPE 이더넷 1-Gb 4-포트 366FLR 어댑터 |
| 관리 | HPE iLO 고급 |
| 장치 액세스 | 전면: 1 x USB 3.0, 1 x USB iLO 서비스 포트 <br>후면: 2 x USB 3.0 <br>내부: 1 x USB 3.0 |
| 전력 | 이중 핫 플러그 전원 공급 장치 500 W |
| 랙 지원 | HPE 1U 짧은 마찰 레일 키트 |

### <a name="appliance-bom"></a>어플라이언스 BOM

| PN | 설명: 높은 끝 | 수량 |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO 키트 | 1 |
| 879507-B21 | HPE 16gb 2Rx8 PC4-2Rx8-E STND 키트 | 2 |
| 655710-B21 | HPE 1TB SATA 7.2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM 라이저 키트 | 1 |
| 665240-B21 | HPE 이더넷 1-Gb 4-포트 366FLR 어댑터 | 1 |
| 782961-B21 | HPE 12-W 스마트 저장소 배터리 | 1 |
| 869081-B21 | HPE 스마트 배열 P408i-a SR G10 LH 컨트롤러 | 1 |
| 865408-B21 | HPE 500-W FS Cross-plat 핫 플러그 LH 전원 공급 장치 키트 | 2 |
| 512485-B21 | HPE iLO 고급 1-서버 라이선스 1 년 지원 | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS 활성화 FIO 키트 | 1 |
| 775612-B21 | HPE 1U 짧은 마찰 레일 키트 | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB 배포: HPE 프로라이언트 DL20

| 구성 요소 | 기술 사양 |
|--|--|
| 섀시 | 1U 랙 서버 |
| 차원 (높이 x 너비 x 깊이) | 4.32 x 43.46 x 38.22 cm/1.70 x 17.11 x 15.05 인치 |
| 무게 | 7.88 kg/17.37 lb |
| 프로세서 | Intel Xeon E-2224, 3.4 GHz, 4C, 71 W |
| 칩셋은 | Intel C242 |
| 메모리 | 1 x 8gb 듀얼 랭크 x8 DDR4-2666 |
| 스토리지 | 2 x 1TB SATA 6G 중간 7.2 K SFF (2.5 in) – RAID 1 (스마트 배열 P208i 포함) |
| 네트워크 컨트롤러 | 온-보드: 2 x 1gb <br>온-보드: iLO 포트 카드 1gb <br>외부: 1 x HPE 이더넷 1-Gb 4-포트 366FLR 어댑터 |
| 관리 | HPE iLO 고급 |
| 장치 액세스 | 전면: 1 x USB 3.0, 1 x USB iLO 서비스 포트 <br>후면: 2 x USB 3.0 <br>내부: 1 x USB 3.0 |
| 전력 | 핫 플러그 전원 공급 장치 290 W |
| 랙 지원 | HPE 1U 짧은 마찰 레일 키트 |

### <a name="appliance-bom"></a>어플라이언스 BOM

| PN | 설명 | 수량 |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO 키트 | 1 |
| 879505-B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND 키트 | 1 |
| 801882-B21 | HPE 1TB SATA 7.2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM 라이저 키트 | 1 |
| 665240-B21 | HPE 이더넷 1-Gb 4-포트 366FLR 어댑터 | 1 |
| 869079-B21 | HPE 스마트 배열 E208i-a SR G10 LH 컨트롤러 | 1 |
| P21649-B21 | HPE DL20 Gen10 Cross-plat 290 W FIO PSU 키트 | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC 케이블 키트 | 1 |
| 512485-B21 | HPE iLO 고급 1-서버 라이선스 1 년 지원 | 1 |
| 775612-B21 | HPE 1U 짧은 마찰 레일 키트 | 1 |

## <a name="virtual-appliance-specifications"></a>가상 어플라이언스 사양

### <a name="sensors"></a>센서

| 유형 | 회사 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| 메모리 | 32GB | 32GB | 8GB |
| 스토리지 | 5.6 TB | 1.8 TB | 500GB |

### <a name="on-premises-management-console-appliance"></a>온-프레미스 관리 콘솔 어플라이언스

| 유형 | Enterprise |
|--|--|
| 설명 | 엔터프라이즈 배포 유형의 가상 어플라이언스 |
| vCPU | 8 |
| 메모리 | 32GB |
| 스토리지 | 1.8 TB |

지원 되는 하이퍼바이저: VMware ESXi 버전 5.0 이상, Hyper-v

## <a name="additional-certified-appliances"></a>추가 인증 어플라이언스

이 섹션에서는 Microsoft에서 인증 했지만 미리 구성 된 어플라이언스로 제공 되지 않는 추가 어플라이언스에 대해 자세히 설명 합니다.

| 배포 유형 | Enterprise |
|--|--|
| 이미지 | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="엔터프라이즈 배포 유형입니다."::: |
| 모델 | Dell PowerEdge R340 XL |
| 포트 모니터링 | 최대 9 개의 RJ45 또는 6 옵트인 |
| 최대 대역폭 [1](#anchortext2)| 1G m b/초 |
| 최대 보호 장치 | 10000 |

<a id="anchortext2">One</a> 대역폭 용량은 프로토콜 배포에 따라 달라질 수 있습니다.

어플라이언스를 구매한 후에 **IoT**  >  **네트워크 센서 ISO** 설치용 Defender로 이동 하 여  >   소프트웨어를 다운로드 합니다.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="네트워크 센서 ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>엔터프라이즈 배포: Dell PowerEdge R340 XL

| 구성 요소 | 기술 사양 |
|--|--|
| 섀시 | 1U 랙 서버 |
| 차원 | 42.8 x 434.0 x 596 (mm)/1.67 "x 17.09" x 23.5 "(에서) |
| 무게 | 최대 29.98 lb/13.6 kg |
| 프로세서 | Intel Xeon E-2144G 3.6 g h z, 8M cache, 4C/8T, 터보 (71 W) |
| 칩셋은 | Intel C246 |
| 메모리 | 32 GB = 2 x 16gb 2666MT/s DDR4 ECC UDIMM |
| 스토리지 | 3 x 2tb 7.2 K RPM SATA 6-Gbps 512n 3.5-Hot-Plug 하드 드라이브-RAID 5 |
| 네트워크 컨트롤러 | 온-보드: 2 x 1gb Broadcom BCM5720<br>온-보드 LOM: iDRAC 포트 카드 1-Gb Broadcom BCM5720 <br><br>외부: 1 x Intel 이더넷 i350 QP 1 Gb 서버 어댑터, 낮은 프로필 |
| 관리 | iDRAC 9 Enterprise |
| 장치 액세스 | 두 개의 후면 USB 3.0 <br> 전면 USB 3.0 하나 |
| 전력 | 이중 핫 플러그 전원 공급 장치 350 W |
| 랙 지원 | ReadyRails II는 도구 감소를 위한 슬라이딩 레일 4 개, 사각형 또는 스레드 없는 라운드 구멍이 나 4-포스트 스레드 구멍 랙을 사용 하는 포스트 랙 (선택적 도구-없음 케이블 관리 arm)을 지원 합니다. |

## <a name="dell-r340-bom"></a>Dell R340 BOM

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 BOM.":::

## <a name="smb-deployment-neousys-nuvo-5006lp"></a>SMB 배포: Neousys Nuvo-5006LP

| 구성 요소 | 기술 사양 |
|--|--|
| 건설업 | 알루미늄, fanless 및 먼지 교정 디자인 |
| 차원 | 240 mm (W) x 225 mm (D) x 77 mm (H) |
| 무게 | 3.1 kg (CPU, 메모리 및 HDD 포함) |
| CPU | Intel Core i5-6500TE (6M 캐시, 최대 3.30 g h z) S1151 |
| 칩셋은 | Intel Q170 Platform Controller Hub |
| 메모리 | 8gb DDR4 2133 MHz 와이드 온도 SODIMM |
| 스토리지 | 128-GB 3ME3 와이드 온도 mSATA SSD |
| 네트워크 컨트롤러 | Intel I219의 6x 기가 비트 이더넷 포트 |
| 장치 액세스 | 4 USBs: 두 개의 rears, 하나의 내부 |
| 전원 어댑터 | 120/240VAC-20VDC/6A |
| 장착 | 탑재 키트, DIN 레일 |
| 운영 온도 | \-25 ° C ~ 70 ° C |
| 저장소 온도 | \-40 ° C ~ 85 ° C |
| 습도 | 10% ~ 90%, 비 비응축 |
| Vibration | 운영, 5 Grms, 5-500 Hz, 3 축 <br>(IEC60068-64에 따라) |
| 충격 | 운영, 50 Grms, 반 사인 11-ms duration (w/SSD, IEC60068-27에 따라) |
| EMC | EN-US 55022, EN 55024 및 EN 55032에 따라 CE/FCC 클래스 A |

## <a name="next-steps"></a>다음 단계

[IoT 설치용 Azure Defender 정보](how-to-install-software.md)

[IoT 용 Azure Defender 네트워크 설정 정보](how-to-set-up-your-network.md)
