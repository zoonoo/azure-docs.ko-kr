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
 ms.date="06/04/2015" 
 ms.author="adegeo"/>
 
# 클라우드 서비스에 적합한 크기

이 항목에서는 클라우드 서비스 역할 인스턴스(웹 역할 및 작업자 역할)에서 사용 가능한 크기 및 옵션을 설명합니다. 또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다.

Azure 가상 컴퓨터 및 클라우드 서비스는 Azure에서 제공하는 여러 유형의 계산 리소스 중 두 가지입니다. 설명은 [Azure에서 제공하는 계산 호스팅 옵션](fundamentals-application-models.md)을 참조하세요.

> [AZURE.NOTE]관련 Azure 제한 사항에 대한 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

## 웹 및 작업자 역할 인스턴스에 적합한 크기

다음 고려 사항이 크기를 결정하는 데 도움이 될 수 있습니다.

* 이제 D 시리즈 VM을 사용하도록 인스턴스를 구성할 수 있습니다. 이러한 인스턴스는 높은 계산 능력과 임시 디스크 성능이 필요한 응용 프로그램을 실행하도록 설계되었습니다. D 시리즈 VM은 임시 디스크를 위해 빠른 프로세서, 더 높은 메모리-코어 비율 및 SSD(반도체 드라이브)를 제공합니다. 자세한 내용은 Azure 블로그에 발표된 [새로운 D 시리즈 가상 컴퓨터 크기](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)를 참조하세요.  

* 시스템 요구 사항으로 인해 웹 역할 및 작업자 역할에는 Azure 가상 컴퓨터 보다 많은 임시 디스크 공간이 필요합니다. 시스템 파일은 Windows 페이지 파일용으로 4GB, Windows 덤프 파일용으로 2GB의 공간이 예약됩니다.

* OS 디스크에는 Windows 게스트 OS가 포함되어 있으며 Program Files 폴더(다른 디스크를 지정하지 않는 경우 시작 작업을 통해 수행되는 설치 포함), 레지스트리 변경 사항, System32 폴더 및 .NET framework가 들어 있습니다.

* 로컬 리소스 디스크에는 Azure 로그 및 구성 파일, Azure 진단(IIS 로그 포함) 및 사용자가 정의한 로컬 저장소 리소스가 포함되어 있습니다.

* 앱(응용 프로그램) 디스크는 사용자의 .cspkg가 추출되는 위치로, 웹 사이트, 이진 파일, 역할 호스트 프로세스, 시작 작업, web.config 등이 포함됩니다.

* A8/A10 및 A9/A11 가상 컴퓨터 크기는 용량이 동일합니다. A8 및 A9 가상 컴퓨터 인스턴스에는 가상 컴퓨터 간의 빠른 통신을 위해 RDMA(원격 직접 메모리 액세스) 네트워크에 연결되는 추가 네트워크 어댑터가 포함됩니다. A8 및 A9 인스턴스는 MPI(메시지 전달 인터페이스)를 사용하는 응용 프로그램 등을 실행하는 동안 노드 간에 지속적이고 대기 시간이 짧은 통신이 필요한 고성능 컴퓨팅 응용 프로그램을 위해 설계되었습니다. A10 및 A11 가상 컴퓨터 인스턴스에는 추가 네트워크 어댑터가 포함되지 않습니다. A10 및 A11 인스턴스는 노드 간에 지속적이고 대기 시간이 짧은 통신을 필요로 하지 않는 고성능 컴퓨팅 응용 프로그램을 위해 설계되었으며 파라메트릭 또는 병렬 응용 프로그램으로 알려져 있습니다.

|크기|CPU<br>코어 수|메모리|디스크 크기|
|---|---|---|---|
|매우 작음|1|768 MB|OS = 게스트 OS 크기<br/>로컬 리소스 = 19GB<br/>앱 = 약 1.5GB|
|작음|1|1.75 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 224GB<br/>앱 = 약 1.5GB|
|중간|2|3.5 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 489GB<br/>앱 = 약 1.5GB|
|큼|4|7 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 999GB<br/>앱 = 약 1.5GB|
|매우 큼|8|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 2,039GB<br/>앱 = 약 1.5GB|
|A5|2|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 489GB<br/>앱 = 약 1.5GB|
|A6|4|28GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 999GB<br/>앱 = 약 1.5GB|
|A7|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 2,039GB<br/>앱 = 약 1.5GB
|A8|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1.77TB<br/>앱 = 약 1.5GB<blockquote> 참고: 이 크기 사용에 대한 정보 및 고려 사항은 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적인 인스턴스</a>를 참조하세요.</blockquote>|
|A9|16|112GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1.77TB<br/>앱 = 약 1.5GB<blockquote> 참고: 이 크기 사용에 대한 정보 및 고려 사항은 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적인 인스턴스</a>를 참조하세요.</blockquote>|
|A10|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1.77TB<br/>앱 = 약 1.5GB<blockquote> 참고: 이 크기 사용에 대한 정보 및 고려 사항은 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적인 인스턴스</a>를 참조하세요.</blockquote>|
|A11|16|112GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 1.77TB<br/>앱 = 약 1.5GB<blockquote> 참고: 이 크기 사용에 대한 정보 및 고려 사항은 <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8, A9, A10 및 A11 계산 집약적인 인스턴스</a>를 참조하세요.</blockquote>|
|Standard_D1|1|3.5 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 50GB<br/>앱 = 약 1.5GB|
|Standard_D2|2|7 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 100GB<br/>앱 = 약 1.5GB|
|Standard_D3|4|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 200GB<br/>앱 = 약 1.5GB|
|Standard_D4|8|28GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 400GB<br/>앱 = 약 1.5GB|
|Standard_D11|2|14 GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 100GB<br/>앱 = 약 1.5GB|
|Standard_D12|4|28GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 200GB<br/>앱 = 약 1.5GB|
|Standard_D13|8|56GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 400GB<br/>앱 = 약 1.5GB|
|Standard_D14|16|112GB|OS = 게스트 OS 크기<br/>로컬 리소스 = 800GB<br/>앱 = 약 1.5GB|

## 다음 단계

[Azure 용 클라우드 서비스 설정](https://msdn.microsoft.com/library/hh124108) [클라우드 서비스에 대한 크기 구성](https://msdn.microsoft.com/library/ee814754)

<!---HONumber=62-->