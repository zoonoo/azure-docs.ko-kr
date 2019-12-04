---
title: 네트워킹에 대 한 Azure Security Center 권장 사항
description: 이 문서에는 네트워크 리소스를 보호 하는 데 도움이 되는 Azure Security Center의 보안 권장 사항이 나열 되어 있습니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781970"
---
# <a name="network-recommendations---reference-guide"></a>네트워크 권장 사항-참조 가이드

이 문서에서는 네트워크 토폴로지 및 인터넷 연결 끝점에 대 한 Azure Security Center에서 볼 수 있는 권장 사항의 전체 목록을 제공 합니다.

이러한 항목을 찾고 해결 하는 방법에 대 한 자세한 내용은 [여기](security-center-network-recommendations.md)를 참조 하세요.

## <a name="network-recommendations"></a>네트워크 권장 사항

|권장 사항 이름|설명|심각도|보안 점수|리소스 종류|
|----|----|----|----|----|----|
|서브넷 수준의 네트워크 보안 그룹을 사용 하도록 설정 해야 합니다.|네트워크 보안 그룹을 사용 하 여 서브넷에 배포 된 리소스에 대 한 네트워크 액세스를 제어 합니다.|높음/보통|30|서브넷|
|가상 컴퓨터는 네트워크 보안 그룹에 연결 해야 합니다.|가상 머신의 네트워크 액세스를 제어하기 위해 네트워크 보안 그룹을 활성화합니다.|높음/보통|30|가상 컴퓨터|
|인터넷 연결 Vm을 사용 하는 허용 되는 네트워크 보안 그룹에 대 한 액세스를 제한 해야 합니다.|기존 허용 규칙의 액세스를 제한 하 여 인터넷 연결 Vm의 네트워크 보안 그룹을 강화 합니다.|높음|20|가상 컴퓨터|
|IaaS NSGs의 웹 응용 프로그램에 대 한 규칙을 강화 해야 합니다.|웹 응용 프로그램 포트와 관련 하 여 과도 하 게 허용 되는 NSG 규칙을 사용 하 여 웹 응용 프로그램을 실행 하는 가상 컴퓨터의 NSG (네트워크 보안 그룹)를 강화 합니다.|높음|20|가상 컴퓨터|
|App Services에 대 한 액세스를 제한 해야 합니다.|너무 광범위 한 범위에서 인바운드 트래픽을 거부 하도록 네트워킹 구성을 변경 하 여 App Services에 대 한 액세스를 제한 합니다.|높음|10|App Service|
|가상 머신에서 관리 포트를 닫아야 합니다.|가상 머신의 네트워크 보안 그룹을 강화 하 여 관리 포트에 대 한 액세스를 제한 합니다.|높음|10|가상 컴퓨터|
DDoS Protection 표준을 사용하도록 설정해야 합니다.|DDoS protection service standard를 사용 하도록 설정 하 여 공용 Ip가 있는 응용 프로그램이 포함 된 가상 네트워크를 보호 합니다. DDoS protection을 사용 하면 네트워크 대규모 및 프로토콜 공격을 완화할 수 있습니다.|높음|10|가상 네트워크|
|가상 머신에서 IP 전달을 사용 하지 않도록 설정 해야 합니다.|IP 전달을 사용 하지 않습니다. 가상 컴퓨터의 NIC에서 IP 전달을 사용 하는 경우 컴퓨터는 다른 대상으로 주소가 지정 된 트래픽을 수신할 수 있습니다. IP 전달은 거의 필요 하지 않습니다 (예: VM을 네트워크 가상 어플라이언스로 사용 하는 경우). 따라서 네트워크 보안 팀에서 검토 해야 합니다.|중간|10|가상 컴퓨터|
|웹 애플리케이션에 HTTPS를 통해서만 액세스 가능|웹 응용 프로그램에 대해 "HTTPS만" 액세스를 사용 하도록 설정 합니다. HTTPS를 사용 하 여 서버/서비스 인증을 보장 하 고 전송 중인 데이터를 네트워크 계층 도청 공격 으로부터 보호 합니다.|중간|20|웹 애플리케이션|
|Just-in-time 네트워크 액세스 제어는 가상 컴퓨터에 적용 되어야 합니다.|JIT (just-in-time) VM (가상 컴퓨터) 액세스 제어를 적용 하 여 선택한 포트에 대 한 액세스 권한을 영구적으로 잠그고, 권한 있는 사용자가 제한 된 시간 동안만 JIT를 통해 해당 포트를 열 수 있도록 합니다.|높음|20|가상 컴퓨터|
|HTTPS를 통해서만 함수 앱에 액세스할 수 있어야 합니다.|함수 앱에 대해 "HTTPS만" 액세스를 사용 하도록 설정 합니다. HTTPS를 사용 하 여 서버/서비스 인증을 보장 하 고 전송 중인 데이터를 네트워크 계층 도청 공격 으로부터 보호 합니다.|중간|20|함수 앱|
|Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.|저장소 계정에 대 한 보안 전송을 사용 하도록 설정 합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용 하면 서버와 서비스 간의 인증을 보장할 뿐 아니라 메시지 가로채기 (man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격 으로부터 전송 중인 데이터를 보호 합니다.|높음|20|Storage 계정|


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

* [Id 및 액세스 모니터링](security-center-identity-access.md)
* [컴퓨터 및 응용 프로그램 보호](security-center-virtual-machine-protection.md)
* [Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)

