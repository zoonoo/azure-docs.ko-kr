<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="" solutions="" manager="" editor="" />

Azure에서 CentOS Linux를 실행하는 가상 컴퓨터에 MongoDB 설치
============================================================

[MongoDB](http://www.mongodb.org/)는 대중적인 오픈 소스의 고성능 NoSQL 데이터베이스입니다. [Azure 관리 포털](http://manage.windowsazure.com)을 사용하면 CentOS Linux를 실행하는 가상 컴퓨터를 이미지 갤러리에서 만들 수 있습니다. 그런 다음, MongoDB 데이터베이스를 가상 컴퓨터에 설치하고 구성할 수 있습니다.

다음 내용을 배웁니다.

-   관리 포털을 사용하여 CentOS Linux를 실행하는 가상 컴퓨터를 이미지 갤러리에서 만드는 방법
-   SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결하는 방법
-   가상 컴퓨터에 MongoDB를 설치하는 방법

가상 컴퓨터 미리 보기 기능 등록
-------------------------------

가상 컴퓨터를 만들려면 Azure 가상 컴퓨터 미리 보기 기능을 등록해야 합니다. Azure 계정이 없는 경우 무료 평가판 계정을 등록할 수도 있습니다.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

CentOS Linux를 실행하는 가상 컴퓨터 만들기
------------------------------------------

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

가상 컴퓨터에서 MongoDB 설치 및 실행
------------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

요약
----

이 자습서에서는 Linux 가상 컴퓨터를 만들고 SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 원격으로 연결하는 방법을 배웠습니다. 또한 MongoDB를 Linux 가상 컴퓨터에 설치하고 구성하는 방법도 배웠습니다. MongoDB에 대한 자세한 내용은 [MongoDB 설명서](http://www.mongodb.org/display/DOCS/Home)(영문)를 참조하십시오.

