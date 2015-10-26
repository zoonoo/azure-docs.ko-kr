<properties
 pageTitle="클라우드 서비스에 적합한 크기"
 description="Azure 클라우드 서비스 웹 및 작업자 역할에 대한 다양한 크기를 나열합니다."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/14/2015"
 ms.author="adegeo"/>

# 클라우드 서비스에 적합한 크기

이 항목에서는 클라우드 서비스 역할 인스턴스(웹 역할 및 작업자 역할)에서 사용 가능한 크기 및 옵션을 설명합니다. 또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다.

Azure 가상 컴퓨터 및 클라우드 서비스는 Azure에서 제공하는 여러 유형의 계산 리소스 중 두 가지입니다. 설명은 [Azure에서 제공하는 계산 호스팅 옵션](fundamentals-application-models.md)을 참조하세요.

> [AZURE.NOTE]관련 Azure 제한 사항에 대한 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

## 웹 및 작업자 역할 인스턴스에 적합한 크기

다음 고려 사항이 크기를 결정하는 데 도움이 될 수 있습니다.

* D 시리즈 VM 인스턴스는 높은 계산 능력과 임시 디스크 성능이 필요한 응용 프로그램을 실행하도록 설계되었습니다. D 시리즈 VM은 임시 디스크를 위해 빠른 프로세서, 더 높은 메모리-코어 비율 및 SSD(반도체 드라이브)를 제공합니다. 자세한 내용은 Azure 블로그에 발표된 [새로운 D 시리즈 가상 컴퓨터 크기](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)를 참조하세요.  

*   원래 D 시리즈의 후속판인 Dv2 시리즈는 더 강력한 CPU가 특징입니다. Dv2 시리즈 CPU는 D 시리즈 CPU보다 약 35% 빠릅니다. 최근 출시된 2.4GHz Intel Xeon® E5-2673 v3(Haswell) 프로세서에 기반하고 Intel Turbo Boost Technology 2.0을 사용하여 최대 3.2GHz까지 올라갈 수 있습니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

    Dv2 시리즈 지역별 가용성은 다음 일정을 따릅니다. 10월 15일: 미국 동부 2, 미국 중부, 미국 중북부, 미국 서부. 11월 15일: 미국 동부, 북유럽, 서유럽. 1월 16일: 미국 중남부, APAC 동부, APAC 남동부, 일본 동부, 일본 서부, 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부.

* 시스템 요구 사항으로 인해 웹 역할 및 작업자 역할에는 Azure 가상 컴퓨터 보다 많은 임시 디스크 공간이 필요합니다. 시스템 파일은 Windows 페이지 파일용으로 4GB, Windows 덤프 파일용으로 2GB의 공간이 예약됩니다.

* OS 디스크에는 Windows 게스트 OS가 포함되어 있으며 Program Files 폴더(다른 디스크를 지정하지 않는 경우 시작 작업을 통해 수행되는 설치 포함), 레지스트리 변경 사항, System32 폴더 및 .NET framework가 들어 있습니다.

* 로컬 리소스 디스크에는 Azure 로그 및 구성 파일, Azure 진단(IIS 로그 포함) 및 사용자가 정의한 로컬 저장소 리소스가 포함되어 있습니다.

* 앱(응용 프로그램) 디스크는 사용자의 .cspkg가 추출되는 위치로, 웹 사이트, 이진 파일, 역할 호스트 프로세스, 시작 작업, web.config 등이 포함됩니다.

* A8/A10 및 A9/A11 가상 컴퓨터 크기는 용량이 동일합니다. A8 및 A9 가상 컴퓨터 인스턴스에는 가상 컴퓨터 간의 빠른 통신을 위해 RDMA(원격 직접 메모리 액세스) 네트워크에 연결되는 추가 네트워크 어댑터가 포함됩니다. A8 및 A9 인스턴스는 MPI(메시지 전달 인터페이스)를 사용하는 응용 프로그램 등을 실행하는 동안 노드 간에 지속적이고 대기 시간이 짧은 통신이 필요한 고성능 컴퓨팅 응용 프로그램을 위해 설계되었습니다. A10 및 A11 가상 컴퓨터 인스턴스에는 추가 네트워크 어댑터가 포함되지 않습니다. A10 및 A11 인스턴스는 노드 간에 지속적이고 대기 시간이 짧은 통신을 필요로 하지 않는 고성능 컴퓨팅 응용 프로그램을 위해 설계되었으며 파라메트릭 또는 병렬 응용 프로그램으로 알려져 있습니다.

|크기|CPU<br>코어 수|메모리|디스크 크기|
|---|---|---|---|
|매우 작음|1|768 MB|OS = 게스트 OS 크기<br/>로컬 리소스 = 15384MB<br/>앱 = 약 1.5GB|
|작음|1|1\.75 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 225304MB<br/>앱 = 약 1.5GB|
|중간|2|3\.5 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 496664MB<br/>앱 = 약 1.5GB|
|큼|4|7 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1018904MB<br/>앱 = 약 1.5GB|
|매우 큼|8|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 2083864MB<br/>앱 = 약 1.5GB|
|A5|2|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 496664MB<br/>앱 = 약 1.5GB|
|A6|4|28GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1018904MB<br/>앱 = 약 1.5GB|
|A7|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 2083864MB<br/>앱 = 약 1.5GB
|A8|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1856172MB<br/>앱 = 약 1.5GB<blockquote> 참고: 이 크기 사용에 대한 정보 및 고려 사항은 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적인 인스턴스</a>를 참조하세요.</blockquote>|
|A9|16|112GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1856172MB<br/>앱 = 약 1.5GB<blockquote> 참고: 이 크기 사용에 대한 정보 및 고려 사항은 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적인 인스턴스</a>를 참조하세요.</blockquote>|
|A10|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1856172MB<br/>앱 = 약 1.5GB<blockquote> 참고: 이 크기 사용에 대한 정보 및 고려 사항은 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적인 인스턴스</a>를 참조하세요.</blockquote>|
|A11|16|112GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1856172MB<br/>앱 = 약 1.5GB<blockquote> 참고: 이 크기 사용에 대한 정보 및 고려 사항은 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적인 인스턴스</a>를 참조하세요.</blockquote>|
|Standard\_D1|1|3\.5 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 46104MB<br/>앱 = 약 1.5GB|
|Standard\_D2|2|7 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 97304MB<br/>앱 = 약 1.5GB|
|Standard\_D3|4|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 199704MB<br/>앱 = 약 1.5GB|
|Standard\_D4|8|28GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 404504MB<br/>앱 = 약 1.5GB|
|Standard\_D11|2|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 97304MB<br/>앱 = 약 1.5GB|
|Standard\_D12|4|28GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 199704MB<br/>앱 = 약 1.5GB|
|Standard\_D13|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 404504MB<br/>앱 = 약 1.5GB|
|Standard\_D14|16|112GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 814104MB<br/>앱 = 약 1.5GB|
|Standard\_D1\_v2|1|3\.5 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 46104MB<br/>앱 = 약 1.5GB|
|Standard\_D2\_v2|2|7 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 97304MB<br/>앱 = 약 1.5GB|
|Standard\_D3\_v2|4|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 199704MB<br/>앱 = 약 1.5GB|
|Standard\_D4\_v2|8|28GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 404504MB<br/>앱 = 약 1.5GB|
|Standard\_D5\_v2|16|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 814104MB<br/>앱 = 약 1.5GB|
|Standard\_D11\_v2|2|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 97304MB<br/>앱 = 약 1.5GB|
|Standard\_D12\_v2|4|28GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 199704MB<br/>앱 = 약 1.5GB|
|Standard\_D13\_v2|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 404504MB<br/>앱 = 약 1.5GB|
|Standard\_D14\_v2|16|112GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 814104MB<br/>앱 = 약 1.5GB|
## 클라우드 서비스에 적합한 크기 구성

서비스 정의 파일에서 설명된 서비스 모델의 일부로서 역할 인스턴스의 가상 컴퓨터 크기를 지정할 수 있습니다. 역할의 크기에 따라 실행 인스턴스에 할당되는 CPU 코어 수, 메모리 용량 및 로컬 파일 시스템 크기가 결정됩니다. 응용 프로그램의 리소스 요구 사항에 따라 역할 크기를 선택합니다.

웹 역할 인스턴스에 역할 크기를 적게 설정하는 예는 다음과 같습니다.


    <WebRole name="WebRole1" vmsize="Small">
    …
    </WebRole>

지정된 로컬 리소스 크기가 위의 표에 있는 최대 로컬 리소스 크기보다 작거나 같은지 확인합니다.
## 다음 단계

[Azure용 클라우드 서비스 설정](https://msdn.microsoft.com/library/hh124108)

<!---HONumber=Oct15_HO3-->