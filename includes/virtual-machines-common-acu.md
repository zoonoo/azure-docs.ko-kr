



Azure 계산 단위(ACU)의 개념을 만들어 Azure SKU를 통한 계산(CPU) 성능 비교 방법을 제공합니다. 어떤 SKU가 성능 요구 사항을 충족할 가능성이 높은지 쉽게 식별하도록 합니다.  ACU는 현재 100인 작은(Standard_A1) VM에서 표준화되고 다른 SKU는 모두 SKU가 표준 벤치 마크를 얼마나 빨리 실행할 수 있는지를 나타냅니다. 

> [!IMPORTANT]
> ACU는 단지 지침일 뿐입니다.  워크로드에 대한 결과가 달라질 수 있습니다. 
> 
> 

<br>

| SKU 제품군 | ACU/코어 |
| --- | --- |
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 |
| [A1-A4](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A5-A7](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A1_v2-A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A2m_v2-A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A8-A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* |
| [D1-D14](../articles/virtual-machines/windows/sizes-general.md) |160 |
| [D1_v2-D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* |
| [DS1-DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 |
| [DS1_v2-DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* |
| [F1-F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* |
| [F1s-F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* |
| [G1-G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* |
| [GS1-GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 - 300* |
| [L4s-L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* |

*로 표시된 ACU는 Intel® 터보 기술을 사용하여 CPU 주파수를 증가시키고 성능 향상을 제공합니다.  상승량은 VM 크기, 워크로드 및 동일한 호스트에서 실행 중인 다른 작업에 따라 달라질 수 있습니다.
