---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 520fd50b2b0864f43c08687f05de377679b36d84
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886926"
---
## <a name="preventative"></a>예방

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 | [Azure에서 Linux 가상 머신을 암호화](/azure/virtual-machines/linux/encrypt-disks) 하 고 [Windows VM에서 가상 디스크를 암호화](/azure/virtual-machines/windows/encrypt-disks)하는 방법을 참조 하세요. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | Azure Virtual Machines는 [express](/azure/expressroute) 경로 및 VNet 암호화를 지원 합니다. [Vm의 전송 중 암호화를](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)참조 하세요. |
| 암호화 키 처리 (CMK, BYOK 등)| 예 | 고객 관리 키는 지원 되는 Azure 암호화 시나리오입니다. [Azure 암호화 개요](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)를 참조 하세요.|
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | HTTPS 및 SSL을 통해. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | |
| VNet 삽입 지원| 예 | 을 선택합니다. |
| 네트워크 격리 및 방화벽 지원| 예 |  |
| 강제 터널링 지원| 예 | [Azure Resource Manager 배포 모델을 사용 하 여 강제 터널링 구성을](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)참조 하세요. |

## <a name="detection"></a>감지

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Azure에서 [Linux 가상 머신 모니터링 및 업데이트](/azure/virtual-machines/linux/tutorial-monitoring) 및 [azure에서 Windows 가상 머신 모니터링 및 업데이트](/azure/virtual-machines/windows/tutorial-monitoring)를 참조 하세요. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 |  |
| Authorization| 예 |  |


## <a name="audit-trail"></a>감사 내역

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사 | 아니요 |  |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 |  | 
