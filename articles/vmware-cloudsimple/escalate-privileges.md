---
title: Azure VMware 솔루션 (AVS)-AVS 권한 에스컬레이션
description: Avs 사설 클라우드 vCenter에서 관리 기능을 수행 하기 위해 AVS 권한을 에스컬레이션 하는 방법에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025336"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>Avs 사설 클라우드 vCenter에서 관리 기능을 수행할 수 있도록 AVS 권한 에스컬레이션

AVS 권한 접근 방식은 vCenter 사용자에 게 일반 작업을 수행 하는 데 필요한 권한을 제공 하도록 설계 되었습니다. 특정 작업을 수행 하기 위해 사용자에 게 추가 권한이 필요할 수도 있습니다. 제한 된 기간 동안 vCenter SSO 사용자의 권한을 상승 시킬 수 있습니다.

권한을 에스컬레이션 하는 이유는 다음과 같습니다.

* Id 원본 구성
* 사용자 관리
* 분산 포트 그룹 삭제
* VCenter 솔루션 (예: 백업 앱) 설치
* 서비스 계정 만들기

> [!WARNING]
> 에스컬레이션 된 권한 상태에서 수행 되는 작업은 시스템에 부정적인 영향을 줄 수 있으며 시스템을 사용할 수 없게 될 수 있습니다. 에스컬레이션 기간 동안 필요한 동작만 수행 합니다.

AVS 포털에서 vCenter SSO의 CloudOwner 로컬 사용자에 대 한 [권한을 에스컬레이션](escalate-private-cloud-privileges.md) 합니다. VCenter에서 추가 id 공급자를 구성 하는 경우에만 원격 사용자의 권한을 확대할 수 있습니다. 권한 상승에는 선택한 사용자를 vSphere 기본 제공 관리자 그룹에 추가 하는 작업이 포함 됩니다. 사용자 한 명에 게 에스컬레이션 된 권한이 있을 수 있습니다. 다른 사용자의 권한을 에스컬레이션 해야 하는 경우 먼저 현재 사용자의 권한을 에스컬레이션 합니다.

추가 id 원본의 사용자를 CloudOwner 그룹의 멤버로 추가 해야 합니다.

> [!CAUTION]
> 새 사용자는 *클라우드-소유자-그룹*, *클라우드-글로벌-* 관리-그룹, 클라우드- *네트워크* -관리자- *그룹, 클라우드*-글로벌- *v m*i-관리 그룹에만 추가 해야 합니다.  *Administrators* 그룹에 추가 된 사용자는 자동으로 제거 됩니다.  서비스 계정만 *관리자* 그룹에 추가 해야 하며, 서비스 계정은 vsphere 웹 UI에 로그인 하는 데 사용 하면 안 됩니다.
에스컬레이션 기간 동안 AVS는 연결 된 경고 알림과 함께 자동화 된 모니터링을 사용 하 여 환경에 대 한 의도 하지 않은 변경 내용을 식별 합니다.
