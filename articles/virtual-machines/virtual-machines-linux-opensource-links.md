<properties
	pageTitle="Azure에서 Linux 및 오픈 소스 컴퓨팅 | Microsoft Azure"
	description="기본 Linux 사용, Azure에서 Linux 이미지 실행 또는 업로드에 대한 몇 가지 기본적인 개념, 기타 특정 기술 및 최적화에 대한 내용 등 Azure의 Linux 및 오픈 소스 컴퓨팅 문서 목록이 포함되어 있습니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/03/2016"
	ms.author="rasquill"/>



# Azure의 오픈 소스 컴퓨팅 및 Linux

이 문서에서는 Microsoft와 그 파트너가 Linux 기반 가상 컴퓨터 실행과 기타 오픈 소스 계산 환경 및 Microsoft Azure의 응용 프로그램에 대해 작성한 모든 항목을 한 곳에 정리해서 보여 줍니다. 클래식 배포 모델을 설명하는 문서는 리소스 관리자 배포 모델을 사용하는 문서처럼 기록됩니다. 배포 모델 메모가 없는 문서는 배포 모델을 모두 설명합니다.

Azure와 오픈 소스 컴퓨팅 환경은 둘 다 빠르게 변화하고 있으므로 Microsoft에서 최신 항목을 지속적으로 추가하고 오래된 항목을 제거하기 위해 최선을 다하고 *있음에도* 이 문서는 분명히 오래된 버전이 될 것입니다. 누락된 사항이 있으면 설명을 통해 알려 주시거나 [GitHub 리포지토리](https://github.com/Azure/azure-content/)로 끌어오기 요청을 제출해 주세요.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## 일반 사항
섹션은 이 페이지 오른쪽에 구분되어 있습니다. 항목이 둘 이상의 개념, Linux 배포판 또는 기술과 관련이 있응 수 있으므로 링크가 둘 이상의 섹션에 나와 있을 수 있습니다. 또한 여러 Linux 옵션, 이미지 리포지토리, 사례 연구 및 고유한 사용자 지정 이미지 업로드 방법을 설명하는 여러 항목이 있습니다.

- [Azure 마켓플레이스](https://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [이벤트 및 데모: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [클래식 배포]: [방법: 사용자 고유의 Distro 이미지 업로드](virtual-machines-linux-classic-create-upload-vhd.md)(및 [Azure 보증 배포판](virtual-machines-linux-endorsed-distros.md) 사용 지침)
- [참고: Azure에서 실행하기 위한 일반적인 Linux 요구 사항](virtual-machines-linux-create-upload-generic.md)
- [클래식 배포]: [참고: Azure의 Linux에 대한 일반적인 소개](virtual-machines-linux-intro-on-azure.md)

<!--
- [Distros](#distros) &mdash; Topics to do with a specific distro.
- [The Basics](#basics) &mdash; A lot of the basic things to do that you either know or need to know.
- [Community Images and Repositories](#images) &mdash; Other places for very useful information, repositories, and binaries.
- [Languages and Platforms](#langsandplats)
- [Samples and Scripts](#samples)
- [Auth and Encryption](#security) &mdash; Important security-related topics, not necessarily specific to Azure.
- [Devops, Management, and Optimization](#devops) &mdash; A big category, changing rapidly.
- [Support, Troubleshooting, and "It Just Doesn't Work"](#supportdebug) &mdash; Really.
-->

## Linux 배포판

일반적으로 패키지 관리 시스템에서 세분화되는 다수의 Linux 배포판이 있습니다. 일부는 dpkg 기반, Debian 및 Ubuntu와 같으며 나머지는 rpm 기반, CentOS, SUSE, RedHat 등입니다. 일부 회사는 Microsoft의 공식 파트너로서 보증된 Linux 배포판 이미지를 제공합니다. 커뮤니티에서 제공하는 Linux 배포판도 있습니다. 이 섹션의 Linux 배포판은 다른 기술의 예제에서만 사용된 경우에도 관련 공식 문서가 있습니다.

### [Ubuntu](https://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu는 매우 널리 사용되는 Azure 보증 Linux 배포판으로, dkpg 및 apt-get 패키지 관리를 기반으로 합니다.

1. [방법: 사용자 고유의 Ubuntu 이미지 업로드](virtual-machines-linux-create-upload-ubuntu.md)
2. [방법: Ubuntu LAMP 스택](virtual-machines-linux-install-lamp-stack.md)
3. [클래식 배포]: [방법: MySQL 클러스터](virtual-machines-linux-classic-mysql-cluster.md)
4. [클래식 배포]: [방법: Node.js 및 Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)
5. [리소스 관리자 배포]: [방법: IPython Notebook](virtual-machines-linux-jupyter-notebook.md)
6. [클래식 배포]: [Geeking out: Docker 컨테이너를 사용하여 Linux에서 ASP.NET 5 실행](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)


### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian은 Linux 및 오픈 소스 환경에 중요한 배포판으로, dpgk 및 apt-get 패키지 관리를 기반으로 합니다. MSOpenTech VM Depot에 사용할 여러 이미지가 있습니다.

### CentOS

CentOS Linux 배포판은 RHEL(Red Hat Enterprise Linux) 원본에서 파생된 안정적이고, 예측 가능하며, 관리가 용이하고, 재현 가능한 플랫폼입니다.

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [이미지 갤러리](https://azure.microsoft.com/marketplace/partners/OpenLogic/)
3. [방법: Azure에 대한 사용자 지정 CentOS 기반 VM 준비](virtual-machines-linux-create-upload-centos.md)
4. [클래식 배포]: [블로그: OpenLogic에서 CentOS VM 이미지를 배포하는 방법](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [클래식 배포]: [방법: AMQP 및 서비스 버스용 Apache Qpid Proton-C 설치](../service-bus/service-bus-amqp-apache.md/)

### SUSE Linux Enterprise Server 및 openSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [클래식 배포]: [방법: MySQL 설치 및 실행](virtual-machines-linux-classic-mysql-on-opensuse.md)
12. [방법: Prep a Custom SLES or openSUSE VM](virtual-machines-linux-suse-create-upload-vhd.md)  
13. [[SUSE 포럼] 방법: 새 패치 서버로 이동](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [이미지: SAP 클라우드 어플라이언스 라이브러리용 SUSE Linux Enterprise Server](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS는 사용자 지정에 대한 제어 수준이 뛰어난 순수 계산용으로 최적화된 소규모 Linux 배포판입니다.

10. [이미지 갤러리](https://azure.microsoft.com/marketplace/partners/coreos/)  
11. [클래식 배포]: [방법: Azure에서 CoreOS 사용](virtual-machines-linux-classic-coreos-howto.md)
12. [클래식 배포]: [방법: Azure의 CoreOS에서 Fleet 및 Docker 시작](virtual-machines-linux-classic-coreos-fleet-get-started.md)


#### [Oracle Linux](https://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Azure용 Oracle Linux 가상 컴퓨터 준비](virtual-machines-linux-oracle-create-upload-vhd.md)

### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [클래식 배포]: [블로그: Azure에서 FreeBSD 실행](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [클래식 배포]: [블로그: 간편한 FreeBSD 배포](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [블로그: 사용자 지정 FreeBSD 이미지 배포](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [방법: Azure Linux 에이전트 설치](virtual-machines-linux-agent-user-guide.md)
18. [마켓플레이스: Linux 파일 서버용 Kaspersky AV](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## 기본 사항

1. [기본 사항: Azure 명령줄 인터페이스(Azure CLI)](../xplat-cli-install.md)
<!-- 4. [The basics: Certificate Use and Management](http://msdn.microsoft.com/library/azure/gg981929.aspx)-->
5. [기본 사항: Linux 사용자 이름 선택](virtual-machines-linux-usernames.md)
6. [클래식 배포]: [기본 사항: Azure 클래식 포털을 사용하여 Linux VM에 로그온](virtual-machines-linux-classic-log-on.md)
7. [기본 사항: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [클래식 배포]: [기본 사항: Linux에 대한 암호 또는 SSH 속성을 다시 설정하는 방법](virtual-machines-linux-classic-reset-access.md)
9. [기본 사항: 루트 사용](virtual-machines-linux-use-root-privileges.md)
10. [클래식 배포]: [기본 사항: Linux VM에 데이터 디스크 연결](virtual-machines-linux-classic-attach-disk.md)
11. [클래식 배포]: [기본 사항: Linux VM에서 데이터 디스크 분리](virtual-machines-linux-classic-detach-disk.md)
12. [기본 사항 블로그: Linux 및 Azure를 사용하여 저장소, 디스크 및 성능 최적화](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [기본 사항: RAID](virtual-machines-linux-configure-raid.md)
14. [클래식 배포]: [기본 사항: Linux VM을 캡처하여 템플릿 작성](virtual-machines-linux-classic-capture-image.md)
15. [기본 사항: Azure Linux 에이전트](virtual-machines-linux-agent-user-guide.md)
16. [기본 사항: Azure VM 확장 및 기능](virtual-machines-windows-extensions-features.md)
17. [클래식 배포]: [기본 사항: Cloud-init에서 사용할 VM에 사용자 지정 데이터 주입](virtual-machines-windows-classic-inject-custom-data.md)
18. [클래식 배포]: [기본 사항 블로그: 12단계에서 Azure에서 고가용성 Linux 빌드](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [클래식 배포]: [기본 사항 블로그: Azure CLI, node.js, jhawk를 사용하여 Azure에서 Linux 프로비전 자동화](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
20. [기본 사항: Azure Docker VM 확장](virtual-machines-linux-dockerextension.md)
23. [클래식 배포]: [Azure 서비스 관리 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) 참조
24. [클래식 배포]: [Azure에서 GlusterFS](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## 커뮤니티 이미지 및 리포지토리
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &mdash; 커뮤니티에서 제공한 가상 컴퓨터 이미지용
4. [GitHub](https://github.com/Azure/) &mdash; Azure CLI 및 기타 많은 도구 및 프로젝트용
5. [Docker 허브 레지스트리](https://registry.hub.docker.com/) &mdash; Docker 컨테이너 이미지용 레지스트리

## 언어 및 플랫폼
### [Azure Java 개발자 센터](https://azure.microsoft.com/develop/java/)

1. [이미지](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [방법: AMQP 1.0을 사용하여 Java에서 서비스 버스 사용](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [방법: Azure 클래식 포털을 사용하여 Linux에 Tomcat7 설치](virtual-machines-linux-classic-setup-tomcat.md)
4. [비디오: 서비스 관리용 Azure Java SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [블로그: Java 용 Azure 관리 라이브러리 블로그: 시작](https://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [GitHub 리포지토리: Azure Toolkit for Eclipse with Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [참고: Azure Toolkit for Eclipse with Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [GitHub 리포지토리: IntelliJ IDEA 및 Android Studio용 MS Open Tech Tools 플러그 인](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [블로그: MSOpenTech는 OpenJDK에 참가](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [이미지: WebSphere](https://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [이미지: WebLogic](https://azure.microsoft.com/marketplace/?term=weblogic)


### JVM 언어

1. [Scala: Azure 클라우드 서비스에서 Play Framework 응용 프로그램 실행](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### SDK 유형, 설치, 업그레이드
4. [Azure 서비스 관리 SDK: Java](http://dl.windowsazure.com/javadoc/)
5. [Azure 서비스 관리 SDK: Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Azure 서비스 관리 SDK: Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [방법: Ruby on Rails 설치](virtual-machines-linux-classic-ruby-rails-web-app.md)
6. [Azure 서비스 관리 SDK: Python](https://github.com/Azure/azure-sdk-for-python)
    - [방법: Django Hello World 웹 응용 프로그램 (Mac-Linux)](virtual-machines-linux-python-django-web-app.md)
7. [Azure 서비스 관리 SDK: Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Azure 서비스 관리 SDK: PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [방법: Azure VM에 LAMP 스택 설치](virtual-machines-linux-install-lamp-stack.md)
    - [비디오: Azure VM에 LAMP 스택 설치](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Azure 서비스 관리 SDK: .NET](https://github.com/Azure/azure-sdk-for-net)
10. [블로그: Mono, ASP.NET 5, Linux 및 Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## 예제 및 스크립트

이 섹션을 찾아서 신속하게 채웁니다. 제안 사항이 있을 경우 PR을 보내거나 아래 주석에서 남겨주세요.

2. [Patrick Chanezon의 Azure Linux GitHub 리포지토리](https://github.com/chanezon/azure-linux)
3. [비디오: **usbip**을 사용하여 Linux의 온-프레미스 USB 데이터를 Azure로 이동하는 방법](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [비디오: fernapp을 사용하여 브라우저에서 Azure의 Linux 기반 GUI에 액세스](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [비디오: Azure 파일 미리 보기를 사용하는 Linux의 공유 저장소 - 1부](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [비디오: 서비스 버스 및 웹 사이트를 사용하여 Azure에서 Linux 장치 사용](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [비디오: Microsoft Azure에 네이티브 Linux 기반 memcached 응용 프로그램 연결](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [비디오: Azure에서 고가용성 Linux 서비스 부하 분산: OpenLDAP and MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## Data

이 섹션에서는 NoSQL, 관계형, 빅 데이터 등 여러 가지 저장소 접근 방식 및 기술에 대한 정보를 제공합니다.

### NoSQL

1. [블로그: 8가지 Azure용 오픈 소스 NoSql 데이터베이스](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech):Azure에서 CouchDb 경험](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [블로그: node.js, CORS 및 Grunt를 사용하여 CouchDB-as-a-Service 실행](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [방법: Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 Node.js 응용 프로그램 만들기](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md)
4. Cassandra
    - [방법: Azure에서 Linux 환경의 Cassandra 실행 및 Node.js에서 Cassandra에 액세스](virtual-machines-linux-classic-cassandra-nodejs.md)
5. Redis
    - [블로그: Azure Redis 캐시 서비스에서 Windows의 Redis](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [블로그: Redis용 ASP.NET 세션 상태 공급자 미리 보기 릴리스 발표](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [블로그: 이제 Azure 마켓플레이스에서 RavenHQ 사용 가능](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### 빅 데이터
2. Hadoop/Cloudera  
	- [블로그: Azure Linux VM에 Hadoop 설치](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
3. [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) - Azure에서 완전히 관리되는 Hadoop 서비스

### 관계형 데이터베이스
2. MySQL
    - [방법: MySQL 설치 및 실행](virtual-machines-linux-classic-mysql-on-opensuse.md)
    - [방법: Azure에서 MySQL 성능 최적화](virtual-machines-linux-classic-optimize-mysql.md)
    - [방법: MySQL 클러스터](virtual-machines-linux-classic-mysql-cluster.md)
    - [방법: 마켓플레이스를 사용하여 MySQL 데이터베이스 만들기](../store-php-create-mysql-database.md)
    - [방법: Azure 웹 사이트의 Django 및 MySQL에 Python 및 Visual Studio 사용](../app-service-web/web-sites-python-ptvs-django-mysql.md)
    - [Microsoft Azure에서 MySQL 고가용성 아키텍처](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [방법: MariaDbs의 다중 마스터 클러스터 만들기](virtual-machines-linux-classic-mariadb-mysql-cluster.md)
8. [ILB를 사용하여 corosync, pg\_bouncer와 함께 Postgres 설치](https://github.com/chgeuer/postgres-azure)


## 인증 및 암호화

인증 및 암호화는 소프트웨어 개발에서 중요한 항목이며, 웹에 인증 및 암호화 둘 다에 대한 적절한 보안 기술을 학습하고 사용하는 방법을 설명하는 많은 항목이 있습니다. 여기서는 Linux 및 오픈 소스 작업을 신속하게 실행하기 위한 몇 가지 기본 사용 방법 및 Azure에서 원격 보안 기능을 다시 설정하거나 제거하는 데 사용되는 도구에 대해 설명합니다. 이상이 기본 절차로, 더 복잡한 시나리오도 곧 추가될 예정입니다.

4. [기본 사항: 인증서 사용 및 관리](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [기본 사항: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [기본 사항: Linux에 대한 암호 또는 SSH 속성을 다시 설정하는 방법](virtual-machines-linux-classic-reset-access.md)
9. [기본 사항: 루트 사용](virtual-machines-linux-use-root-privileges.md)

## Linux HPC(고성능 컴퓨팅)

오픈 소스 도구나 Microsoft HPC Pack을 통해 Linux VM 클러스터에서 HPC 작업을 실행합니다.

1.	[퀵스타트 템플릿: SLURM 클러스터 스핀업](https://azure.microsoft.com/documentation/templates/slurm/)(및 [블로그 게시물](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
3.	[빠른 시작 템플릿: Linux 계산 노드가 포함된 HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[자습서: Azure에서 HPC Pack 클러스터의 Linux 컴퓨터 노드 시작](virtual-machines-linux-classic-hpcpack-cluster.md)
5.	[자습서: Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
6.	[자습서: MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-classic-rdma-cluster.md)


## 개발, 관리 및 최적화

이 섹션에는 일련의 비디오를 포함하는 블로그 항목의 시작 [비디오: Azure 가상 컴퓨터: Chef를 사용하여, Puppet 및 Linux VM을 관리하기 위한 Docker](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). 그러나 개발, 관리 및 최적화 환경은 매우 광범위하고 빠르게 변화하므로 아래 목록을 출발점으로 고려해야 합니다.

1. Docker
	- [Azure의 Linux용 Docker VM 확장](virtual-machines-linux-dockerextension.md)
	- [Azure 명령줄 인터페이스(Azure CLI)에서 Docker VM 확장 사용](virtual-machines-linux-classic-cli-use-docker.md)
	- [Azure 포털에서 Docker VM 확장 사용](virtual-machines-linux-classic-portal-use-docker.md)
    - [Azure 마켓플레이스에서 신속하게 Docker 시작](virtual-machines-linux-classic-docker-quickstart.md)
	- [Azure에서 docker-machine을 사용하는 방법](virtual-machines-linux-classic-docker-machine.md)
	- [Azure에서 swarm과 함께 Docker를 사용하는 방법](virtual-machines-linux-docker-swarm.md)
	- [Azure 가상 컴퓨터에서 Docker 및 Compose 시작](virtual-machines-linux-docker-compose-quickstart.md)

2. [CoreOS와 함께 Fleet 사용](virtual-machines-linux-classic-coreos-howto.md)
3. Deis
	- [GitHub 리포지토리: Azure에서 CoreOS 클러스터에 Deis 설치](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [CoreOS 및 Weave로 자동화된 Kubernetes 클러스터에 대한 완벽한 가이드](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
5. Jenkins 및 Hudson
	- [블로그: Azure용 Jenkins Slave 플러그인](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub 리포지토리: Azure용 Jenkins 저장소 플러그인](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [타사: Azure용 Hudson 슬레이브 플러그인](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [타사: Azure용 Hudson Storage 플러그인](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef 및 가상 컴퓨터](virtual-machines-windows-chef-automation.md)
	- [비디오: Chef의 정의 및 작동 방식](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure 자동화
	- [비디오: Linux VM에서 Azure 자동화를 사용하는 방법](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Linux용 Powershell DSC
    - [블로그: Linux용 Powershell DSC 작업 방법](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: Docker 클라이언트 DSC](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Azure용 Packer 플러그인](https://github.com/msopentech/packer-azure)

## 지원, 문제 해결 및 "작동 오류"

1. Microsoft 지원 문서
	- [지원: Microsoft Azure의 Linux 이미지 지원](http://support2.microsoft.com/kb/2941892)

<!--Anchors-->
[Distros]: #distros
[The Basics]: #basics
[Community Images and Repositories]: #images
[Languages and Platforms]: #langsandplats
[Samples and Scripts]: #samples
[Auth and Encryption]: #security
[Devops, Management, and Optimization]: #devops
[Support, Troubleshooting, and "It Just Doesn't Work"]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash. -->
[How to use docker-machine on Azure]: virtual-machines-linux-classic-docker-machine.md
[How to use docker with swarm on Azure]: virtual-machines-docker-swarm.md

<!---HONumber=AcomDC_0323_2016-->