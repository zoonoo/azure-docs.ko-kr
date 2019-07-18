---
title: Avere vFXT 클러스터 튜닝 - Azure
description: Avere vFXT for Azure의 성능을 최적화하기 위한 사용자 지정 설정에 대해 간략히 설명합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409168"
---
# <a name="cluster-tuning"></a>클러스터 튜닝


대부분의 vFXT 클러스터는 사용자 지정 성능 설정에서 이점을 얻을 수 있습니다. 클러스터는 이러한 설정을 통해 특정 워크플로, 데이터 세트 및 도구에서 가장 효율적으로 작동합니다. 

이 사용자 지정은 일반적으로 Avere 제어판에서 사용할 수 없는 기능을 구성하므로 지원 담당자와 함께 수행해야 합니다.

이 섹션에서는 수행할 수 있는 몇 가지 사용자 지정 튜닝에 대해 설명합니다.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>일반 최적화

이러한 변경은 데이터 세트 품질 또는 워크플로 스타일에 따라 권장될 수 있습니다.

* 쓰기 집약적 작업이 많은 경우 쓰기 캐시의 크기를 기본값인 20%에서 늘립니다. 
* 데이터 세트에 작은 파일이 많이 포함된 경우 클러스터 캐시의 파일 수 제한을 늘립니다. 
* 두 저장소 간에 데이터를 복사하거나 이동하는 작업이 포함된 경우 데이터 이동에 사용되는 스레드 수를 조정합니다. 
  * 속도를 높이려면 사용되는 병렬 스레드 수를 늘릴 수 있습니다.
  * 백 엔드 저장소 볼륨이 오버로드되는 경우 사용되는 병렬 스레드 수를 줄이는 것이 좋습니다.
* 클러스터에서 NFSv4 ACL을 사용하는 코어 파일러에 대한 데이터를 캐시하는 경우 액세스 모드 캐싱을 사용하도록 설정하여 특정 클라이언트에 대한 파일 권한 부여를 간소화합니다.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>클라우드 NAS 또는 클라우드 게이트웨이 최적화

클라우드 NAS 또는 게이트웨이 시나리오(vFXT 클러스터에서 NAS 스타일 액세스를 클라우드 컨테이너에 제공하는 경우)에서 vFXT 클러스터와 클라우드 저장소 간에 더 빠른 데이터 속도를 활용하려는 경우, 담당자는 설정을 다음과 같이 변경하여 데이터를 캐시에서 저장소 볼륨으로 더 적극적으로 푸시하도록 권장할 수 있습니다.

* 클러스터와 저장소 컨테이너 간의 TCP 연결 수를 늘립니다.
* 즉시 성공하지 않으면 쓰기를 더 빨리 다시 시도할 수 있도록 클러스터와 저장소 간의 통신에 대한 REST 시간 제한 값을 줄입니다.  
* 각 백 엔드 쓰기 세그먼트에서 1MB 대신 8MB의 데이터 청크를 전송할 수 있도록 세그먼트 크기를 늘립니다.

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>클라우드 버스팅 또는 하이브리드 WAN 최적화

vFXT 클러스터에서 클라우드와 온-프레미스 하드웨어 저장소 간의 통합을 제공하는 클라우드 버스팅 시나리오 또는 하이브리드 저장소 WAN 최적화 시나리오에서는 다음과 같은 변경이 유용할 수 있습니다.

* 클러스터와 코어 파일러 간에 허용되는 TCP 연결 수를 늘립니다.
* 원격 코어 파일러에 WAN 최적화 설정을 사용합니다(이 설정은 다른 Azure 지역의 원격 온-프레미스 파일러 또는 클라우드 코어 파일러에 사용할 수 있음).
* TCP 소켓 버퍼 크기를 늘립니다(작업 및 성능 요구 사항에 따라 다름)
* "항상 전달" 설정을 사용하도록 설정하여 중복적으로 캐시되는 파일을 줄입니다(작업 및 성능 요구 사항에 따라 다름)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Avere vFXT for Azure 최적화 도움말

[시스템 지원 받기](avere-vfxt-open-ticket.md)에서 설명한 절차를 사용하여 이러한 최적화에 대해 지원 담당자에게 문의하세요. 