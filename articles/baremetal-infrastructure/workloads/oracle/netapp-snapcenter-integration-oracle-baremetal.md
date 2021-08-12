---
title: BareMetal Infrastructure의 Oracle용 SnapCenter 통합
description: Oracle 및 NetApp의 스냅샷 기술을 사용하여 BareMetal Infrastructure의 Oracle 데이터베이스에 대한 운영 복구 백업을 만드는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.subservice: workloads
ms.date: 05/05/2021
ms.openlocfilehash: 4182a5fe97e356152b6d589fc6788b922e5c8493
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109520672"
---
# <a name="snapcenter-integration-for-oracle-on-baremetal-infrastructure"></a>BareMetal Infrastructure의 Oracle용 SnapCenter 통합

이 방법 가이드는 Oracle 및 NetApp의 스냅샷 기술을 사용하여 BareMetal 인프라의 Oracle 데이터베이스에 대한 운영 복구 백업을 만드는 데 도움이 됩니다. 스냅샷을 사용하는 것은 Oracle에 대한 몇 가지 데이터 보호 방법 중 하나일 뿐입니다. 스냅샷은 BareMetal Infrastructure에서 Oracle 데이터베이스를 실행하는 경우 가동 중지 시간 및 데이터 손실을 완화할 수 있습니다. 

>[!IMPORTANT]
>SnapCenter는 SAP 애플리케이션용 Oracle을 지원하지만 SAP BR\*Tools 통합은 제공하지 않습니다.

Oracle은 스냅샷에 대한 두 가지 백업 방법을 제공하지만 NetApp의 SnapCenter는 핫 백업 모드만 지원합니다. 핫 백업 모드는 Oracle 스냅샷 백업 및 만들기의 기존 버전입니다. 보관 로그를 적절하게 분류하기 위해 데이터베이스를 일시적으로 핫 백업 모드로 전환하려면 Oracle 호스트와 상호 작용해야 합니다. 또한 핫 백업 모드를 사용하면 RMAN 데이터베이스와의 상호 작용이 강화되어 복구에 사용 가능한 스냅샷을 보다 자세히 추적할 수 있습니다. 

이 가이드의 문서에서는 핫 백업 모드로 Oracle에 대한 운영 복구 및 재해 복구 프레임워크를 만드는 과정을 안내합니다. 재해 복구는 재해가 발생한 후 데이터베이스 또는 데이터베이스의 일부를 복구하는 것입니다. 잘못된 드라이브나 손상된 데이터베이스를 예로 들 수 있습니다. 운영 복구는 재해 이외의 상황에서 복구하는 것입니다. 비즈니스에 지장을 주지 않는 일부 데이터 행 손실을 예로 들 수 있습니다.

## <a name="snapcenter-database-organization"></a>SnapCenter 데이터베이스 구성
SnapCenter는 데이터베이스를 리소스 그룹으로 구성합니다. 리소스 그룹은 동일한 보호 정책을 사용하는 하나 이상의 데이터베이스가 될 수 있습니다. 따라서 백업의 일부인 개별 볼륨을 선택할 필요가 없습니다.

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-resource-group.png" alt-text="SnapCenter가 데이터베이스를 리소스 그룹으로 구성하는 방법을 보여주는 다이어그램" border="false":::

## <a name="oracle-disaster-recovery-architecture"></a>Oracle 재해 복구 아키텍처

Oracle 핫 백업은 데이터 파일을 복원한 후 보관 로그를 사용하여 롤포워드하기 위해 서로 다른 두 개의 백업으로 나뉩니다. 데이터 파일 및 제어 파일의 보호는 "매시간" 이루어지지만 더 긴 백업 빈도도 허용됩니다. 백업 간격이 길수록 복구 시간이 길어집니다.  

SnapCenter는 기본 데이터 파일 위치(nfs01)에 데이터베이스의 로컬 스냅샷을 만듭니다. 스냅샷은 데이터 파일 또는 제어 파일을 호스팅하는 각 파일 시스템에 생성됩니다. 이러한 백업은 데이터베이스의 빠른 복구를 보장합니다. 다중 디스크 오류 또는 사이트 오류의 데이터는 보호하지 않습니다. 

"매시간" 스냅샷 수는 백업 정책 집합에 따라 달라집니다. Oracle 데이터베이스에는 스냅샷에서 2-5일의 운영 복구 기능이 있습니다.
 
보관 로그가 충분해야 하며 가장 최근의 실행 가능한 데이터 파일 복구 지점에서 Oracle 데이터베이스를 롤포워드하는 데 필요합니다. 보관 로그의 스냅샷을 사용하여 Oracle 데이터베이스의 RPO(복구 지점 목표)를 줄입니다. 보관 로그에 대한 스냅샷 빈도가 짧을수록 RPO가 줄어듭니다. 보관 로그에 권장되는 스냅샷 간격은 5분 또는 15분입니다. 5분의 짧은 간격은 RPO가 가장 짧습니다.  더 많은 스냅샷을 복구 프로세스의 일부로 관리해야 하므로 간격이 짧을수록 복잡성이 증가합니다.

## <a name="next-steps"></a>다음 단계

Azure에서 Oracle BareMetal Infrastructure 서버로 트래픽을 라우팅하도록 NetApp SnapCenter를 설정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [트래픽을 라우팅하도록 SnapCenter 설정](set-up-snapcenter-to-route-traffic.md)
