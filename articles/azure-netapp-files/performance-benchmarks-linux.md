---
title: Linux에 대 한 Azure NetApp Files 성능 벤치 마크 Microsoft Docs
description: Linux에서 제공 하 Azure NetApp Files 성능 벤치 마크에 대해 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614591"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Linux에 대 한 Azure NetApp Files 성능 벤치 마크

이 문서에서는 Linux 용으로 제공 되는 Azure NetApp Files 성능 벤치 마크에 대해 설명 합니다.

## <a name="linux-scale-out"></a>Linux 확장

이 섹션에서는 Linux 작업 처리량 및 워크 로드 IOPS의 성능 벤치 마크에 대해 설명 합니다.

### <a name="linux-workload-throughput"></a>Linux 작업 처리량  

아래 그래프는 KiB (64-kibibyte) 순차적 작업 및 1 TiB 작업 집합을 나타냅니다. 단일 Azure NetApp Files 볼륨이 1600 MiB/s 순수 순차 쓰기와 ~ 4500 MiB/s 순수 순차 읽기 간에 처리할 수 있음을 보여 줍니다.  

이 그래프는 순수 읽기에서 순수 쓰기로 10% 감소 하는 것을 보여 줍니다. 다양 한 읽기/쓰기 비율 (100%: 0%, 90%: 10%, 80%: 20% 등)을 사용할 때 예측할 수 있는 작업을 보여 줍니다.

![Linux 작업 처리량](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux 워크 로드 IOPS  

다음 그래프는 KiB (4-kibibyte) 임의 작업 및 1 TiB 작업 집합을 나타냅니다. 그래프는 Azure NetApp Files 볼륨이 ~ 13만 순수한 임의 쓰기와 ~ 46만 순수 무작위 읽기 사이에서 처리할 수 있음을 보여 줍니다.  

이 그래프는 순수 읽기에서 순수 쓰기로 10% 감소 하는 것을 보여 줍니다. 다양 한 읽기/쓰기 비율 (100%: 0%, 90%: 10%, 80%: 20% 등)을 사용할 때 예측할 수 있는 작업을 보여 줍니다.

![Linux 워크 로드 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux 확장  

Linux 5.3 커널은 NFS 용 단일 클라이언트 확장 네트워킹을`nconnect`사용 하도록 설정 합니다. 이 단원의 그래프는 NFSv3을 사용 하 여 클라이언트 쪽 탑재 옵션에 대 한 유효성 검사 테스트 결과를 보여줍니다. 이 기능은 SUSE (SLES12SP4부터 시작) 및 Ubuntu (19.10 릴리스부터)에서 사용할 수 있습니다. SMB 다중 채널과 Oracle Direct NFS를 모두 개념적으로 유사 합니다.

그래프는의 `nconnect` 장점과 연결 되지 않은 탑재 된 볼륨을 비교 합니다. 그래프에서 FIO는 미국 west2 Azure 지역의 단일 D32s_v3 인스턴스에서 작업을 생성 했습니다.

### <a name="linux-read-throughput"></a>Linux 읽기 처리량  

다음 그래프는 약 2.3 X가 아닌를 사용 하 여 `nconnect`~ 3500 MiB/s 읽기의 순차적`nconnect`읽기를 보여 줍니다.

![Linux 읽기 처리량](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux 쓰기 처리량  

다음 그래프에서는 순차적 쓰기를 보여 줍니다. 이는가 `nconnect` 순차적 쓰기에 대 한 눈에 띄는 이점을 갖지 않는다는 것을 의미 합니다. 1500 MiB/s는 거의 순차적 쓰기 볼륨 상한 및 D32s_v3 인스턴스 송신 한도입니다.

![Linux 쓰기 처리량](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux 읽기 IOPS  

다음 그래프는 ~ 20만 읽기 IOPS의 임의 읽기와 `nconnect`약 3, 0003을`nconnect`보여 줍니다.

![Linux 읽기 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux 쓰기 IOPS  

다음 그래프는 ~ 135000 write IOPS의 임의 쓰기 `nconnect`를 보여 줍니다 (약 3,`nconnect`0003).

![Linux 쓰기 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files: 클라우드 저장소를 최대한 활용](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
