---
title: Avere vFXT 클러스터 튜닝 - Azure
description: Avere vFXT for Azure의 성능을 최적화하기 위한 사용자 지정 설정에 대해 간략히 설명합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152938"
---
# <a name="cluster-tuning"></a>클러스터 튜닝

대부분의 vFXT 클러스터는 사용자 지정 성능 설정에서 이점을 얻을 수 있습니다. 클러스터는 이러한 설정을 통해 특정 워크플로, 데이터 세트 및 도구에서 가장 효율적으로 작동합니다.

이 사용자 지정은 Avere 제어판에서 사용할 수 없는 기능을 구성할 수 있으므로 지원 담당자의 도움을 받아 수행해야 합니다.

이 섹션에서는 수행할 수 있는 일부 사용자 지정 튜닝에 대해 설명합니다.

## <a name="general-optimizations"></a>일반 최적화

이러한 변경은 데이터 세트 품질 또는 워크플로 스타일에 따라 권장될 수 있습니다.

* 쓰기 집약적 작업이 많은 경우 쓰기 캐시의 크기를 기본값인 20%에서 늘립니다.
* 데이터 세트에 작은 파일이 많이 포함된 경우 클러스터 캐시의 파일 수 제한을 늘립니다.
* 두 저장소 간에 데이터를 복사하거나 이동하는 작업이 포함된 경우 데이터 이동에 사용되는 스레드 수를 조정합니다.
  * 속도를 높이려면 사용되는 병렬 스레드 수를 늘릴 수 있습니다.
  * 백 엔드 스토리지 볼륨이 오버로드되는 경우 사용되는 병렬 스레드 수를 줄이는 것이 좋습니다.
* 클러스터에서 NFSv4 ACL을 사용하는 코어 파일러에 대한 데이터를 캐시하는 경우 액세스 모드 캐싱을 사용하도록 설정하여 특정 클라이언트에 대한 파일 권한 부여를 간소화합니다.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>클라우드 NAS 또는 클라우드 게이트웨이 최적화

클라우드 NAS 또는 게이트웨이 시나리오에서 vFXT 클러스터는 클라우드 컨테이너에 대한 NAS 스타일 액세스를 제공합니다. vFXT 클러스터와 클라우드 저장소 간의 더 높은 데이터 속도를 활용하려면 담당자가 캐시에서 저장소 볼륨으로 데이터를 보다 적극적으로 푸시하도록 설정을 변경하는 것이 좋습니다. 예를 들어:

* 클러스터와 스토리지 컨테이너 간의 TCP 연결 수를 늘립니다.

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>클라우드 버스팅 또는 하이브리드 WAN 최적화

클라우드 버스팅 시나리오 또는 하이브리드 스토리지 WAN 최적화 시나리오에서 vFXT 클러스터는 클라우드와 온-프레미스 하드웨어 스토리지 간의 통합을 제공합니다. 이러한 변경 사항은 도움이 될 수 있습니다.

* 클러스터와 코어 파일러 간에 허용되는 TCP 연결 수를 늘립니다.
* 원격 코어 파일러에 WAN 최적화 설정을 사용합니다(이 설정은 다른 Azure 지역의 원격 온-프레미스 파일러 또는 클라우드 코어 파일러에 사용할 수 있음).
* TCP 소켓 버퍼 크기 증가<sup>*</sup>
* "항상 전달" 설정을 사용하여 중복 캐시된 파일 감소<sup>*</sup>

<sup>*</sup>이러한 조정은 워크로드 및 성능 요구 사항에 따라 모든 시스템에 적용되지 않을 수 있습니다.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Avere vFXT for Azure 최적화 도움말

이러한 최적화에 대해 지원 담당자에게 문의하려면 [시스템에 대한 도움말 얻기에](avere-vfxt-open-ticket.md)설명된 절차를 사용합니다.
