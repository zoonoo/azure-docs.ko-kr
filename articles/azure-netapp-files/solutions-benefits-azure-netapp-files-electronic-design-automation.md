---
title: 전자 디자인 자동화를 위해 Azure NetApp Files를 사용할 경우의 이점 | Microsoft Docs
description: 반도체 및 칩 설계 업계의 요구 사항을 충족 하기 위해 제공 Azure NetApp Files 솔루션에 대해 설명 합니다. Azure NetApp Files를 사용 하 여 .EDA (electronic design automation) 용 표준 업계 벤치 마크를 실행 하는 테스트 시나리오를 제공 합니다.
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
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160156"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>전자 디자인 자동화에 Azure NetApp Files 사용의 이점

TTM (출시 시점)은 반도체 및 칩 설계 업계에 대 한 중요 한 고려 사항입니다. 업계의 대역폭이 높고 대기 시간이 짧은 저장소를 요구 합니다. 이 문서에서는 업계의 요구 사항을 충족 하기 위해 제공 하는 솔루션 Azure NetApp Files 설명 합니다. Azure NetApp Files를 사용 하 여 .EDA (electronic design automation) 용 표준 업계 벤치 마크를 실행 하는 테스트 시나리오를 제공 합니다. 

## <a name="test-scenario-configurations"></a>시나리오 구성 테스트

테스트에는 다음 구성의 세 가지 시나리오가 포함 됩니다. 

|    시나리오    |    볼륨    |    클라이언트<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    1         |    1          |    1                           |
|    2         |    6          |    24                          |
|    3       |    12         |    24                          |

첫 번째 시나리오는 단일 볼륨을 기반으로 할 수 있는 정도를 해결 합니다.  

두 번째 및 세 번째 시나리오는 단일 Azure NetApp Files 끝점의 제한을 평가 합니다. I/o 상한 및 대기 시간의 잠재적인 이점을 조사 합니다.

## <a name="test-scenario-results"></a>테스트 시나리오 결과

다음 표에서는 테스트 시나리오 결과를 요약 하 여 보여 줍니다.

|    시나리오       |    I/o 율<br>  2 밀리초     |    I/o 율<br>  가장자리에서     |    처리량<br>  2 밀리초     |    처리량<br>  가장자리에서     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    볼륨 1 개       |    39601                 |    49502                      |    692 MiB/s                 |    866 MiB/s                      |
|    6 개 볼륨      |    255613                |    317000                     |    4577 MiB/s               |    5568 MiB/s                    |
|    12 개 볼륨     |    256612                |    319196                     |    4577 MiB/s               |    5709 MiB/s                    |

단일 볼륨 시나리오는 기본 응용 프로그램 구성을 나타냅니다. 이는 후속 테스트 시나리오에 대 한 기준선 시나리오입니다.  

6 개 볼륨 시나리오는 선형 증가 (600%)를 보여 줍니다. 단일 볼륨 워크 로드를 기준으로 합니다.  단일 가상 네트워크 내의 모든 볼륨은 단일 IP 주소를 통해 액세스 됩니다.  

12 개 볼륨 시나리오에서는 6 개 볼륨 시나리오에 대 한 대기 시간을 줄이는 일반적인 방법을 보여 줍니다. 그러나 달성 가능한 처리량이 증가 하는 것은 아닙니다.   

다음 그래프는 Azure NetApp Files의 .EDA 워크 로드에 대 한 대기 시간 및 작업 요금을 보여 줍니다.  

![Azure NetApp Files의 .EDA 워크 로드에 대 한 대기 시간 및 작업 요금](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

다음 그래프는 Azure NetApp Files의 .EDA 워크 로드에 대 한 대기 시간 및 처리량을 보여 줍니다.  

![Azure NetApp Files의 .EDA 워크 로드에 대 한 대기 시간 및 처리량](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>테스트 시나리오의 레이아웃 

아래 표에는 테스트 시나리오의 레이아웃이 요약 되어 있습니다.

|    테스트 시나리오     |    총 디렉터리 수     |    총 파일 수     |
|----------------------|------------------------------------|------------------------------|
|    볼륨 1 개          |    88000                          |    88만                   |
|    6 개 볼륨         |    568000                         |    568만                 |
|    12 개 볼륨        |    568000                         |    568만                 |

전체 작업은 동시에 실행 되는 기능 및 물리적 단계를 혼합 한 것입니다. 이는 하나의 .EDA 도구 집합에서 다른 것으로의 일반적인 흐름을 나타냅니다.   

기능 단계는 초기 사양과 논리적 디자인으로 구성 됩니다. 물리적 단계는 논리적 디자인이 물리적 칩으로 변환 될 때 발생 합니다. 로그 오프 및 테이프 아웃 단계 중에 최종 검사가 완료 되 고 디자인이 제조를 위한 foundry로 전달 됩니다.  

기능 단계에는 순차 및 임의 읽기/쓰기 i/o의 혼합이 포함 됩니다. 기능 단계는 파일 상태 및 액세스 호출과 같이 메타 데이터 집약적입니다. 메타 데이터 작업은 크기 없이 효과적 이지만 읽기 및 쓰기 작업의 범위는 1 K에서 16k 사이입니다. 대부분의 읽기는 4 K에서 16kb 사이입니다.  대부분의 쓰기는 4 K이 하 여야 합니다. 물리적 단계는 32 K 및 64 K OP 크기를 혼합 하 여 순차적 읽기 및 쓰기 작업으로 구성 됩니다.  

위의 그래프에서 대부분의 처리량은 작업의 순차적 물리적 단계에서 제공 됩니다. I/o는 작은 무작위 및 메타 데이터 집약적 기능 단계에서 제공 됩니다. 두 단계가 병렬로 수행 됩니다. 

결론에는 Azure compute를 Azure NetApp Files 하 여 .EDA 디자인을 통해 확장 가능한 대역폭을 얻을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files를 사용하는 솔루션 아키텍처](azure-netapp-files-solution-architectures.md)
