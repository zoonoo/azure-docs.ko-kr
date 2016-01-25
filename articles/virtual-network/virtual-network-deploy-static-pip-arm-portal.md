<properties 
   pageTitle="리소스 관리자에서 미리 보기 포털을 사용하여 고정 공용 IP를 사용하는 VM 배포 | Microsoft Azure"
   description="리소스 관리자에서 미리 보기 포털을 사용하여 고정 공용 IP를 사용하는 VM을 배포하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/08/2015"
   ms.author="telmos" />

# Azure 포털을 사용하여 고정 공용 IP를 사용하는 VM 배포

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## 고정 공용 IP를 사용하여 VM 만들기 

Azure 포털에서 고정 공용 IP 주소의 VM을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 [Azure 포털](http://portal.azure.com)로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. 포털의 왼쪽 맨 위에서 **새로 만들기**>>**계산**>**Windows Server 2012 R2 Datacenter**를 차례로 클릭합니다.
3. **배포 모델 선택** 목록에서 **리소스 관리자**를 선택하고 **만들기**를 클릭합니다.
4. **기본 사항** 블레이드에서 아래와 같이 VM 정보를 입력한 다음 **확인**을 클릭합니다.

	![Azure 포털 - 기본 사항](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. **크기 선택** 블레이드에서 아래와 같이 **A1 표준**을 클릭한 다음 **선택**을 클릭합니다.

	![Azure 포털 - 크기 선택](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. **설정** 블레이드에서 **공용 IP 주소**를 클릭한 다음 **공용 IP 주소 만들기** 블레이드의 **할당**에서 아래와 같이 **고정**을 클릭합니다. 그런 다음 **확인**을 클릭합니다.

	![Azure 포털 - 공용 IP 주소 만들기](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. **설정** 블레이드에서 **확인**을 클릭합니다.
8. 아래와 같이 **요약** 블레이드를 검토한 다음 **확인**을 클릭합니다.

	![Azure 포털 - 공용 IP 주소 만들기](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. 대시보드에서 새 타일을 확인합니다.

	![Azure 포털 - 공용 IP 주소 만들기](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. VM이 만들어지면 아래와 같이 **설정** 블레이드가 표시됩니다.

	![Azure 포털 - 공용 IP 주소 만들기](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0114_2016-->