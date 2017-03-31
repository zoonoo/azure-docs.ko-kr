---
title: "Azure에서 Linux 및 오픈 소스 컴퓨팅 | Microsoft Docs"
description: "기본 Linux 사용, Azure에서 Linux 이미지 실행 또는 업로드에 대한 몇 가지 기본적인 개념, 기타 특정 기술 및 최적화에 대한 내용 등 Azure의 Linux 및 오픈 소스 컴퓨팅 문서 목록이 포함되어 있습니다."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: a7e608b5-26ea-41e0-b46b-1a483a257754
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/27/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 5c3c7707d2e2eaa0b47084c26e96721afea4e4d8
ms.lasthandoff: 03/27/2017


---
# <a name="linux-and-open-source-computing-on-azure"></a>Azure의 오픈 소스 컴퓨팅 및 Linux
클래식 배포 모델에서 Linux 기반 가상 컴퓨터를 만들고 관리하는 데 필요한 모든 설명서를 찾아보세요.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../azure-resource-manager/resource-manager-deployment-model.md)라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

## <a name="get-started"></a>시작
* [Azure의 Linux에 대한 소개](virtual-machines-linux-intro-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식 배포 모델을 사용하여 만든 Azure 가상 컴퓨터에 대한 질문과 대답](linux/classic/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [가상 컴퓨터에 대한 이미지 정보](linux/classic/about-images.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [사용자 고유의 Distro 이미지 업로드](linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)(및 [Azure 보증 배포판](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 사용 지침)
* [Azure 클래식 포털을 사용하여 Linux VM에 로그온](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="set-up"></a>설정
* [Azure 명령줄 인터페이스(Azure CLI) 설치](../cli-install-nodejs.md)

## <a name="tutorials"></a>자습서
* [Azure에서 Linux 가상 컴퓨터에 LAMP 스택 설치](virtual-machines-linux-create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure VM의 Ruby on Rails 웹 응용 프로그램](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
* [방법: AMQP 및 서비스 버스용 Apache Qpid Proton-C 설치](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>데이터베이스
* [Azure에서 MySQL 성능 최적화](linux/classic/optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MySQL 클러스터](linux/classic/mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure에서 Linux 환경의 Cassandra 실행 및 Node.js에서 Cassandra에 액세스](linux/classic/cassandra-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MariaDbs의 다중 마스터 클러스터 만들기](linux/classic/mariadb-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="hpc"></a>HPC
* [Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작](linux/classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](linux/classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="docker"></a>Docker
* [Azure 명령줄 인터페이스(Azure CLI)에서 Docker VM 확장 사용](linux/classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure 포털에서 Docker VM 확장 사용](linux/classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure에서 docker-machine을 사용하는 방법](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="ubuntu"></a>Ubuntu
* [방법: MySQL 클러스터](linux/classic/mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [방법: Node.js 및 Cassandra](linux/classic/cassandra-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="opensuse"></a>OpenSUSE
* [방법: MySQL 설치 및 실행](linux/classic/mysql-on-opensuse.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="coreos"></a>CoreOS
* [방법: Azure에서 CoreOS를 사용하는 방법](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="planning"></a>계획
* [Azure 인프라 서비스 구현 지침](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux 사용자 이름 선택](virtual-machines-linux-usernames.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식 배포 모델에서 가상 컴퓨터에 대한 가용성 집합을 구성하는 방법](linux/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure VM에 계획된 유지 관리 예약 방법](virtual-machines-linux-planned-maintenance-schedule.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [가상 컴퓨터의 가용성 관리](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure에서 Linux 가상 컴퓨터에 대한 계획된 유지 관리](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="deployment"></a>배포
* [Linux를 실행하는 사용자 지정 가상 컴퓨터 만들기](linux/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [기본 사항: Linux VM을 캡처하여 템플릿 작성](linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="management"></a>관리
* [SSH](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux에 대한 암호 또는 SSH 속성을 다시 설정하는 방법](linux/classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [루트 사용](virtual-machines-linux-use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-resources"></a>Azure 리소스
* [Azure Linux 에이전트](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure VM 확장 및 기능](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Cloud-init에서 사용할 VM에 사용자 지정 데이터 주입](windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>저장소
* [Linux VM에 데이터 디스크 연결](linux/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Linux VM에서 데이터 디스크 분리](linux/classic/detach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>네트워킹
* [Azure에서 클래식 가상 컴퓨터에 끝점을 설정하는 방법](linux/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="troubleshooting"></a>문제 해결
* [Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure에서 새 Linux 가상 컴퓨터 생성 관련 클래식 배포 문제 해결](linux/classic/troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)  
* [Azure의 기존 Linux 가상 컴퓨터 재시작 또는 크기 조정 관련 클래식 배포 문제 해결](linux/classic/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 

## <a name="reference"></a>참조
* [ASM(Azure 서비스 관리) 모드의 Azure CLI 명령](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure 서비스 관리 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

## <a name="general-links"></a>일반 링크
다음 링크는 Microsoft 블로그, Technet 페이지 및 위의 Azure.com 설명서 외의 외부 사이트입니다. Azure와 오픈 소스 컴퓨팅 환경은 둘 다 빠르게 변화하고 있으므로 Microsoft에서 최신 항목을 지속해서 추가하고 오래된 항목을 제거하기 위해 최선을 다하고 *있음에도* 다음 링크는 분명히 오래된 버전일 것입니다. 누락된 사항이 있으면 설명을 통해 알려 주시거나 [Github 리포지토리](https://github.com/Azure/azure-content/)로 끌어오기 요청을 제출해 주세요.

* [Docker 컨테이너를 사용하여 Linux에서 ASP.NET 5 실행](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
* [OpenLogic에서 CentOS VM 이미지를 배포하는 방법](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
* [SUSE 업데이트 인프라](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
* [SAP Cloud Appliance Library용 SUSE Linux Enterprise Server](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
* [Azure에서 고가용성 Linux를 빌드하는 12단계](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
* [Azure CLI, node.js 및 jhawk를 사용하여 Azure에서 Linux 프로비전 자동화](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
* [Azure에서 GlusterFS](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
* [Azure에서 FreeBSD 실행](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
* [간편한 FreeBSD 배포](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
* [사용자 지정 FreeBSD 이미지 배포](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
* [Linux 파일 서버용 Kaspersky AV](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL
* [8가지 Azure용 오픈 소스 NoSql 데이터베이스](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
* [Slideshare (MSOpenTech):Azure에서 CouchDb 경험](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
* [node.js, CORS 및 Grunt를 사용하여 CouchDB-as-a-Service 실행](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
* [Azure Redis 캐시 서비스에서 Windows의 Redis](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
* [Redis용 ASP.NET 세션 상태 공급자 미리 보기 릴리스 발표](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
* [블로그: 이제 Azure 마켓플레이스에서 RavenHQ 사용 가능](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>빅 데이터
* [Azure Linux VM에 Hadoop 설치](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
* [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>관계형 데이터베이스
* [Microsoft Azure에서 MySQL 고가용성 아키텍처](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
* [ILB를 사용하는 corosync, pg_bouncer를 통해 Postgres 설치](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux HPC(고성능 컴퓨팅)
* [빠른 시작 템플릿: SLURM 클러스터 스핀업](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (및 [블로그 게시물](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
* [빠른 시작 템플릿: Linux 계산 노드가 포함된 HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>개발, 관리 및 최적화
개발, 관리 및 최적화 환경은 매우 광범위하고 빠르게 변화하므로 아래 목록을 출발점으로 고려해야 합니다.

* [비디오: Azure 가상 컴퓨터 : Chef, Puppet 및 Docker를 사용하여 Linux VM 관리](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)
* [CoreOS 및 Weave로 자동화된 Kubernetes 클러스터에 대한 완벽한 가이드](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
* [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Azure용 Jenkins 슬레이브 플러그 인](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
* [GitHub 리포지토리: Azure용 Jenkins 저장소 플러그인](https://github.com/jenkinsci/windows-azure-storage-plugin)
* [타사: Azure용 Hudson 슬레이브 플러그인](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
* [타사: Azure용 Hudson Storage 플러그인](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [비디오: Chef의 정의 및 작동 방식](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)
* [비디오: Linux VM에서 Azure 자동화를 사용하는 방법](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* [블로그: Linux용 Powershell DSC 작업 방법](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
* [GitHub: Docker 클라이언트 DSC](https://github.com/anweiss/DockerClientDSC)
* [Azure용 Packer 플러그인](https://github.com/msopentech/packer-azure)


