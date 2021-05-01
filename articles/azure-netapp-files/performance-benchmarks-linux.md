---
title: Linux용 Azure NetApp Files 성능 벤치마크 | Microsoft Docs
description: Linux용으로 제공되는 Azure NetApp Files의 성능 벤치마크를 설명합니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "82614591"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Linux용 Azure NetApp Files 성능 벤치마크

이 문서에서는 Linux용으로 제공되는 Azure NetApp Files의 성능 벤치마크를 설명합니다.

## <a name="linux-scale-out"></a>Linux 스케일 아웃

이 섹션에서는 Linux 워크로드 처리량 및 워크로드 IOPS의 성능 벤치마크를 설명합니다.

### <a name="linux-workload-throughput"></a>Linux 워크로드 처리량  

아래 그래프는 64KiB(kibibyte)의 순차적 워크로드 및 1TiB 작업 집합을 나타냅니다. 그래프는 단일 Azure NetApp Files 볼륨이 1,600MiB/s 미만 순수 순차 쓰기와 4,500MiB/s 미만 순수 순차 읽기 사이에서 처리할 수 있음을 보여 줍니다.  

이 그래프는 순수 읽기에서 순수 쓰기로 10% 감소하는 것을 보여 줍니다. 또한 다양한 읽기/쓰기 비율(100%:0%, 90%:10%, 80%:20% 등)을 사용할 때 예측할 수 있는 항목을 보여 줍니다.

![Linux 워크로드 처리량](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux 워크로드 IOPS  

다음 그래프는 4KiB(kibibyte)의 임의 워크로드 및 1TiB 작업 집합을 나타냅니다. 이 그래프는 Azure NetApp Files 볼륨이 130,000 미만인 순수 임의 쓰기 및 460,000 미만인 순수 임의 읽기 사이에서 처리할 수 있음을 보여 줍니다.  

이 그래프는 순수 읽기에서 순수 쓰기로 10% 감소하는 것을 보여 줍니다. 또한 다양한 읽기/쓰기 비율(100%:0%, 90%:10%, 80%:20% 등)을 사용할 때 예측할 수 있는 항목을 보여 줍니다.

![Linux 워크로드 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux 확장  

Linux 5.3 커널을 통해 NFS-`nconnect`용 단일 클라이언트 스케일 아웃 네트워킹을 사용할 수 있습니다. 이 섹션의 그래프는 NFSv3을 사용한 클라이언트 측 탑재 옵션에 대한 유효성 검사 테스트 결과를 보여 줍니다. 이 기능은 SUSE(SLES12SP4부터) 및 Ubuntu(19.10 릴리스부터)에서 제공됩니다. SMB 다중 채널 및 Oracle Direct NFS와 유사한 개념입니다.

이 그래프는 연결되지 않은 탑재된 볼륨에 대한 `nconnect`의 장점을 비교합니다. 그래프의 FIO는 us-west2 Azure 지역의 단일 D32s_v3 인스턴스에서 워크로드를 생성했습니다.

### <a name="linux-read-throughput"></a>Linux 읽기 처리량  

다음 그래프는 `nconnect`를 사용하는 3,500MiB/s 미만의 순차 읽기(비`nconnect`의 약 2.3배)를 보여 줍니다.

![Linux 읽기 처리량](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux 쓰기 처리량  

다음 그래프에서는 순차 쓰기를 보여 줍니다. 그래프는 `nconnect`에 순차 쓰기에 대해 눈에 띄는 이점이 없음을 나타냅니다. 1,500MiB/s는 대략 순차 쓰기 볼륨 상한 및 D32s_v3 인스턴스 송신 한도입니다.

![Linux 쓰기 처리량](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux 읽기 IOPS  

다음 그래프는 `nconnect`를 사용하는 200,000 미만 읽기 IOPS의 임의 읽기(비`nconnect`의 약 3배)를 보여 줍니다.

![Linux 읽기 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux 쓰기 IOPS  

다음 그래프는 `nconnect`를 사용하는 135,000 미만 쓰기 IOPS의 임의 쓰기(`nconnect`의 약 3배)를 보여 줍니다.

![Linux 쓰기 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files: 클라우드 스토리지를 최대한 활용](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
