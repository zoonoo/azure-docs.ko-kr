---
title: Azure Stack Edge Pro GPU 디바이스를 배포하기 위한 배포 전 검사 목록 | Microsoft Docs
description: 이 문서에서는 Azure Stack Edge Pro GPU 디바이스를 배포하기 전에 수집할 수 있는 정보에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/07/2021
ms.author: alkohli
ms.openlocfilehash: 9bb7f552fccf4b6f58def2046b80473a57e6116f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756386"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에 대한 배포 체크리스트  

이 문서에서는 Azure Stack Edge Pro 디바이스를 실제로 배포하기 전에 수집할 수 있는 정보에 대해 설명합니다. 

다음 검사 목록을 사용하여 Azure Stack Edge Pro 디바이스를 주문하고 디바이스를 받기 전에 이 정보가 있는지 확인합니다. 

## <a name="deployment-checklist"></a>배포 검사 목록 

| 단계                             | 매개 변수                                                                                                                                                                                                                           | 세부 정보                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 디바이스 관리               | <li>Azure 구독</li><li>등록된 리소스 공급자</li><li>Azure Storage 계정</li>|<li>Azure Stack Edge Pro/Data Box Gateway, 소유자 또는 참가자 액세스를 사용할 수 있습니다.</li><li>Azure Portal에서 **홈 > 구독 > 사용자의 구독 > 리소스 공급자** 로 차례로 이동합니다. `Microsoft.DataBoxEdge`를 검색하고 등록합니다. IoT 워크로드를 배포하는 경우 `Microsoft.Devices`에 반복합니다.</li><li>액세스 자격 증명 필요</li> |
| 장치 설치               | 패키지의 전원 케이블 <br>미국에서는 125V 및 15A 등급의 SVE 18/3 케이블과 NEMA 5-15P에서 C13(입/출력) 커넥터가 제공됩니다. | 자세한 내용은 [국가별로 지원되는 전원 코드](azure-stack-edge-technical-specifications-power-cords-regional.md)의 목록을 참조하세요.  |
|                                   | <li>포트 1을 위한 1-GbE RJ-45 네트워크 케이블 1개 이상  </li><li> 포트 3, 포트 4, 포트 5 또는 포트 6에 대해 하나 이상의 X 25/10-GbE SFP+ 구리 케이블</li>| 고객은 해당 케이블을 구매해야 합니다.<br>Cavium의 디바이스 네트워크 카드용으로 지원되는 네트워크 케이블, 스위치 및 트랜시버의 전체 목록은 [Cavium FastlinQ 41000 시리즈 상호 운용성 매트릭스](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)를 참조하세요.<br>Mellanox의 25GbE 및 10GbE에 대해 지원되는 케이블 및 모듈의 전체 목록은 [Mellanox 듀얼 포트 25G ConnectX-4 채널 네트워크 어댑터 호환 제품](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)을 참조하세요.| 
| 최초 디바이스 연결      | <li>IPv4 설정을 변경할 수 있는 랩톱입니다. 이 랩톱은 스위치 또는 USB에서 이더넷 어댑터를 통해 포트 1에 연결합니다.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| 디바이스 로그인                      | 디바이스 관리자 암호(대문자, 소문자, 숫자, 특수문자 중 세 가지 문자 유형을 포함하여 8~16자)                                            | 기본 암호는 *Password1* 이며, 이 암호는 최초 로그인 시 만료됩니다.                                                     |
| 네트워크 설정                  | 디바이스에는 1-GbE, 4 x 25-GbE 네트워크 포트 2개가 제공됩니다. <li>포트 1은 관리 설정 구성에만 사용됩니다. 하나 이상의 데이터 포트를 연결하고 구성할 수 있습니다. </li><li> 포트 2 ~ 포트 6 중 하나 이상의 데이터 네트워크 인터페이스를 인터넷에 연결해야 합니다(Azure에 연결).</li><li> DHCP 및 정적 IPv4 구성이 지원됩니다. | 정적 IPv4 구성에는 IP, DNS 서버, 기본 게이트웨이가 필요합니다.   |
| 컴퓨팅 네트워크 설정     | <li>Kubernetes 노드에는 무료 정적 연속 IP 2개, IoT Edge 서비스에는 정적 IP 1개가 필요합니다.</li><li>배포할 각 추가 서비스 또는 모듈에는 추가 IP 1개가 필요합니다.</li>| 정적 IPv4 구성만 지원됩니다.|
| (선택 사항) 웹 프록시 설정     | <li>웹 프록시 서버 IP/FQDN, 포트 </li><li>웹 프록시 사용자 이름, 암호</li> |  |
| 방화벽 및 포트 설정        | 방화벽을 사용하는 경우 [나열된 URL 패턴 및 포트가](azure-stack-edge-system-requirements.md#networking-port-requirements) 디바이스 IP에 허용되는지 확인합니다. |  |
| (권장) 시간 설정       | 표준 시간대, 기본 NTP 서버, 보조 NTP 서버를 구성합니다. | 로컬 네트워크에서 기본 및 보조 NTP 서버를 구성합니다.<br>로컬 서버를 사용할 수 없는 경우 퍼블릭 NTP 서버를 구성할 수 있습니다.                                                    |
| (선택 사항) 업데이트 서버 설정 | <li>로컬 네트워크의 업데이트 서버 IP 주소, WSUS 서버 경로가 필요합니다. </li> | 기본적으로 공용 Windows 업데이트 서버가 사용됩니다.|
| 디바이스 설정 | <li>디바이스 정규화된 도메인 이름(FQDN) </li><li>DNS 도메인</li> | |
| (선택 사항) 인증서  | 비 프로덕션 워크로드를 테스트하려면 [인증서 생성 옵션](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)을 사용합니다. <br><br> 서명 체인을 포함하여 자체 인증서를 가져오는 경우 [인증서를 적절한 형식으로 추가](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates)합니다.| 디바이스 이름 및/또는 DNS 도메인을 변경하는 경우에만 인증서를 구성합니다. |
| 정품 인증  | Azure Stack Edge Pro/Data Box Gateway 리소스의 활성화 키가 필요합니다.    | 생성된 키는 3일 후에 만료됩니다. |

<!--
| (Optional) MAC Address            | If MAC address needs to be on the allowed list, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro 디바이스](azure-stack-edge-gpu-deploy-prep.md)를 배포할 준비를 합니다.
