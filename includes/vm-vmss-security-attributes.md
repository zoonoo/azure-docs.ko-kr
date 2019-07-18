---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800277"
---
## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| (예: 서버 쪽 암호화, 고객 관리 키를 사용 하 여 서버 쪽 암호화 및 기타 암호화 기능)는 미사용 데이터 암호화 | 예 | 참조 [Azure에서 Linux 가상 머신을 암호화 하는 방법](/azure/virtual-machines/linux/encrypt-disks.md) 하 고 [Windows VM에서 가상 디스크를 암호화](/azure/virtual-machines/windows/encrypt-disks.md)합니다. |
| (예: ExpressRoute 암호화 VNet 암호화 및 VNet 대 VNet 암호화에서) 전송 중 암호화| 예 | Azure 가상 컴퓨터 지원 [ExpressRoute](/azure/expressroute) 및 VNET 암호화 합니다. 참조 [전송 중 Vm에서 암호화](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms)합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 고객 관리 키가 지원 되는 Azure 암호화 시나리오; 참조 [Azure 암호화 개요](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md)합니다.|
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | HTTPS 및 SSL을 통해 |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 끝점 지원| 예 | |
| VNet 삽입 지원| 예 | 을 선택합니다. |
| 네트워크 격리 및 Firewalling 지원| 예 |  |
| 강제 터널링 지원| 예 | 참조 [강제 터널링 Azure Resource Manager 배포 모델을 사용 하 여 구성](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md)합니다. |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 참조 [모니터링 및 Azure에서 Linux 가상 머신을 업데이트](/azure/virtual-machines/linux/tutorial-monitoring.md) 하 고 [모니터링 하 고 Azure에서 Windows 가상 머신을 업데이트](/azure/virtual-machines/windows/tutorial-monitoring.md)합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 |  |
| Authorization| 예 |  |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사 | 아니요 |  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 |  | 
