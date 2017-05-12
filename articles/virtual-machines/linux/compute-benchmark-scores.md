---
title: "Linux VM에 대한 벤치마크 점수 계산 | Microsoft Docs"
description: "Linux를 실행하는 Azure VM에 대한 CoreMark 계산 벤치마크 점수를 비교합니다."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 93e812c1-79dd-40c5-b97b-aa79f5cd7d76
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0d0bd736ddbdf1c4b4344747130236df2af76138
ms.contentlocale: ko-kr
ms.lasthandoff: 04/03/2017


---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Linux VM에 대한 벤치마크 점수 계산
다음 CoreMark 벤치마크 점수는 Ubuntu를 실행하는 Azure의 고성능 VM 라인업에 대한 계산 성능을 보여 줍니다. [Windows Vm](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 대해 계산 벤치마크 점수를 사용할 수도 있습니다.

## <a name="a-series---compute-intensive"></a>A 시리즈-계산 집약적
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |179 |110,294 |554 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |189 |210,816 |2,126 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |188 |110,025 |1,045 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |188 |210,727 |2,073 |

## <a name="dv2-series"></a>Dv2 시리즈
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |140 |14,852 |780 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |133 |29,467 |1,863 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |139 |56,205 |1,167 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |126 |108,543 |3,446 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |126 |205,332 |9,998 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |125 |28,598 |1,510 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |131 |55,673 |1,418 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |140 |107,986 |3,089 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |140 |208,186 |8,839 |
| Standard_D15_v2 |20 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |28 |268,560 |4,667 |

## <a name="f-series"></a>F 시리즈
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |154 |15,602 |787 |
| Standard_F2 |2 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |126 |29,519 |1,233 |
| Standard_F4 |4 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |147 |58,709 |1,227 |
| Standard_F8 |8 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |224 |112,772 |3,006 |
| Standard_F16 |16 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |42 |218,571 |5,113 |

## <a name="g-series"></a>G 시리즈
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |31,310 |2,891 |
| Standard_G2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |60,112 |3,537 |
| Standard_G3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |107,522 |4,537 |
| Standard_G4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |195,116 |5,024 |
| Standard_G5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |360,329 |14,212 |

## <a name="gs-series"></a>GS 시리즈
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |28,613 |1,884 |
| Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |54,348 |3,474 |
| Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |104,564 |1,834 |
| Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |194,111 |4,735 |
| Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |357,396 |16,228 |

## <a name="h-series"></a>H 시리즈
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |140,782 |2,512 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |35 |275,289 |7,110 |
| Standard_H18m |8 |1 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |139,071 |3,988 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |275,988 |6,963 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |273,982 |6,069 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |274,523 |5,698 |

## <a name="about-coremark"></a>CoreMark 정보
Linux 숫자는 Ubuntu에서 [CoreMark](http://www.eembc.org/coremark/faq.php) 를 실행하여 계산됩니다. CoreMark는 가상 CPU의 수에 설정된 스레드 수와 PThread에 설정된 동시성 수로 구성 되었습니다. 대상 반복 횟수는 20초 이상(일반적으로 훨씬 더 김)의 런타임을 제공하기 위해 예상되는 성능에 따라 조정되었습니다. 최종 점수는 완료된 반복 횟수를 테스트를 실행하는 데 걸린 시간(초)으로 나누어 나타냅니다. 각 테스트는 각 VM에서 적어도 7번 실행되었습니다. 테스트(H 시리즈 제외)는 2015년 10월에 실행 당일 VM이 지원되는 모든 Azure 공용 지역에서 여러 VM 상에서 실행되었습니다.

## <a name="next-steps"></a>다음 단계
* 저장 용량, 디스크 세부 정보 및 VM 크기 선택시 추가적인 고려 사항에 관한 자세한 내용은 [가상 컴퓨터의 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* Linux VM에서 CoreMark 스크립트를 실행하려면 [CoreMark 스크립트 팩](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)을 다운로드합니다.


