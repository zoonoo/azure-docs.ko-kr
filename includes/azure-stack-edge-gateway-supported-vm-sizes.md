---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/27/2021
ms.author: alkohli
ms.openlocfilehash: f92dc476d3bc79fdb522424e36e44a13644c725e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078804"
---
VM 크기에 따라 CPU, GPU, 메모리 등 VM에 사용할 수 있는 컴퓨팅 리소스의 양이 결정됩니다. 워크로드에 적합한 VM 크기를 사용하여 가상 머신을 만들어야 합니다. 모든 가상 머신이 동일한 하드웨어에서 실행되는 경우에도 디스크에 액세스할 때는 컴퓨터 크기에 다른 제한이 적용됩니다. 이렇게 하면 VM 전체에서 디스크 액세스를 관리할 수 있습니다. 워크로드가 증가할 경우 기존 가상 머신의 크기를 조정할 수도 있습니다.

다음 VM은 Azure Stack Edge 디바이스에서 만들기가 지원됩니다.

### <a name="dv2-series"></a>Dv2 시리즈
|크기     |vCPU     |메모리(GiB) | 임시 스토리지(GiB)   | 최대 데이터 디스크 수 | 최대 NIC 수 |
|-------------------|----|----|-----|----|------|
|**Standard_D1_v2** |1   |3.5 |50   | 4    |2 |
|**Standard_D2_v2** |2   |7   |100  | 8    |4 |
|**Standard_D3_v2** |4   |14  |200  | 16  |4 |
|**Standard_D4_v2** |8   |28  |400  | 32  |8 |
|**Standard_D5_v2** |16  |56  |800  | 64  |8 |
|**Standard_D11_v2** |2   |14  |100 | 8     |2 |
|**Standard_D12_v2** |4   |28  |200  | 16   |4 |
|**Standard_D13_v2** |8   |56  |400  | 32  |8 |

### <a name="dsv2-series"></a>DSv2 시리즈
|크기     |vCPU     |메모리(GiB) | 임시 스토리지(GiB)  | 최대 데이터 디스크 수| 
|--------------------|----|----|----|-----|
|**Standard_DS1_v2** |1   |3.5 |4000  |4  | 
|**Standard_DS2_v2** |2   |7   |8000  |8  | 
|**Standard_DS3_v2** |4   |14  |16000 |16 | 
|**Standard_DS4_v2** |8   |28  |32000 |32 | 
|**Standard_DS5_v2** |16  |56  |64000 |64 |  
|**Standard_DS11_v2**|2   |14  |8000  |4  | 
|**Standard_DS12_v2**|4   |28  |16000 |8  | 
|**Standard_DS13_v2**|8   |56  |32000 |16 | 


자세한 내용은 [Dv2 및 DSv2 시리즈](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series)를 참조하세요.

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3 시리즈(미리 보기)

이러한 크기는 디바이스의 GPU VM에 대해 지원되며 컴퓨팅 집약적 GPU 가속 애플리케이션에 최적화되어 있습니다. 이 시리즈는 Nvidia의 Tesla T4 GPU를 사용하는 추론 워크로드에 초점을 맞추고 있습니다. 

|크기     |vCPU     |메모리(GiB) | 임시 스토리지(GiB)  | GPU | GPU 메모리(GiB) | 최대 NIC 수 |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1 |16  |8 |

자세한 내용은 [NCasT4_v3 시리즈](../articles/virtual-machines/nct4-v3-series.md)를 참조하세요.

### <a name="f-series"></a>F 시리즈

이러한 시리즈는 계산 워크로드에 최적화되고 Intel Xeon 프로세서에서 실행됩니다. 

| 크기 | vCPU 수 | 메모리: GiB | 임시 스토리지(GiB) |  최대 데이터 디스크 수 | 최대 NIC 수 |
|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      | 4  |  2 |
| Standard_F2 | 2  | 4 |32      | 8  |  4 |
| Standard_F4  | 4  | 8 |64   | 16 |  4 |
| Standard_F8 | 8 | 16  |132    | 32 |  8 |
| Standard_F16 | 16 | 32  |262   | 64 |  8 |
| Standard_F1s | 1 | 2  | 4  | 4 | 1 |
| Standard_F2s | 2 | 4 |8   | 8 | 4 |
| Standard_F4s | 4 | 8 |16 | 16 |  4 |
| Standard_F8s | 8 | 16 |32 | 32 |  8 |
| Standard_F16s | 16 | 32 |64 | 64 |  8 |

자세한 내용은 [Fsv2 시리즈](../articles/virtual-machines/fsv2-series.md)를 참조하세요.

