<properties" pageTitle="title="Oracle VM 이미지 목록 | Microsoft Azure" description="Azure 갤러리의 Oracle 이미지 목록을 가져오고 이미지 한 개를 기반으로 하여 Oracle 가상 컴퓨터를 만드는 방법을 알아봅니다." services="virtual-machines" documentationCenter="" authors="bbenz" manager="" editor="" tags="azure-service-management, azure-resource-manager"/>

<tags
ms.service="virtual-machines"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-multiple"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

#Oracle 가상 컴퓨터 이미지 목록

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Oracle 이미지에 따라 가상 컴퓨터를 만들려면 [Azure 포털](https://ms.portal.azure.com/)에 로그인하고 **마켓플레이스**, **계산**을 차례로 클릭한 다음 검색 상자에 **Oracle**을 입력합니다. 이미지를 선택하고 지침에 따라 Microsoft Azure에서 이미지를 설정합니다. [Azure 포털](https://ms.portal.azure.com/)의 Microsoft 제공 Oracle 이미지는 Windows에서 실행되고 Oracle 제공 Oracle 이미지는 Oracle Linux에서 실행됩니다.

![](media/virtual-machines-oracle-list-oracle-virtual-machine-images/image1.png)

##Windows 기반 가상 컴퓨터 이미지
다음은 Azure의 Windows Server에서 실행되는 사용 가능한 Oracle 가상 컴퓨터 이미지 목록입니다. 이러한 이미지 요금은 종량제이므로 Oracle 라이선스 비용이 해당 이미지 사용료에 포함됩니다. 사용자 고유의 라이선스를 가져와 Windows 또는 Linux에서 Oracle 소프트웨어를 실행할 수도 있습니다. [Azure 가상 컴퓨터 및 가상 컴퓨터 갤러리 이미지의 가격 및 라이선스](http://azure.microsoft.com/pricing/details/virtual-machines/#oracle-software)에 대한 자세한 내용은 다음과 같습니다. 특정 Oracle 가격 책정에 대한 자세한 내용을 보려면 **Oracle** 탭을 클릭하세요.

###Oracle Database 가상 컴퓨터 이미지
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

###Oracle Database 및 WebLogic Server 가상 컴퓨터 이미지  
- Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012
- Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012
- Oracle Database 11g and WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2
- Oracle Database 11g and WebLogic Server 11g Standard Edition on Windows Server 2008 R2

### Java 가상 컴퓨터 이미지
-	JDK 8 on Windows Server 2012 R2
-	JDK 7 on Windows Server 2012
-	JDK 6 on Windows Server 2012


##Oracle Linux 가상 컴퓨터 이미지
다음은 Azure의 Oracle Linux에서 실행되는, 미리 구성된 사용 가능한 Oracle 가상 컴퓨터 이미지 목록입니다. 이러한 미리 구성된 가상 컴퓨터 이미지 사용료에는 Oracle 라이선스 비용이 포함되지 않으므로 해당 이미지에 대한 사용자 고유의 라이선스를 가져와야 합니다. 사용자 고유의 라이선스를 가져와 Windows 또는 Linux의 사용자 지정 가상 컴퓨터에서 Oracle 소프트웨어를 설치 및 실행할 수도 있습니다. 여기에 [Azure에서 Oracle 라이센스 얻기](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)에 대한 자세한 전체 내용이 있습니다. [사용자 고유의 이미지](virtual-machines-create-upload-vhd-windows-server.md)를 사용하여 가상 컴퓨터를 만드는 방법에 대한 자세한 내용은 다음과 같습니다. 이 방법과 Oracle 작업 및 다른 작업을 Azure로 마이그레이션하는 기타 방법에 대한 자세한 내용은 [Windows 기반 가상 컴퓨터를 만드는 다양한 방법](virtual-machines-windows-choices-create-vm.md)을 참조하세요.

- Oracle Database 12c Enterprise Edition on Oracle Linux
- Oracle Database 12c Standard Edition on Oracle Linux
- Oracle WebLogic Server 12c Enterprise Edition on Oracle Linux
- Oracle Linux 6.4.0.0.0
- Oracle Linux 7.0.0.0.0

##추가 리소스
[Azure 마켓플레이스의 새로운 올인원 Oracle 이미지](https://msopentech.com/blog/2015/02/19/new-one-oracle-images-azure-marketplace/)

[Oracle 가상 컴퓨터 이미지 - 기타 고려 사항](#miscellaneous-considerations-for-oracle-virtual-machine-images-new-article)

<!---HONumber=AcomDC_1203_2015-->