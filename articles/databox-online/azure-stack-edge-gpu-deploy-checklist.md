---
title: Azure Stack Edge Pro GPU 장치를 배포 하기 위한 배포 전 검사 목록 | Microsoft Docs
description: 이 문서에서는 Azure Stack Edge Pro GPU 장치를 배포 하기 전에 수집할 수 있는 정보에 대해 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: 251b15f72c3216ad3ff4b2983c8cbbd2d9ec6579
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618253"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 장치에 대 한 배포 검사 목록  

이 문서에서는 Azure Stack Edge Pro 장치를 실제로 배포 하기 전에 수집할 수 있는 정보에 대해 설명 합니다. 

다음 검사 목록을 사용 하 여 Azure Stack Edge Pro 장치를 주문 하 고 장치를 받기 전에이 정보가 있는지 확인 합니다. 

## <a name="deployment-checklist"></a>배포 검사 목록 

| 단계                             | 매개 변수                                                                                                                                                                                                                           | 세부 정보                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 장치 관리               | <li>Azure 구독</li><li>등록 된 리소스 공급자</li><li>Azure Storage 계정</li>|<li>Edge Pro/Data Box Gateway, 소유자 또는 참가자 액세스를 Azure Stack 사용할 수 있습니다.</li><li>Azure Portal에서 **구독 > 리소스 공급자 > 홈 > 구독**으로 이동 합니다. 을 검색 `Microsoft.DataBoxEdge` 하 고 등록 합니다. `Microsoft.Devices`IoT 작업을 배포 하는 경우 반복 합니다.</li><li>액세스 자격 증명이 필요 합니다.</li> |
| 장치 설치               | 패키지의 전원 케이블 <br>우리에 게는 125 V에 대 한 SVE 18/3 케이블과 NEMA 5-15P to C13 (입/출력) 커넥터가 포함 된 15 암페어가 제공 됩니다. | 자세한 내용은 [국가별로 지원 되는 전원 코드](azure-stack-edge-technical-specifications-power-cords-regional.md) 의 목록을 참조 하세요.  |
|                                   | <li>포트 1에 대 한 하나 이상의 X 1-GbE RJ-45 네트워크 케이블  </li><li> 포트 3, 포트 4, 포트 5 또는 포트 6의 경우 최소 1 X 25gbe SFP + 구리 케이블</li>| 고객은 이러한 케이블을 조달 해야 합니다.<br>장치 네트워크 카드에 대해 지원 되는 네트워크 케이블, 스위치 및 트랜시버의 전체 목록은 [Cavium FastlinQ 41000 시리즈 상호 운용성 매트릭스](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) 및 [Mellanox 이중 포트 25g connectx-3-4 채널 네트워크 어댑터 호환 제품](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)을 참조 하세요.| 
| 첫 번째 장치 연결      | <li>IPv4 설정을 변경할 수 있는 랩톱입니다. 이 랩톱은 스위치 또는 USB에서 이더넷 어댑터를 통해 포트 1에 연결 합니다.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| 장치 로그인                      | 장치 관리자 암호는 8 자에서 16 자 사이 이며 대문자, 소문자, 숫자 및 특수 문자 중 세 가지를 포함 합니다.                                            | 기본 암호는 처음 로그인 할 때 만료 되는 *Password1* 입니다.                                                     |
| 네트워크 설정                  | 장치는 2 x 1Gbe, 4 x 25 GbE 네트워크 포트와 함께 제공 됩니다. <li>포트 1은 관리 설정만 구성 하는 데 사용 됩니다. 하나 이상의 데이터 포트를 연결 하 고 구성할 수 있습니다. </li><li> 포트 2-포트 6 사이에서 하나 이상의 데이터 네트워크 인터페이스를 인터넷에 연결 해야 합니다 (Azure에 대 한 연결 사용).</li><li> DHCP 및 고정 IPv4 구성이 지원 됩니다. | 고정 IPv4 구성에는 IP, DNS 서버 및 기본 게이트웨이가 필요 합니다.   |
| 네트워크 설정 계산     | <li>Kubernetes 노드에는 2 개의 무료, 정적, 연속 Ip가 필요 하 고 IoT Edge 서비스에는 고정 IP가 하나 필요 합니다.</li><li>배포 하는 각 추가 서비스 또는 모듈에 대해 하나의 추가 IP가 필요 합니다.</li>| 고정 IPv4 구성만 지원 됩니다.|
| 필드 웹 프록시 설정     | <li>웹 프록시 서버 IP/FQDN, 포트 </li><li>웹 프록시 사용자 이름, 암호</li> | Compute 설정에서 지원 되지 않는 웹 프록시입니다. |
| 방화벽 및 포트 설정        | 방화벽을 사용 하는 경우 [나열 된 url 패턴 및 포트가](azure-stack-edge-system-requirements.md#networking-port-requirements) 장치 ip에 허용 되는지 확인 합니다. |  |
| 바람직하지 시간 설정       | 표준 시간대, 기본 NTP 서버, 보조 NTP 서버를 구성 합니다. | 로컬 네트워크에서 기본 및 보조 NTP 서버를 구성 합니다.<br>로컬 서버를 사용할 수 없는 경우 공용 NTP 서버를 구성할 수 있습니다.                                                    |
| 필드 서버 설정 업데이트 | <li>로컬 네트워크의 업데이트 서버 IP 주소, WSUS 서버에 대 한 경로를 설정 해야 합니다. </li> | 기본적으로 공용 windows 업데이트 서버가 사용 됩니다.|
| 디바이스 설정 | <li>장치 정규화 된 도메인 이름 (FQDN) </li><li>DNS 도메인</li> | |
| 필드 인증서  | 비프로덕션 작업을 테스트 하려면 [인증서 생성 옵션](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) 을 사용 합니다. <br><br> 서명 체인을 포함 하 여 자체 인증서를 가져오는 경우 인증서를 적절 한 형식으로 [추가](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) 합니다.| 장치 이름 및/또는 DNS 도메인을 변경 하는 경우에만 인증서를 구성 합니다. |
| 정품 인증  | Azure Stack Edge Pro/Data Box Gateway 리소스의 정품 인증 키가 필요 합니다.    | 생성 된 키는 3 일 후에 만료 됩니다. |

<!--
| (Optional) MAC Address            | If MAC address needs to be whitelisted, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro 장치](azure-stack-edge-gpu-deploy-prep.md)를 배포할 준비를 합니다.
