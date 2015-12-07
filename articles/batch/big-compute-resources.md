<properties
   pageTitle="큰 계산: 배치 및 HPC(고성능 컴퓨팅)에 대한 기술 리소스 | Microsoft Azure"
   description="Azure에서 대규모 병렬, 배치 및 HPC 워크로드를 실행하는 데 도움을 주는 기술 리소스를 나열합니다."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Azure에서의 큰 계산: 배치 및 HPC(고성능 컴퓨팅)에 대한 기술 리소스
Azure에서 대규모 병렬, 배치 및 HPC 작업을 실행할 수 있도록 지원하는 기술 리소스에 대한 가이드입니다. 기존 배치 또는 HPC 작업을 Azure 클라우드로 확장하거나 다양한 Azure 서비스를 사용하여 Azure에서 새로운 큰 계산 솔루션을 구축합니다.

## 솔루션 옵션

Azure의 큰 계산 옵션에 대해 알아보고 사용자의 작업 및 비즈니스 요구에 가장 적합한 방식을 선택합니다.

* [배치 및 HPC 솔루션](batch-hpc-solutions.md)

* [비디오: 클라우드에서 Azure 및 HPC를 사용한 큰 계산](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)(영문)


## Azure 배치

[배치](http://azure.microsoft.com/services/batch/)는 응용 프로그램에서 클라우드를 쉽게 지원하도록 만들고 클러스터 및 작업 스케줄러를 설정 및 관리하지 않고도 작업을 실행할 수 있게 해주는 플랫폼 서비스입니다. SDK를 사용하면 다양한 언어로 Azure 배치와 응용 프로그램을 통합하고, 데이터를 Azure에 스테이징하고, 작업 실행 파이프라인을 빌드할 수 있습니다.

* [설명서](http://azure.microsoft.com/documentation/services/batch/)

* [API 참조](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [자습서: .NET용 Azure 배치 라이브러리 시작](batch-dotnet-get-started.md)

* [배치 포럼](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [배치 비디오](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC 클러스터 솔루션

계산 집약적 작업을 실행할 수 있도록 기존 Windows 또는 Linux HPC 클러스터를 Azure에 배포하거나 확장합니다.

### Microsoft HPC 팩

HPC Pack은 Microsoft Azure 및 Windows Server 기술로 구축된 무료 HPC 솔루션으로, Windows와 Linux HPC 작업을 모두 실행할 수 있습니다.

* [HPC 팩 2012 R2 업데이트 3 다운로드](https://www.microsoft.com/ko-KR/download/details.aspx?id=49922)(영문)

* [설명서](https://technet.microsoft.com/library/jj899572.aspx)


* [Azure에서 Microsoft HPC 팩을 사용하는 HPC 클러스터 옵션](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [HPC 팩을 사용한 Azure 작업자 인스턴스에 버스트](https://technet.microsoft.com/library/gg481749.aspx)

* [HPC 팩을 사용하여 Azure 배치로 버스트](https://technet.microsoft.com/library/mt612877.aspx)


* [Windows HPC 포럼](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux 및 OSS 클러스터 솔루션

Azure 빠른 시작 템플릿을 사용하여 Linux HPC 클러스터와 오픈 소스 도구를 배포합니다.

* [SLURM 클러스터 스핀업](http://azure.microsoft.com/documentation/templates/slurm/) 및 [블로그 게시물](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [토크 클러스터 스핀업](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx)(MS-MPI)는 Microsoft가 구현한 메시지 전달 인터페이스 표준으로서, Windows 플랫폼에서 병렬 응용 프로그램을 개발 및 실행하는 데 사용됩니다.


* [MS-MPI 다운로드](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI 참조(영문)](https://msdn.microsoft.com/library/dn473458.aspx)

* [MPI 포럼](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## 계산 집약적 인스턴스

Azure는 Linux 및 Windows HPC 워크로드를 실행하도록 계산 집약적 [A8, A9, A10 및 A11 인스턴스](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md)를 비롯하여 [다양한 크기](../virtual-machines/virtual-machines-size-specs.md)를 제공합니다.

* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [MPI 응용 프로그램을 실행하기 위해 Microsoft HPC Pack을 사용하여 Windows RDMA 클러스터 설정](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## 아키텍처 청사진

* [대규모 컴퓨팅 - 금융 서비스](http://go.microsoft.com/fwlink/?LinkId=536378)(PDF)에서는 위험 관리, 보고, 시뮬레이션을 위해 클라우드에서 대규모 계산 및 데이터 분석을 운영 및 조정하는 방법을 보여 줍니다.

## 샘플 및 데모

* [Azure 배치 코드 샘플](https://github.com/Azure/azure-batch-samples)

* [배치 응용 프로그램 혼합기 샘플](https://github.com/Azure/azure-batch-apps-blender) 및 [블로그 게시물](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## 관련 Azure 서비스

* [데이터 팩터리](http://azure.microsoft.com/documentation/services/data-factory/)

* [기계 학습](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [가상 컴퓨터](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [클라우드 서비스](http://azure.microsoft.com/documentation/services/cloud-services/)

* [미디어 서비스](http://azure.microsoft.com/documentation/services/media-services/)



## 다음 단계

* 최신 공지 사항은 [Microsoft HPC 및 배치 팀 블로그](http://blogs.technet.com/b/windowshpc/) 및 [Azure 블로그](http://azure.microsoft.com/blog/tag/hpc/)를 참조하세요.
* 또한 [배치의 새로운 기능](http://azure.microsoft.com/updates/?service=batch)을 참조하거나 [RSS 피드](http://azure.microsoft.com/updates/feed/?service=batch)를 구독하세요.

<!---HONumber=AcomDC_1125_2015-->