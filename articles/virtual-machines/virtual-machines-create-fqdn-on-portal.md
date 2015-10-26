<properties
   pageTitle="Azure 포털에서 VM에 FQDN 만들기 | Microsoft Azure"
   description="Azure Preview 포털에서 가상 컴퓨터를 기반으로 하는 정규화된 도메인 이름 또는 리소스 관리자용 FQDN을 만드는 방법을 알아봅니다."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/21/2015"
   ms.author="dkshir"/>

# Azure Preview 포털에서 정규화된 도메인 이름 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


[Azure Preview 포털](https://portal.azure.com)에서 **리소스 관리자** 배포 모델을 사용하여 가상 컴퓨터를 만들 경우 포털은 가상 컴퓨터용 공용 IP 리소스를 만듭니다. 이 IP 주소를 사용하여 가상 컴퓨터에 원격으로 액세스할 수 있습니다. 그러나 포털은 기본적으로 [정규화된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 또는 FQDN를 만들지 않습니다. IP 주소 대신 FQDN을 기억하고 사용하기 쉽기 때문에 이 문서는 가상 컴퓨터에 이를 추가하는 방법을 보여 줍니다.

문서에서는 **리소스 관리자**를 사용하여 포털에서 구독에 로그인하고 사용 가능한 이미지로 가상 컴퓨터를 만들었다고 가정합니다. 가상 컴퓨터가 실행되기 시작되면 다음 단계를 수행합니다.

1.  포털에서 가상 컴퓨터 설정을 보고 공용 IP 주소를 클릭합니다.

    ![locate ip resource](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  가상 컴퓨터에서 공용 IP의 **연결을 끊습니다**. 아직 도메인 이름을 표시하지 않습니다 **예** 단추를 클릭한 후 연결 끊기가 완료되기까지 몇 초 정도 걸릴 수도 있습니다.

    ![dissociate ip resource](media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    다음 단계 후에 가상 컴퓨터에 이 공용 IP를 연결합니다. 공용 IP가 _동적 공용 IP_인 경우 IPV4 주소가 손실되고 FQDN이 구성된 후에 새로 할당됩니다.

3.  **연결 끊기** 단추가 회색으로 표시되면 공용 IP의 **모든 설정** 섹션을 클릭하고 **구성** 탭을 엽니다. 원하는 DNS 이름 레이블을 입력합니다. 이 구성을 **저장**합니다.

    ![enter dns name label](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  포털에서 가상 컴퓨터 블레이드로 돌아가서 가상 컴퓨터에 대한 **모든 설정**을 클릭합니다. **네트워크 인터페이스** 탭을 열고 이 가상 컴퓨터와 연결된 네트워크 인터페이스 리소스를 클릭합니다. 그러면 포털에서 **네트워크 인터페이스** 블레이드가 열립니다.

    ![open network interface](media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  네트워크 인터페이스에 대한 **공용 IP 주소**는 비어 있습니다. 이 네트워크 인터페이스에 대한 **모든 설정** 섹션을 클릭하고 **IP 주소** 탭을 엽니다. **IP 주소** 블레이드에서 **공용 IP 주소** 필드에 대해 **사용**을 클릭합니다. **IP 주소 필요한 설정 구성** 탭을 선택하고 앞에서 연결을 끊은 기본 IP를 선택합니다. **Save**를 클릭합니다. IP 리소스를 추가하려면 몇 분이 걸릴 수 있습니다.

    ![configure IP resource](media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  다른 모든 블레이드를 닫고 **가상 컴퓨터** 블레이드로 돌아갑니다. 설정에서 공용 IP 리소스를 클릭합니다. 이제 공용 IP 블레이드에서 원하는 FQDN이 **DNS 이름**으로 표시됩니다.

    ![FQDN is created](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    이제 이 DNS 이름을 사용하여 원격으로 가상 컴퓨터에 연결할 수 있습니다. 예를 들어 정규화된 도메인 이름이 `testdnslabel.eastus.cloudapp.azure.com`이고 사용자 이름이 `adminuser`인 Linux 가상 컴퓨터에 연결하는 경우 `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com`을 사용합니다.

<!---HONumber=Oct15_HO3-->