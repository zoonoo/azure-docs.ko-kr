<properties
 pageTitle="Linux VM에 대한 벤치마크 점수 계산 | Microsoft Azure"
 description="Linux를 실행하는 Azure VM에 대한 CoreMark 계산 벤치마크 점수를 비교합니다."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Linux VM에 대한 벤치마크 점수 계산

다음 CoreMark 벤치마크 점수는 Ubuntu를 실행하는 Azure의 고성능 VM 라인업에 대한 계산 성능을 보여 줍니다. [Windows Vm](virtual-machines-windows-compute-benchmark-scores.md)에 대해 계산 벤치마크 점수를 사용할 수도 있습니다.




## A 시리즈-계산 집약적


크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 179 | 110,294 | 554
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 189 | 210,816| 2,126
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 188 | 110,025 | 1,045
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 188 | 210,727| 2,073

## Dv2 시리즈


크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 14,852 | 780
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 133 | 29,467 | 1,863
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 139 | 56,205 | 1,167
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 108,543 | 3,446
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 205,332 | 9,998
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 125 | 28,598 | 1,510
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 131 | 55,673 | 1,418
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 107,986 | 3,089
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 208,186 | 8,839
Standard\_D15\_v2 | 20 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz |28 | 268,560 | 4,667

## F 시리즈

크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_F1 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 154 | 15,602 | 787
Standard\_F2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 29,519 | 1,233
Standard\_F4 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 147 | 58,709 | 1,227
Standard\_F8 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 224 | 112,772 | 3,006
Standard\_F16 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 42 | 218,571 | 5,113



## G 시리즈


크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 31,310 | 2,891
Standard\_G2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 60,112 | 3,537
Standard\_G3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 107,522 | 4,537
Standard\_G4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 195,116 | 5,024
Standard\_G5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 360,329 | 14,212

## GS 시리즈


크기 | vCPU | NUMA 노드 | CPU | 실행 | 반복 수/초 | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 28,613 | 1,884
Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 54,348 | 3,474
Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 104,564 | 1,834
Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 194,111 | 4,735
Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 357,396 | 16,228


## CoreMark 정보

Linux 숫자는 Ubuntu에서 [CoreMark](http://www.eembc.org/coremark/faq.php)를 실행하여 계산됩니다. CoreMark는 가상 CPU의 수에 설정된 스레드 수와 PThread에 설정된 동시성 수로 구성 되었습니다. 반복 횟수 목표는 최소 20초(일반적으로 훨씬 더 김) 의 런타임을 제공하도록 예상되는 성능을 기준으로 조정되었습니다. 이 때 최종 점수는 완료된 반복 횟수를 테스트를 실행하는 데 걸린 시간(초)으로 나눈 값을 나타냅니다. 각 테스트는 각 VM에서 적어도 7번 실행되었습니다. 테스트는 2015년 10월에 실행 당일 VM이 지원되는 모든 Azure 공용 지역에서 여러 VM 상에서 실행되었습니다.
## 다음 단계



* 저장 용량, 디스크 세부 정보 및 VM 크기 선택시 추가적인 고려 사항에 관한 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-linux-sizes.md)를 참조합니다.

* Linux VM에서 CoreMark 스크립트를 실행 하려면 [CoreMark 스크립트 팩](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)을 다운로드합니다.

<!---HONumber=AcomDC_0727_2016-->