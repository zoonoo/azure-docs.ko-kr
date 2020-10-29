---
title: 관리자 권한으로 액세스 하는 동안 금지 된 작업
description: Vmware 엔진은 다음과 같은 금지 된 작업을 검색할 때 변경 내용을 되돌리고 서비스가 중단 되지 않은 상태로 유지 되도록 합니다.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92915435"
---
# <a name="forbidden-actions-during-elevated-access"></a>관리자 권한으로 액세스 하는 동안 금지 된 작업

권한 상승 시간 간격 동안 일부 작업은 사용할 수 없습니다. VMware 엔진에서 다음과 같은 사용할 수 없는 작업을 검색할 때 VMware 엔진은 변경 내용을 되돌리고 서비스가 중단 되지 않은 상태로 유지 되도록 합니다.

## <a name="cluster-actions"></a>클러스터 작업

- VCenter에서 클러스터 제거
- 클러스터에서 vSphere HA (고가용성)를 변경 합니다.
- VCenter에서 클러스터에 호스트를 추가 합니다.
- VCenter에서 클러스터의 호스트를 제거 합니다.

## <a name="host-actions"></a>호스트 작업

- ESXi 호스트에서 데이터 저장소를 제거 하는 중입니다.
- 호스트에서 vCenter 에이전트를 제거 하는 중입니다.
- 호스트 구성을 수정 하는 중입니다.
- 호스트 프로필을 변경 합니다.
- 호스트를 유지 관리 모드에 둡니다.

## <a name="network-actions"></a>네트워크 작업

- 사설 클라우드에서 기본 분산 가상 스위치 (DVS)를 삭제 합니다.
- 기본 DVS에서 호스트를 제거 합니다.
- DVS 설정을 가져오는 중입니다.
- DVS 설정을 다시 구성 합니다.
- DVS를 업그레이드 하는 중입니다.
- 관리 인를 삭제 하는 중입니다.
- 관리 인 편집

## <a name="roles-and-permissions-actions"></a>역할 및 사용 권한 작업

- 전역 역할 만들기
- 관리 개체에 대 한 사용 권한을 수정 하거나 삭제 합니다.
- 기본 역할 수정 또는 제거
- 역할의 권한을 클라우드 소유자 역할 보다 높게 늘립니다.

## <a name="other-actions"></a>다른 작업

- 기본 라이선스 제거:
  - vCenter Server
  - ESXi 노드
  - NSX-T
  - HCX
- 관리 리소스 풀을 수정 하거나 삭제 하는 중입니다.
- 관리 Vm을 복제 합니다.


## <a name="next-steps"></a>다음 단계
[CloudSimple 유지 관리 및 업데이트](cloudsimple-maintenance-updates.md) 
