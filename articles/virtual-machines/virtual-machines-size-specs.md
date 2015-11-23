<properties
 pageTitle="가상 컴퓨터 크기 | Microsoft Azure"
 description="가상 컴퓨터 및 해당 용량에 대한 다양한 크기를 나열합니다."
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="09/02/2015"
 ms.author="cynthn"/>

# 가상 컴퓨터의 크기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 개요

이 문서에서는 앱 및 작업을 실행하는데 사용할 수는 가상 컴퓨터 기반 계산 리소스에 대한 옵션을 설명합니다. 또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다.또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다.

Azure 가상 컴퓨터 및 클라우드 서비스는 Azure에서 제공하는 여러 유형의 계산 리소스 중 두 가지입니다. 설명은 [Azure에서 제공하는 계산 호스팅 옵션](http://go.microsoft.com/fwlink/p/?LinkID=311926)을 참조하세요.

>[AZURE.NOTE]관련 Azure 제한 사항에 대한 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

## 고려 사항 계획

가상 컴퓨터는 기본 및 표준 – 두 계층에서 사용할 수 있습니다. 두 유형 모두 선택한 크기를 제공하지만 기본 계층은 부하 분산 및 자동 크기 조정 같은 표준 계층에서는 사용할 수 있는 일부 기능을 제공하지 않습니다. 다른 크기: A, D, DS, G 및 GS로 구성된 표준 계층 크기입니다. 이러한 크기 중 일부에 대한 고려 사항은 다음을 포함합니다.

*   D 시리즈 VM은 높은 계산 능력과 임시 디스크 성능이 필요한 응용 프로그램을 실행하도록 설계되었습니다. D 시리즈 VM은 임시 디스크를 위해 빠른 프로세서, 더 높은 메모리-코어 비율 및 SSD(반도체 드라이브)를 제공합니다. 자세한 내용은 Azure 블로그에 발표된 [새로운 D 시리즈 가상 컴퓨터 크기](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)를 참조하세요.

*   원래 D 시리즈의 후속판인 Dv2 시리즈는 더 강력한 CPU가 특징입니다. Dv2 시리즈 CPU는 D 시리즈 CPU보다 약 35% 빠릅니다. 최근 출시된 2.4GHz Intel Xeon® E5-2673 v3(Haswell) 프로세서에 기반하고 Intel Turbo Boost Technology 2.0을 사용하여 최대 3.2GHz까지 올라갈 수 있습니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

    Dv2 시리즈 지역별 가용성은 다음 일정을 따릅니다. 10월 15일: 미국 동부 2, 미국 중부, 미국 중북부, 미국 서부. 11월 15일: 미국 동부, 북유럽, 서유럽. 1월 16일: 미국 중남부, APAC 동부, APAC 남동부, 일본 동부, 일본 서부, 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부.


*   G 시리즈 VM은 가장 큰 크기와 최상의 성능을 제공하고 Intel Xeon E5 V3 제품군 프로세서가 설치된 호스트에서 실행합니다.

*   DS 시리즈 및 GS 시리즈 VM은 I/O가 많은 작업에 대한 고성능, 지연율이 낮은 저장소를 제공하는 프리미엄 저장소를 사용할 수 있습니다. 이러한 VM은 SSD(반도체 드라이브)를 사용하여 가상 컴퓨터의 디스크를 호스트하고 로컬 SSD 디스크 캐시를 제공합니다. 프리미엄 저장소는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](../storage-premium-storage-preview-portal.md)를 참조하세요.

가상 컴퓨터의 크기는 가격 산정에 영향을 줍니다. 크기는 가상 컴퓨터의 처리, 메모리 및 저장소 용량에 영향을 줍니다. 저장소 비용은 저장소 계정에 사용된 페이지에 따라 개별적으로 계산됩니다. 자세한 내용은 [가상 컴퓨터 가격 세부 정보](http://azure.microsoft.com/pricing/details/virtual-machines/) 및 [Azure 저장소 가격](http://azure.microsoft.com/pricing/details/storage/)을 참조하세요. VM용 저장소에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](virtual-machines-disks-vhds.md)를 참조하세요.

다음 고려 사항이 크기를 결정하는 데 도움이 될 수 있습니다.

*   A0\\Basic\_A0 크기는 Azure SDK 버전 1.3 이상을 사용하여만 사용할 수 있습니다.  

*   A1\\Basic\_A1는 프로덕션 작업용으로 권장되는 최소 크기입니다.

*   SQL Server Enterprise Edition을 사용하는 경우 4 또는 8 CPU 코어가 있는 가상 컴퓨터를 선택합니다.

*   Azure 데이터 센터의 일부 물리적 호스트는 A5 – A11과 같은 큰 크기의 가상 컴퓨터를 지원하지 않을 수 있습니다. 결과적으로, 기존 가상 컴퓨터의 크기를 새 크기로 조정, 2013년 4월 16일 이전에 만든 가상 네트워크에서 새 가상 컴퓨터 만들기 또는 새 가상 컴퓨터를 기존 클라우드 서비스에 추가할 때 **가상 컴퓨터를 구성하지 못했습니다<machine name>** 또는 **가상 컴퓨터를 만들지 못했습니다<machine name>**라는 오류 메시지가 표시될 수 있습니다. 각 배포 시나리오의 해결 방법에 대한 지원 포럼에서 [오류: "가상 컴퓨터를 구성하지 못했습니다."](https://social.msdn.microsoft.com/Forums/ko-KR/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) 항목을 참조하세요.

*   A8/A10 및 A9/A11 가상 컴퓨터 크기는 용량이 동일합니다. A8 및 A9 가상 컴퓨터 인스턴스에는 가상 컴퓨터 간의 빠른 통신을 위해 RDMA(원격 직접 메모리 액세스) 네트워크에 연결되는 추가 네트워크 어댑터가 포함됩니다. A8 및 A9 인스턴스는 MPI(메시지 전달 인터페이스)를 사용하는 응용 프로그램 등을 실행하는 동안 노드 간에 지속적이고 대기 시간이 짧은 통신이 필요한 고성능 컴퓨팅 응용 프로그램을 위해 설계되었습니다. A10 및 A11 가상 컴퓨터 인스턴스에는 추가 네트워크 어댑터가 포함되지 않습니다. A10 및 A11 인스턴스는 노드 간에 지속적이고 대기 시간이 짧은 통신을 필요로 하지 않는 고성능 컴퓨팅 응용 프로그램을 위해 설계되었으며 파라메트릭 또는 병렬 응용 프로그램으로 알려져 있습니다.

*   Dv2 시리즈 및 D 시리즈는 더 빠른 CPU와 더 좋은 로컬 디스크 성능을 요구하거나 더 높은 메모리 요구량을 가진 응용 프로그램에 이상적입니다. 이들은 많은 엔터프라이즈급 응용 프로그램을 위한 강력한 조합을 제공합니다.



## 일반 제한

이 표에서는 서비스 관리 배포 모델을 사용하여 만든 가상 컴퓨터에 대한 가상 컴퓨터의 크기에 관계없이 적용되는 제한을 보여 줍니다.

[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

이 표에서는 리소스 관리자 배포 모델을 사용하여 만든 가상 컴퓨터에 대한 가상 컴퓨터의 크기에 관계없이 적용되는 제한을 보여 줍니다.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## 테이블 크기 조정

다음 표에서 제공된 크기와 용량을 표시합니다.

>[AZURE.NOTE]저장소 용량은 1024^3 바이트를 사용하여 GB의 측정 단위로 표시됩니다. gibibyte, 또는 기본 2 정의라고도 합니다. 다른 기본 시스템을 사용하는 크기와 비교할 때, 기본 2 크기가 기본 10보다 작게 나타날 수 있지만 특정 크기(예: 1GB)의 경우 1024 ^3이 1000^3 보다 크기 때문에 기본 2 시스템은 기본 10 시스템보다 더 많은 용량을 제공합니다.

## 기본 계층

|크기 – Azure 포털\\cmdlet 및 API|CPU 코어|메모리|NIC(최대)|최대 디스크 크기 – 가상 컴퓨터|최대 데이터 디스크는 가 1023GB임)|최대 IOPS(디스크당 300)|
|---|---|---|---|---|---|---|
|A0\\Basic\_A0|1|768 MB|1|<p>OS = 1023GB</p><p>임시 = 20GB</p>|1|1x300|
|A1\\Basic\_A1|1|1\.75 GB|1|<p>OS = 1023GB</p><p>임시 = 40GB</p>|2|2x300|
|A2\\Basic\_A2|2|3\.5 GB|1|<p>OS = 1023GB</p><p>임시 = 60GB</p>|4|4x300|
|A3\\Basic\_A3|4|7 GB|1|<p>OS = 1023GB</p><p>임시 = 120GB</p>|8|8x300|
|A4\\Basic\_A4|8|14 GB|1|<p>OS = 1023GB</p><p>임시 = 240GB</p>|16|16x300|

## 표준 계층: A 시리즈

|크기 – Azure 포털\\cmdlet 및 API|CPU 코어|메모리|NIC(최대)|최대 디스크 크기 – 가상 컴퓨터|최대 데이터 디스크(각 1023GB)|최대 IOPS(디스크당 500)|
|---|---|---|---|---|---|---|
|A0\\ExtraSmall|1|768 MB|1|<p>OS = 1023GB</p><p>임시 = 20GB</p>|1|1x500|
|A1\\작음|1|1\.75 GB|1|<p>OS = 1023GB</p><p>임시 = 70GB</p>|2|2x500|
|A2\\중간|2|3\.5 GB|1|<p>OS = 1023GB</p><p>임시 = 135GB</p>|4|4x500|
|A3\\큼|4|7 GB|2|<p>OS = 1023GB</p><p>임시 = 285GB</p>|8|8x500|
|A4\\매우 큼|8|14 GB|4|<p>OS = 1023GB</p><p>임시 = 605GB</p>|16|16x500|
|A5\\동일|2|14 GB|1|<p>OS = 1023GB</p><p>임시 = 135GB</p>|4|4X500|
|A6\\동일|4|28GB|2|<p>OS = 1023GB</p><p>임시 = 285GB</p>|8|8x500|
|A7\\동일|8|56GB|4|<p>OS = 1023GB</p><p>임시 = 605GB</p>|16|16x500|
|A8\\동일|8|56GB|2|<p><p>OS = 1023GB</p><p>Temporary = 382GB</p><blockquote><p>참고: 이 크기의 사용과 관련된 자세한 내용 및 고려 사항에 대해서는 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적 인스턴스 정보</a>를 참조하세요.</p></blockquote>|16|16x500|
|A9\\동일|16|112GB|4|<p><p>OS = 1023GB</p><p>Temporary = 382GB</p><blockquote><p>참고: 이 크기의 사용과 관련된 자세한 내용 및 고려 사항에 대해서는 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적 인스턴스 정보</a>를 참조하세요.</p></blockquote>|16|16x500|
|A10\\동일|8|56GB|2|<p><p>OS = 1023GB</p><p>Temporary = 382GB</p><blockquote><p>참고: 이 크기의 사용과 관련된 자세한 내용 및 고려 사항에 대해서는 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적 인스턴스 정보</a>를 참조하세요.</p></blockquote>|16|16x500|
|A11\\동일|16|112GB|4|<p><p>OS = 1023GB</p><p>Temporary = 382GB</p><blockquote><p>참고: 이 크기의 사용과 관련된 자세한 내용 및 고려 사항에 대해서는 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적 인스턴스 정보</a>를 참조하세요.</p></blockquote>|16|16x500|

## 표준 계층: D 시리즈

|크기 – Azure 포털\\cmdlet 및 API|CPU 코어|메모리|NIC(최대)|최대 디스크 크기 – 가상 컴퓨터|최대 데이터 디스크(각 1023GB)|최대 IOPS(디스크당 500)|
|---|---|---|---|---|---|---|
|Standard\_D1\\동일|1|3\.5 GB|1|<p>OS = 1023GB</p><p>임시 (SSD) =50GB</p>|2|2x500|
|Standard\_D2\\동일|2|7 GB|2|<p>OS = 1023GB</p><p>임시 (SSD) = 100GB</p>|4|4x500|
|Standard\_D3\\동일|4|14 GB|4|<p>OS = 1023GB</p><p>임시 (SSD) = 200GB</p>|8|8x500|
|Standard\_D4\\동일|8|28GB|8|<p>OS = 1023GB</p><p>임시 (SSD) = 400GB</p>|16|16x500|
|Standard\_D11\\동일|2|14 GB|2|<p>OS = 1023GB</p><p>임시 (SSD) = 100GB</p>|4|4x500|
|Standard\_D12\\동일|4|28GB|4|<p>OS = 1023GB</p><p>임시 (SSD) = 200GB</p>|8|8x500|
|Standard\_D13\\동일|8|56GB|8|<p>OS = 1023GB</p><p>임시 (SSD) = 400GB</p>|16|16x500|
|Standard\_D14\\동일|16|112GB|16|<p>OS = 1023GB</p><p>임시 (SSD) =800GB</p>|32|32x500|

## 표준 계층: Dv2 시리즈

|크기 – Azure 포털\\cmdlet 및 API|CPU 코어|메모리|NIC(최대)|최대 디스크 크기 – 가상 컴퓨터|최대 데이터 디스크(각 1023GB)|최대 IOPS(디스크당 500)|
|---|---|---|---|---|---|---|
|Standard\_D1\_v2\\same|1|3\.5 GB|1|<p>OS = 1023GB</p><p>임시 (SSD) =50GB</p>|2|2x500|
|Standard\_D2\_v2\\same|2|7 GB|2|<p>OS = 1023GB</p><p>임시 (SSD) = 100GB</p>|4|4x500|
|Standard\_D3\_v2\\same|4|14 GB|4|<p>OS = 1023GB</p><p>임시 (SSD) = 200GB</p>|8|8x500|
|Standard\_D4\_v2\\same|8|28GB|8|<p>OS = 1023GB</p><p>임시 (SSD) = 400GB</p>|16|16x500|
|Standard\_D5\_v2\\same|16|56GB|16|<p>OS = 1023GB</p><p>임시 (SSD) =800GB</p>|32|32x500|
|Standard\_D11\_v2\\same|2|14 GB|2|<p>OS = 1023GB</p><p>임시 (SSD) = 100GB</p>|4|4x500|
|Standard\_D12\_v2\\same|4|28GB|4|<p>OS = 1023GB</p><p>임시 (SSD) = 200GB</p>|8|8x500|
|Standard\_D13\_v2\\same|8|56GB|8|<p>OS = 1023GB</p><p>임시 (SSD) = 400GB</p>|16|16x500|
|Standard\_D14\_v2\\same|16|112GB|16|<p>OS = 1023GB</p><p>임시 (SSD) =800GB</p>|32|32x500|

## 표준 계층: DS 시리즈*

|크기 – Azure 포털\\cmdlet 및 API|CPU 코어|메모리|NIC(최대)|최대 디스크 크기 – 가상 컴퓨터|최대 데이터 디스크(각 1023GB)|캐시 크기(GB)|최대 디스크 IOPS & amp; 대역폭|
|---|---|---|---|---|---|---|---|
|Standard\_DS1\\동일|1|3\.5|1|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 7GB</p>|2|43|<p>3,200</p><p>초당 32MB</p>|
|Standard\_DS2\\동일|2|7|2|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 14GB</p>|4|86|<p>6,400</p><p>초당 64MB</p>|
|Standard\_DS3\\동일|4|14|4|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 28GB</p>|8|172|<p>12,800</p><p>초당 128MB</p>|
|Standard\_DS4\\동일|8|28|8|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 56GB</p>|16|344|<p>25,600</p><p>초당 256MB</p>|
|Standard\_DS11\\동일|2|14|2|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 28GB</p>|4|72|<p>6,400</p><p>초당 64MB</p>|
|Standard\_DS12\\동일|4|28|4|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 56GB</p>|8|144|<p>12,800</p><p>초당 128MB</p>|
|Standard\_DS13\\동일|8|56|8|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 112GB</p>|16|288|<p>25,600</p><p>초당 256MB</p>|
|Standard\_DS14\\동일|16|112|16|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 224GB</p>|32|576|<p>50,000</p><p>초당 512MB</p>|

**DS 시리즈 VM에서 가능한 최당 최대 입/출력 작업(IOPS) 및 처리량(대역폭) VM은 디스크의 크기에 영향을 받습니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](../storage-premium-storage-preview-portal.md)를 참조하세요.

## 표준 계층: G 시리즈

|크기 – Azure 포털\\cmdlet &amp; API|CPU 코어|메모리|NIC(최대)|최대 디스크 크기 – 가상 컴퓨터|최대 데이터 디스크(각 1023GB)|최대 IOPS(디스크당 500)|
|---|---|---|---|---|---|---|
|Standard\_G1\\동일|2|28GB|1|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 384GB</p>|4|4 x 500|
|Standard\_G2\\동일|4|56GB|2|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 768GB</p>|8|8 x 500|
|Standard\_G3\\동일|8|112GB|4|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 1,536GB</p>|16|16 x 500|
|Standard\_G4\\동일|16|224GB|8|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 3,072GB</p>|32|32 x 500|
|Standard\_G5\\동일|32|448GB|16|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 6,144GB</p>|64|<p>64 x 500</p>|

## 표준 계층: GS 시리즈

|크기 – Azure 포털\\cmdlet 및 API|CPU 코어|메모리|NIC(최대)|최대 디스크 크기 – 가상 컴퓨터|최대 데이터 디스크(각 1023GB)|캐시 크기(GB)|최대 디스크 IOPS & amp; 대역폭|
|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 56GB</p>|4|264|<p>5,000</p><p>초당 125MB</p>|
|Standard\_GS2|4|56|2|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 112GB</p>|8|528|<p>10,000</p><p>초당 250MB</p>|
|Standard\_GS3|8|112|4|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 224GB</p>|16|1056|<p>20,000</p><p>초당 500MB</p>|
|Standard\_GS4|16|224|8|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 448GB</p>|32|2112|<p>40,000</p><p>초당 1,000MB</p>|
|Standard\_GS5|32|448|16|<p>OS = 1023GB</p><p>로컬 SSD 디스크 = 896GB</p>|64|4224|<p>80,000</p><p>초당 2,000MB</p>|


### 참고 항목

[Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)

[A8, A9, A10 및 A11 계산 집약적인 인스턴스 정보](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=Nov15_HO3-->