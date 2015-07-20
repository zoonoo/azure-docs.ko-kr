<properties title="List of Oracle Virtual Machine Images" pageTitle="Oracle 가상 컴퓨터 이미지 목록" description="Azure 갤러리에서 Oracle 이미지의 목록을 가져오고 해당 이미지 한개를 기반으로 하여 Oracle 가상 머신을 만드는 방법을 알아봅니다." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Oracle 가상 컴퓨터 이미지 목록
Oracle 이미지에 따라 가상 컴퓨터를 만들려면 [Azure 포털](https://ms.portal.azure.com/)에 로그인, **마켓플레이스**, **계산**을 차례대로 클릭한 다음 **Oracle**을 검색 상자에 입력합니다. 이미지를 선택하고 Microsoft Azure에서 이미지를 설정하기 위해 지침을 따릅니다. [Azure 포털](https://ms.portal.azure.com/)의 Microsoft에 의한 Oracle 이미지는 Windows에서 실행하고 Oracle에 의한 Oracle 이미지는 Oracle Linux에서 실행해야 합니다.

![](media/virtual-machines-oracle-list-oracle-virtual-machine-images/image1.png)

##Windows 가상 컴퓨터 이미지
다음은 Azure의 Windows Server에서 실행되는 사용 가능한 Oracle 가상 이미지의 목록입니다. 이러한 이미지 사용료는 종량제 이며 이는 Oracle 라이센스 비용이 해당 이미지 사용료에 포함된다는 의미입니다. 또한 사용자는 Windows 또는 Linux에서 Oracle 소프트웨어를 실행하기 위해 자신의 라이센스를 가져올 수 있습니다. 여기에 [pVM 및 VM 겔러리 이미지에 대한 가격 책정 및 라이센스](http://azure.microsoft.com/pricing/details/virtual-machines/#oracle-software)에 대한 완전한 자세한 내용이 있습니다. 특정 Oracle 가격 책정에 대한 자세한 내용은 **Oracle** 탭을 클릭하십시오.

###Oracle 데이터베이스 가상 컴퓨터 이미지
- Oracle Database 12c Enterprise Edition on Windows Server 2012
- Oracle Database 12c Standard Edition on Windows Server 2012
- 인기있는 옵션이 탑재된 Oracle 데이터베이스 12c
- 고급 옵션이 탑제된 Oracle 데이터베이스 12c
- Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2
- Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2
- 인기있는 옵션이 탑재된 Oracle 데이터베이스 12c
- 고급 옵션이 탑재된 Oracle 데이터베이스 11g R2 EE  

###Oracle WebLogic Server 가상 컴퓨터 이미지
- Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012
- Oracle WebLogic Server 12c Standard Edition on Windows Server 2012
- Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2
- Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2  

###Oracle 데이터 베이스 Weblogic Server 가상 컴퓨터 이미지  
- Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012
- Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012
- Oracle Database 11g and WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2
- Oracle Database 11g and WebLogic Server 11g Standard Edition on Windows Server 2008 R2

### Java 가상 컴퓨터 이미지
-	JDK 8 on Windows Server 2012 R2
-	JDK 7 on Windows Server 2012
-	JDK 6 on Windows Server 2012


##Oracle Linux 가상 컴퓨터 이미지
다음은 Azure의 Oracle Linux에서 실행되는 사용 가능한 미리 구성된 Oracle 가상 컴퓨터의 목록입니다. Oracle 라이센스 비용이 이러한 미리구성된 VM 이미지 사용료에 포함되어 있지 않으므로, 사용자는 이러한 이미지에 대한 고객 자신의 라이센스를 가져오시기를 바랍니다. 사용자는 또한 Windows 또는 Linux를 운영하는 사용자 가상 컴퓨터에서 Oracle 소프트웨어를 설치하고 실행하기 위한 고객 자신의 라이센스를 가져올 수 있습니다. 여기에 [Azure에서 Oracle 라이센스 얻기](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)에 대한 자세한 전체 내용이 있습니다. 그리고 여기에 [사용자 자신의 이미지](virtual-machines-create-upload-vhd-windows-server.md)를 사용하여 VM을 만드는 법에 대한 자세한 내용이 있습니다. Oracle 및 다른 작업을 Azue에 마이그레이션 하는 방법에 대한 자세한 내용은 [Windows 가상 머신을 만드는 다양한 방법](virtual-machines-windows-choices-create-vm.md)을 참조하십시오.

- Oracle Database 12c Enterprise Edition on Oracle Linux
- Oracle Database 12c Standard Edition on Oracle Linux
- Oracle WebLogic Server 12c Enterprise Edition on Oracle Linux
- Oracle Linux 6.4.0.0.0 
- Oracle Linux 7.0.0.0.0 

##추가 리소스
[Azure 마켓플레이스의 새로운 올인원 Oracle 이미지](https://msopentech.com/blog/2015/02/19/new-one-oracle-images-azure-marketplace/)

[Oracle 가상 컴퓨터 이미지 - 기타 고려 사항](#miscellaneous-considerations-for-oracle-virtual-machine-images-new-article)

<!---HONumber=July15_HO2-->