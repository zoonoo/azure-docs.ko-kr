<properties
 pageTitle="Windows VM에 대한 벤치마크 점수 계산 | Microsoft Azure"
 description="Windows Server를 실행하는 Azure VM에 대한 SPECint 계산 벤치마크 점수를 비교합니다."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Windows VM에 대한 벤치마크 점수 계산

다음 SPECInt 벤치마크 점수는 Windows Server를 실행하는 Azure의 고성능 VM 라인업에 대한 계산 성능을 보여 줍니다. [Linux VM](virtual-machines-linux-compute-benchmark-scores.md)에 대해 계산 벤치마크 점수를 사용할 수도 있습니다.



## A 시리즈-계산 집약적


크기 | vCPU | NUMA 노드 | CPU | 실행 | Avg 기본 요율 | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 10 | 236\.1 | 1\.1
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 10 | 450\.3 | 7\.0
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 5 | 235\.6 | 0\.9
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz |7 | 454\.7 | 4\.8

## Dv2 시리즈


크기 | vCPU | NUMA 노드 | CPU | 실행 | Avg 기본 요율 | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 83 | 36\.6 | 2\.6
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 27 | 70\.0 | 3\.7
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 130\.5 | 4\.4.
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 238\.1 | 5\.2
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 14 | 460\.9 | 15\.4
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 70\.1 | 3\.7
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 2 | 132\.0 | 1\.4
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 17 | 235\.8 | 3\.8
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 15 | 460\.8 | 6\.5


## G-시리즈, GS 시리즈


크기 | vCPU | NUMA 노드 | CPU | 실행 | Avg 기본 요율 | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1, Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 31 | 71\.8 | 6\.5
Standard\_G2, Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 5 | 133\.4 | 13\.0
Standard\_G3, Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 6 | 242\.3 | 6\.0
Standard\_G4, Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 15 | 398\.9 | 6\.0
Standard\_G5, Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 22 | 762\.8 | 3\.7

## SPECint 정보

Windows 번호는 Windows Server에서 [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html)을 실행하여 계산합니다. SPECint는 코어당 하나의 복사본과 함께 기본 요율 옵션(SPECint\_rate2006)을 사용하여 실행합니다. SPECint은 12번의 별도 테스트로 각기 세 번 실행되도록 구성되며, 각 테스트에서 중간값을 취해 가중치를 주어 종합 점수를 형성합니다. 그런 다음 여러 VM에서 실행하여 평균값을 구합니다.


## 다음 단계

* 저장 용량, 디스크 세부 정보 및 VM 크기 선택시 추가적인 고려 사항에 관한 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-windows-sizes.md)를 참조합니다.

<!---HONumber=AcomDC_0727_2016-->