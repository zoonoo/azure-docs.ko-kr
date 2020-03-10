---
title: '빠른 시작: 포털을 사용 하 여 웹 트래픽 직접 전송'
titleSuffix: Azure Application Gateway
description: Azure Portal을 사용하여 백 엔드 풀의 가상 머신에 웹 트래픽을 보내는 Azure Application Gateway를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 17adc800bd5a2ae53e27350c7e0d588eaeee4a8f
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78946061"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure Portal

이 빠른 시작에서는 Azure Portal를 사용 하 여 응용 프로그램 게이트웨이를 만듭니다. 그런 다음 테스트 하 여 제대로 작동 하는지 확인 합니다. 

응용 프로그램 게이트웨이는 응용 프로그램 웹 트래픽을 백 엔드 풀의 특정 리소스로 보냅니다. 수신기를 포트에 할당 하 고, 규칙을 만들고, 백 엔드 풀에 리소스를 추가 합니다. 간단히 하기 위해이 문서에서는 공용 프런트 엔드 IP를 사용 하는 간단한 설정, 응용 프로그램 게이트웨이에서 단일 사이트를 호스트 하는 기본 수신기, 기본 요청 라우팅 규칙 및 백 엔드 풀의 두 가상 머신을 사용 합니다.

[Azure PowerShell](quick-create-powershell.md) 또는 [Azure CLI](quick-create-cli.md)를 사용 하 여이 빠른 시작을 완료할 수도 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]



## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

응용 프로그램 게이트웨이 **만들기** 페이지의 탭을 사용 하 여 응용 프로그램 게이트웨이를 만듭니다.

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다. **새로 만들기** 창이 나타납니다.

2. **네트워킹**을 선택한 다음, **추천** 목록에서 **Application Gateway**를 선택합니다.

### <a name="basics-tab"></a>기본 사항 탭

1. **기본 사항** 탭에서 다음 애플리케이션 게이트웨이 설정에 대한 값을 입력합니다.

   - **리소스 그룹**: 리소스 그룹에 대해 **myResourceGroupAG** 를 선택 합니다. 이 리소스 그룹이 없으면 **새로 만들기**를 선택하여 새로 만듭니다.
   - **Application gateway name**: application gateway의 이름에 *myAppGateway* 를 입력 합니다.

     ![새 application gateway 만들기: 기본 사항](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 선택할 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이를 만들면서 새 가상 네트워크를 만듭니다. 별도의 서브넷으로 Application Gateway 인스턴스가 만들어집니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다.

    **가상 네트워크 구성**에서 **새로 만들기**를 선택하여 새 가상 네트워크를 만듭니다. **가상 네트워크 만들기** 창이 열리면 다음 값을 입력하여 가상 네트워크 및 두 개의 서브넷을 만듭니다.

    - **이름**: 가상 네트워크의 이름으로 *myvnet* 을 입력 합니다.

    - **서브넷 이름** (Application Gateway 서브넷): 서브넷 **표에 Default 라는 서브넷이** 표시 됩니다. 이 서브넷의 이름을 *myAGSubnet*으로 변경합니다.<br>애플리케이션 게이트웨이 서브넷은 애플리케이션 게이트웨이만 포함할 수 있습니다. 다른 리소스는 허용되지 않습니다.

    - **서브넷 이름** (백 엔드 서버 서브넷): **서브넷 표의 두** 번째 행에 있는 **서브넷 이름** 열에 *myBackendSubnet* 를 입력 합니다.

    - **주소 범위** (백 엔드 서버 서브넷): **서브넷** 표의 두 번째 행에서 *myagsubnet*의 주소 범위와 겹치지 않는 주소 범위를 입력 합니다. 예를 들어 *myAGSubnet*의 주소 범위가 10.0.0.0/24인 경우 *myBackendSubnet*의 주소 범위로 *10.0.1.0/24*를 입력합니다.

    **확인**을 선택하여 **가상 네트워크 만들기** 창을 닫고 가상 네트워크 설정을 저장합니다.

     ![새 애플리케이션 게이트웨이 만들기: 가상 네트워크](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. 기본 **사항** 탭에서 다른 설정의 기본값을 그대로 적용 하 고 다음을 선택 합니다. **프런트 엔드**.

### <a name="frontends-tab"></a>프런트 엔드 탭

1. **프런트 엔드** 탭에서 **프런트 엔드 IP 주소 형식**이 **퍼블릭**으로 설정되어 있는지 확인합니다. <br>사용 사례에 따라 프런트 엔드 IP를 공용 또는 프라이빗 IP로 구성할 수 있습니다. 이 예제에서는 공용 프런트 엔드 IP를 선택합니다.
   > [!NOTE]
   > Application Gateway v2 SKU의 경우 **퍼블릭** 프런트 엔드 IP 구성이 있어야 합니다. 여전히 퍼블릭 및 프라이빗 프런트 엔드 IP 구성을 모두 사용할 수 있지만 프라이빗 전용 프런트 엔드 IP 구성(ILB 모드만 해당)은 현재 v2 SKU에 대해 사용하도록 설정되어 있지 않습니다. 

2. **퍼블릭 IP 주소**에 대해 **새로 만들기**를 선택하고 퍼블릭 IP 주소 이름으로 *myAGPublicIPAddress*를 입력한 후 **확인**을 선택합니다. 

     ![새 애플리케이션 게이트웨이 만들기: 프런트 엔드](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. **다음: 백 엔드**를 선택 합니다.

### <a name="backends-tab"></a>백 엔드 탭

백 엔드 풀은 요청을 처리하는 백 엔드 서버로 요청을 라우팅하는 데 사용됩니다. 백 엔드 풀은 NIC, 가상 머신 확장 집합, 공용 IP, 내부 IP, FQDN(정규화된 도메인 이름) 및 다중 테넌트 백 엔드(예: Azure App Service)로 구성될 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이가 있는 빈 백 엔드 풀을 만든 다음, 백 엔드 대상을 백 엔드 풀에 추가합니다.

1. **백 엔드** 탭에서 **+백 엔드 풀 추가**를 선택합니다.

2. 열리는 **백 엔드 풀 추가** 창에서 다음 값을 입력하여 빈 백 엔드 풀을 만듭니다.

    - **이름**: 백 엔드 풀의 이름으로 *myBackendPool* 을 입력 합니다.
    - **대상이 없는 백 엔드 풀 추가**: **예** 를 선택 하 여 대상이 없는 백 엔드 풀을 만듭니다. 애플리케이션 게이트웨이를 만든 후에 백 엔드 대상을 추가합니다.

3. **백 엔드 풀 추가** 창에서 **추가**를 선택하여 백 엔드 풀 구성을 저장하고 **백 엔드** 탭으로 돌아갑니다.

     ![새 애플리케이션 게이트웨이 만들기: 백 엔드](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. **백 엔드** 탭에서 **다음: 구성**을 선택 합니다.

### <a name="configuration-tab"></a>구성 탭

**구성** 탭에서 라우팅 규칙을 사용하여 만든 프런트 엔드 및 백 엔드 풀을 연결합니다.

1. **라우팅 규칙** 열에서 **규칙 추가**를 선택합니다.

2. 열리는 **라우팅 규칙 추가** 창에서 *규칙 이름*으로 **myRoutingRule**을 입력합니다.

3. 라우팅 규칙에는 수신기가 필요합니다. **라우팅 규칙 추가** 창 내의 **수신기** 탭에서 수신기에 대해 다음 값을 입력합니다.

    - **수신기 이름**: 수신기 이름으로 *mylistener* 를 입력 합니다.
    - **프런트 엔드 ip**: **공용** 을 선택 하 여 프런트 엔드에 대해 만든 공용 IP를 선택 합니다.
  
      **수신기** 탭에서 다른 설정에 대해 기본값을 그대로 적용한 다음, **백 엔드 대상** 탭을 선택하여 나머지 라우팅 규칙을 구성합니다.

   ![새 애플리케이션 게이트웨이 만들기: 수신기](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. **백 엔드 대상** 탭에서 **백 엔드 대상**으로 **myBackendPool**을 선택합니다.

5. **Http 설정**에서 **새로 만들기**를 선택하여 새 HTTP 설정을 만듭니다. HTTP 설정에 따라 라우팅 규칙의 동작이 결정됩니다. 열리는 **http 설정 추가** 창에서 **백 엔드 포트**의 **http 설정 이름** 및 *80* 에 대해 *myhttpsetting* 을 입력 합니다. **HTTP 설정 추가** 창에서 다른 설정에 대해 기본값을 그대로 적용한 다음, **추가**를 선택하여 **라우팅 규칙 추가** 창으로 돌아옵니다. 

     ![새 application gateway 만들기: HTTP 설정](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. **라우팅 규칙 추가** 창에서 **추가**를 선택하여 라우팅 규칙을 저장하고 **구성** 탭으로 돌아옵니다.

     ![새 애플리케이션 게이트웨이 만들기: 라우팅 규칙](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. **다음: 태그** 를 선택 하 고 다음 **: 검토 + 만들기**를 선택 합니다.

### <a name="review--create-tab"></a>리뷰 + 만들기 탭

**리뷰 + 만들기** 탭에서 설정을 검토한 다음, **만들기**를 선택하여 가상 네트워크, 공용 IP 주소 및 애플리케이션 게이트웨이를 만듭니다. Azure가 애플리케이션 게이트웨이를 만들 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공적으로 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

## <a name="add-backend-targets"></a>백 엔드 대상 추가

이 예제에서는 가상 머신을 대상 백 엔드로 사용합니다. 기존 가상 머신을 사용해도 되고 새로 만들어도 됩니다. Application gateway에 대 한 백 엔드 서버로 두 개의 가상 머신을 만듭니다.

이 작업을 수행하려면 다음이 필요합니다.

1. 백 엔드 서버로 사용할 VM 2개, *myVM* 및 *myVM2*를 만듭니다.
2. 애플리케이션 게이트웨이가 성공적으로 만들어졌는지 확인하기 위해 가상 머신에 IIS를 설치합니다.
3. 백 엔드 서버를 백 엔드 풀에 추가합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다. **새로 만들기** 창이 나타납니다.
2. **인기** 목록에서 **Windows Server 2016 Datacenter**를 선택합니다. **가상 머신 만들기** 페이지가 표시됩니다.<br>Application Gateway는 백 엔드 풀에서 사용한 가상 머신 유형에 관계없이 트래픽을 라우팅할 수 있습니다. 이 예제에서는 Windows Server 2016 Datacenter를 사용합니다.
3. **기본 사항** 탭에서 다음 가상 머신 설정의 값을 입력합니다.

    - **리소스 그룹**: 리소스 그룹 이름에 대해 **myResourceGroupAG** 를 선택 합니다.
    - **가상 컴퓨터 이름**: 가상 컴퓨터의 이름으로 *myvm* 을 입력 합니다.
    - **지역**: 응용 프로그램 게이트웨이를 만든 동일한 지역을 선택 합니다.
    - **사용자 이름**: 관리자 사용자 이름으로 *azureuser* 를 입력 합니다.
    - **암호**: 암호를 입력 합니다.
4. 다른 기본값을 적용 하 고 **다음: 디스크**를 선택 합니다.  
5. **디스크** 탭 기본값을 그대로 적용 하 고 **다음: 네트워킹**을 선택 합니다.
6. **네트워킹** 탭에서 **가상 네트워크**로 **myVNet**이 선택되었고 **서브넷**이 **myBackendSubnet**으로 설정되었는지 확인합니다. 다른 기본값을 적용 하 고 **다음: 관리**를 선택 합니다.<br>Application Gateway는 가상 네트워크 외부의 인스턴스와 통신할 수 있지만, IP가 연결되어야 합니다.
7. **관리** 탭에서 **부트 진단**을 **해제**합니다. 나머지는 기본값으로 두고 **검토 + 만들기**를 선택합니다.
8. **검토 + 만들기** 탭에서 설정을 검토하고, 유효성 검사 오류를 수정하고, **만들기**를 선택합니다.
9. 가상 머신 만들기가 완료되기를 기다렸다가 계속합니다.

### <a name="install-iis-for-testing"></a>테스트를 위해 IIS 설치

이 예제에서는 Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지만 확인할 목적으로 가상 머신에 IIS를 설치합니다.

1. Azure PowerShell을 엽니다. Azure Portal의 위쪽 탐색 모음에서 **Cloud Shell** 를 선택한 다음 드롭다운 목록에서 **PowerShell** 을 선택 합니다. 

    ![사용자 지정 확장 설치](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. 다음 명령을 실행 하 여 가상 컴퓨터에 IIS를 설치 합니다. 필요한 경우 *위치* 매개 변수를 변경 합니다. 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 앞에서 완료한 단계를 사용하여 두 번째 가상 머신을 만들고 IIS를 설치합니다. 가상 머신 이름에 *myVM2*를 사용하고, **VMName** 설정에 **Set-AzVMExtension** cmdlet을 사용합니다.

### <a name="add-backend-servers-to-backend-pool"></a>백 엔드 풀에 백 엔드 서버 추가

1. Azure Portal 메뉴에서 **모든 리소스**를 선택하거나 *모든 서비스*를 검색하여 선택합니다. 그런 후 **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **백 엔드 풀**을 선택합니다.

3. **myBackendPool**을 선택합니다.

4. **백 엔드 대상**의 **대상 유형**아래에 있는 드롭다운 목록에서 **가상 머신** 를 선택 합니다.

5. **대상**의 드롭다운 목록에서 **Myvm** 및 **myVM2** 가상 머신과 연결 된 네트워크 인터페이스를 선택 합니다.


   > [!div class="mx-imgBorder"]
   > 백엔드 서버를 추가 ![](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. **저장**을 선택합니다.

7. 배포가 완료될 때까지 기다렸다가 다음 단계로 진행합니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

IIS는 응용 프로그램 게이트웨이를 만들 필요가 없지만이 빠른 시작에 설치 하 여 Azure에서 응용 프로그램 게이트웨이를 만들었는지 확인 합니다. IIS를 사용하여 애플리케이션 게이트웨이 테스트:

1. **개요** 페이지에서 애플리케이션 게이트웨이에 대한 공용 IP 주소를 찾습니다.![애플리케이션 게이트웨이 공용 IP 주소를 기록](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)하거나, **모든 리소스**를 선택하고 검색 상자에 *myAGPublicIPAddress*를 입력한 후 검색 결과에서 선택합니다. Azure는 공용 IP 주소를 **개요** 페이지에 표시합니다.
2. 공용 IP 주소를 복사 하 여 브라우저의 주소 표시줄에 붙여넣어 해당 IP 주소를 찾습니다.
3. 응답을 확인합니다. 응답이 유효하면 애플리케이션 게이트웨이가 성공적으로 만들어졌으며 백 엔드에 성공적으로 연결할 수 있다는 의미입니다.

   ![애플리케이션 게이트웨이 테스트](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   브라우저를 여러 번 새로 고치면 myVM 및 myVM2에 대 한 연결이 표시 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Application gateway를 사용 하 여 만든 리소스가 더 이상 필요 하지 않은 경우 리소스 그룹을 삭제 합니다. 리소스 그룹을 삭제 하면 응용 프로그램 게이트웨이와 관련 된 모든 리소스도 제거 됩니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal 메뉴에서 **리소스 그룹**을 선택하거나 *리소스 그룹*을 검색하여 선택합니다.
2. **리소스 그룹** 페이지의 목록에서 **myResourceGroupAG**를 검색하여 선택합니다.
3. **리소스 그룹** 페이지에서 **리소스 그룹 삭제**를 선택합니다.
4. *리소스 그룹 이름 입력*에 **myResourceGroupAG**를 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)
