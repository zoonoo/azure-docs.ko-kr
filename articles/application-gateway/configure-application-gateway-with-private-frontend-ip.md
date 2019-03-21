---
title: 개인 프런트 엔드 IP 주소를 사용 하 여 Azure Application Gateway 구성
description: 이 문서에서는 개인 프런트 엔드 IP 주소를 사용 하 여 Application Gateway를 구성 하는 방법에 대해 설명
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: 4755eeda6a254389f0e0fbceec602fef718a9c45
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100175"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>내부 부하 분산 장치 (ILB) 끝점을 사용 하 여 응용 프로그램 게이트웨이 구성

Azure Application Gateway는 인터넷 연결 VIP 또는 내부 끝점 (프런트 엔드 IP 주소에 대 한 개인 IP를 사용)가 인터넷에 노출 되지를 라고도 내부 부하 분산 (ILB) 끝점을 구성할 수 있습니다. 프런트 엔드 개인 IP 주소를 사용 하 여 게이트웨이 구성 하는 것은 인터넷에 노출 되지 않는 내부 기간 업무 응용 프로그램에 유용 합니다. 또한 인터넷에 노출되지 않은 보안 경계에 앉아 있는 다중 계층 애플리케이션 내에 포함된 서비스 및 계층에 유용하지만 여전히 라운드 로빈 부하 분산, 세션 인력 또는 SSL(Secure Sockets Layer) 종료가 필요합니다.

이 문서에서는 Azure Portal에서 프런트 엔드 개인 IP 주소를 사용 하 여 application gateway를 구성 하는 단계를 안내 합니다.

이 아티클에서는 다음 방법을 설명합니다.

- 응용 프로그램 게이트웨이에 대 한 개인 프런트 엔드 IP 구성을 만들려면
- 개인 프런트 엔드 IP 구성을 사용 하 여 응용 프로그램 게이트웨이 만들기

## <a name="log-in-to-azure"></a>Azure에 로그인

<https://portal.azure.com>에서 Azure Portal에 로그인

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다. 새 가상 네트워크 만들기 또는 기존 계정을 사용할 수 있습니다. 이 예제에서는 새 가상 네트워크를 만듭니다. 애플리케이션 게이트웨이를 만드는 동시에 가상 네트워크를 만들 수 있습니다. 응용 프로그램 게이트웨이 인스턴스는 별도 서브넷에서 생성 됩니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다.

1. Azure Portal의 왼쪽 위에서 **새로 만들기**를 클릭합니다.
2. **네트워킹**을 선택한 다음, 추천 목록에서 **Application Gateway**를 선택합니다.
3. 애플리케이션 게이트웨이의 이름으로 *myAppGateway*를 입력하고 새 리소스 그룹에 대해 *myResourceGroupAG*를 입력합니다.
4. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
5. **가상 네트워크 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 가상 네트워크에 대해 다음 값을 입력합니다.
   - myVNet *-가상 네트워크의 이름입니다.
   - 10.0.0.0/16*-가상 네트워크 주소 공간에 대 한 합니다.
   - *myAGSubnet* - 서브넷 이름
   - *10.0.0.0/24* - 서브넷 주소 공간  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. **확인**을 클릭하여 가상 네트워크 및 서브넷을 만듭니다.
7. 개인으로 프런트 엔드 IP 구성을 선택 이며 기본적으로 동적 IP 주소 할당 합니다. 선택한의 첫 번째 사용 가능한 주소는 프런트 엔드 IP 주소와 서브넷이 할당 됩니다.
8. 서브넷 주소 범위 (정적 할당)에서 개인 IP를 선택 하려는 경우 상자를 클릭 **특정 개인 IP 주소 선택** IP 주소를 지정 합니다.
   > [!NOTE]
   > 할당 된 후 IP 주소 유형 (정적 또는 동적) 나중에 변경할 수 없습니다.
9. 프로토콜 및 포트, WAF 구성 (필요한 경우)에 대 한 수신기 구성을 선택 하 고 확인을 클릭 합니다.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. 요약 페이지에서 설정을 검토한 다음, **확인**을 클릭하여 네트워크 리소스와 애플리케이션 게이트웨이를 만듭니다. 애플리케이션 게이트웨이가 생성되는 데 몇 분이 걸릴 수 있습니다. 배포가 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

## <a name="add-backend-pool"></a>백 엔드 풀 추가

백 엔드 풀 요청 제공 하는 백 엔드 서버로 요청을 라우팅하기 위해 사용 됩니다. 백 엔드 Nic, 가상 머신 확장 집합, 공용 Ip 구성 될 수 있습니다, 내부 Ip를 정규화 된 도메인 이름 (FQDN) 및 다중 테 넌 트 백 엔드를 Azure App Service와 같은 이 예제에서는 가상 머신 대상 백 엔드로 사용 합니다. 기존 virtual machines를 사용 하거나 새로 만들 것입니다. 이 예제에서는 Azure application gateway 백 엔드 서버로 사용 하는 두 개의 가상 머신을 만듭니다. 이 위해 수행 합니다.

1. 2 개의 새 VM을 만듭니다 *myVM* 및 *myVM2*, 백 엔드 서버로 사용할 수 있습니다.
2. Application gateway를 성공적으로 만들어졌는지 확인 하려면 가상 컴퓨터에 IIS를 설치 합니다.
3. 백 엔드 풀으로 백 엔드 서버를 추가 합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. **새로 만들기**를 클릭합니다.
2. **Compute**를 클릭한 다음, 추천 목록에서 **Windows Server 2016 Datacenter**를 선택합니다.
3. 가상 머신에 대해 다음 값을 입력합니다.
   - *myVM* - 가상 머신의 이름
   - *azureuser* - 관리자 사용자 이름
   - *Azure123456!* - 암호
   - **기존 항목 사용**을 선택한 다음, *myResourceGroupAG*를 선택합니다.
4. **확인**을 클릭합니다.
5. 선택 **DS1_V2** 크기의 가상 머신 및 클릭 **선택**합니다.
6. 가상 네트워크에 대해 **myVNet**이 선택되어 있고 서브넷이 **myBackendSubnet**인지 확인합니다.
7. **사용 안 함**을 클릭하여 부팅 진단을 사용하지 않도록 설정합니다.
8. **확인**을 클릭하고 요약 페이지에서 설정을 검토한 다음, **만들기**를 클릭합니다.

### <a name="install-iis"></a>IIS 설치

1. 대화형 셸을 열고 **PowerShell**로 설정되어 있는지 확인합니다.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. 다음 명령을 실행하여 가상 머신에 IIS를 설치합니다.

   ```azurepowershell
   Set-AzureRmVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzureRmVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
