---
title: 클라우드의 배치 및 HPC 워크로드에 대한 리소스 | Microsoft Docs
description: Azure에서 대규모 병렬, 배치 및 HPC(고성능 컴퓨팅) 워크로드를 실행하는 데 도움을 주는 기술 리소스를 나열합니다.
services: batch, cloud-services, virtual-machines
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''

ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 09/22/2016
ms.author: danlep

---
# Azure에서의 큰 계산: 배치 및 고성능 컴퓨팅에 대한 기술 리소스
Azure에서 대규모 병렬, 배치 및 HPC(고성능 컴퓨팅) 워크로드를 실행하는 데 도움을 주는 기술 리소스를 나열하는 가이드입니다. 기존 배치 또는 HPC 작업을 Azure 클라우드로 확장하거나 다양한 Azure 서비스를 사용하여 새로운 큰 계산 솔루션을 구축합니다.

## 솔루션 옵션
Azure의 큰 계산 옵션에 대해 알아보고 사용자의 작업 및 비즈니스 요구에 가장 적합한 방식을 선택합니다.

* [배치 및 HPC 솔루션](batch-hpc-solutions.md)
* [비디오: 클라우드에서 Azure 및 HPC를 사용한 큰 계산(영문)](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)

## Azure 배치
[배치](https://azure.microsoft.com/services/batch/)는 Linux 및 Windows 응용 프로그램에서 클라우드를 쉽게 지원하도록 만들고 클러스터 및 작업 스케줄러를 설정 및 관리하지 않고도 작업을 실행할 수 있게 해주는 플랫폼 서비스입니다. SDK를 사용하면 다양한 언어로 Azure 배치와 응용 프로그램을 통합하고, 데이터를 Azure에 스테이징하고, 작업 실행 파이프라인을 빌드할 수 있습니다.

* [설명서](https://azure.microsoft.com/documentation/services/batch/)
* [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) 및 [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API 참조
* [배치 관리 .NET 라이브러리](https://msdn.microsoft.com/library/mt463120.aspx) 참조
* 자습서: [NET용 Azure 배치 라이브러리](batch-dotnet-get-started.md) 및 [배치 Python 클라이언트](batch-python-tutorial.md) 시작하기
* [배치 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=azurebatch)
* [배치 비디오](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC 클러스터 솔루션
계산 집약적 작업을 실행할 수 있도록 기존 Windows 또는 Linux HPC 클러스터를 Azure에 배포하거나 확장합니다.

### Microsoft HPC 팩
HPC Pack은 Microsoft Azure 및 Windows Server 기술로 구축된 무료 HPC 솔루션으로, Windows와 Linux HPC 작업을 모두 실행할 수 있습니다.

* [HPC 팩 2012 R2 업데이트 3 다운로드(영문)](https://www.microsoft.com/download/details.aspx?id=49922)
* [설명서](https://technet.microsoft.com/library/jj899572.aspx)
* Azure의 [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md) 및 [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md) HPC 팩 클러스터 옵션
* [HPC 팩을 사용한 Azure 작업자 인스턴스에 버스트](https://technet.microsoft.com/library/gg481749.aspx)
* [HPC 팩을 사용하여 Azure 배치로 버스트](https://technet.microsoft.com/library/mt612877.aspx)
* [Windows HPC 포럼](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux 및 OSS 클러스터 솔루션
Linux HPC 클러스터를 배포하려면 이러한 Azure 템플릿을 사용합니다.

* [SLURM 클러스터 스핀업](https://azure.microsoft.com/documentation/templates/slurm/) 및 [블로그 게시물](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [토크 클러스터 스핀업](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [Lustre 소프트웨어용 Intel 클라우드 버전 - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)

## Microsoft MPI
[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx)(MS-MPI)는 Microsoft가 구현한 메시지 전달 인터페이스 표준으로서, Windows 플랫폼에서 병렬 응용 프로그램을 개발 및 실행하는 데 사용됩니다. 최신 버전은 MS-MPI v7입니다.

* [MS-MPI 다운로드](http://go.microsoft.com/FWLink/p/?LinkID=389556)
* [MS-MPI 참조(영문)](https://msdn.microsoft.com/library/dn473458.aspx)
* [MPI 포럼](https://social.microsoft.com/Forums/ko-KR/home?forum=windowshpcmpi)

## 계산 집약적 인스턴스
Azure는 Linux 및 Windows HPC 워크로드를 실행하도록 백 엔드 RDMA 네트워크에 연결할 수 있는 계산 집약적 [계산 집약적](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) 인스턴스를 비롯하여 [다양한 VM 크기](../virtual-machines/virtual-machines-windows-sizes.md)를 제공합니다.

* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](../virtual-machines/virtual-machines-linux-classic-rdma-cluster.md)
* [MPI 응용 프로그램을 실행하기 위해 Microsoft HPC Pack을 사용하여 Windows RDMA 클러스터 설정](../virtual-machines/virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

## 샘플 및 데모
* [Azure 배치 C# 및 Python 코드 샘플](https://github.com/Azure/azure-batch-samples)
* 배치 스타일 Dockerized 워크로드의 간편한 배포를 위한 [Batch Shipyard](https://azure.github.io/batch-shipyard/) 도구 키트
* [HPC용 SUSE Linux Enterprise Server 시험 사용](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## 관련 Azure 서비스
* [데이터 팩터리](https://azure.microsoft.com/documentation/services/data-factory/)
* [기계 학습](https://azure.microsoft.com/documentation/services/machine-learning/)
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [가상 컴퓨터](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [가상 컴퓨터 크기 집합](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)
* [클라우드 서비스](https://azure.microsoft.com/documentation/services/cloud-services/)
* [앱 서비스](https://azure.microsoft.com/documentation/services/app-service/)
* [미디어 서비스](https://azure.microsoft.com/documentation/services/media-services/)
* [함수](https://azure.microsoft.com/documentation/services/functions/)

## 아키텍처 청사진
* [Azure 배치 및 Azure Data Factory를 사용하여 HPC 및 데이터 오케스트레이션](http://go.microsoft.com/fwlink/?linkid=717686)(PDF) 및 [문서](../data-factory/data-factory-data-processing-using-batch.md)

## 업계 솔루션
* [뱅킹 및 자금 시장](https://finance.azure.com/)
* [엔지니어링 시뮬레이션](https://simulation.azure.com/)

## 고객 사례
* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168)
* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)
* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)
* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)
* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)
* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

## 다음 단계
* 최신 공지 사항은 [Microsoft HPC 및 배치 팀 블로그](http://blogs.technet.com/b/windowshpc/) 및 [Azure 블로그](https://azure.microsoft.com/blog/tag/hpc/)를 참조하세요.
* 또한 [배치의 새로운 기능](https://azure.microsoft.com/updates/?service=batch)을 참조하거나 [RSS 피드](https://azure.microsoft.com/updates/feed/?service=batch)를 구독하세요.

<!---HONumber=AcomDC_0928_2016-->