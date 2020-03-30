---
title: ILB(내부 부하 분산기) 엔드포인트 구성
titleSuffix: Azure Application Gateway
description: 이 문서에서는 개인 프런트 엔드 IP 주소로 응용 프로그램 게이트웨이를 구성하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: f56929e14aef34f675139782328ed5c559df12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198601"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>ILB(내부 로드 밸런서) 엔드포인트로 애플리케이션 게이트웨이 구성

Azure 응용 프로그램 게이트웨이는 인터넷 을 향한 VIP 또는 인터넷에 노출되지 않는 내부 끝점으로 구성할 수 있습니다. 내부 끝점은 프런트 엔드에 대한 개인 IP 주소를 사용합니다. *internal load balancer (ILB) endpoint*

프런트 엔드 개인 IP 주소를 사용하여 게이트웨이를 구성하는 것은 인터넷에 노출되지 않는 내부 업무 용 업무 용 응용 프로그램에 유용합니다. 또한 인터넷에 노출되지 않지만 라운드 로빈 로드 배포, 세션 끈기 또는 SSL(보안 소켓 계층) 종료가 필요한 보안 경계에 있는 다중 계층 응용 프로그램 내의 서비스 및 계층에도 유용합니다.

이 문서에서는 Azure 포털을 사용하여 프런트 엔드 개인 IP 주소로 응용 프로그램 게이트웨이를 구성하는 단계를 안내합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

<https://portal.azure.com>에서 Azure Portal에 로그인합니다.

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 선택할 수 있습니다. 이 예제에서는 새 가상 네트워크를 만듭니다. 애플리케이션 게이트웨이를 만드는 동시에 가상 네트워크를 만들 수 있습니다. 별도의 서브넷으로 Application Gateway 인스턴스가 만들어집니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다.

1. 포털 메뉴를 확장하고 **리소스 만들기를**선택합니다.
2. **네트워킹**을 선택한 다음, 추천 목록에서 **Application Gateway**를 선택합니다.
3. 애플리케이션 게이트웨이의 이름으로 *myAppGateway*를 입력하고 새 리소스 그룹에 대해 *myResourceGroupAG*를 입력합니다.
4. **지역,** **미국 중부를**선택합니다.
5. **계층의**경우 **표준**을 선택합니다.
6. **가상 네트워크 구성에서** 새 을 **만든**다음 가상 네트워크에 대해 다음 값을 입력합니다.
   - *myVNet* - 가상 네트워크 이름
   - *10.0.0.0/16* - 가상 네트워크 주소 공간
   - *myAGSubnet* - 서브넷 이름
   - *10.0.0.0/24* - 서브넷 주소 공간
   - *myBackendSubnet* - 백 엔드 서브넷 이름.
   - *10.0.1.0/24* - 백엔드 서브넷 주소 공간.

    ![가상 네트워크 만들기](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. **확인**을 선택하여 가상 네트워크 및 서브넷을 만듭니다.
7. **다음:프런트 엔드를**선택합니다.
8. **프런트 엔드 IP 주소 유형의**경우 **비공개**를 선택합니다.

   기본적으로 동적 IP 주소 할당입니다. 구성된 서브넷의 첫 번째 사용 가능한 주소는 프런트 엔드 IP 주소로 할당됩니다.
   > [!NOTE]
   > IP 주소 유형(정적 또는 동적)이 할당되면 나중에 변경할 수 없습니다.
9. **다음:백엔드**를 선택합니다.
10. **백 엔드 풀 추가를**선택합니다.
11. **이름의**경우 *앱게이트웨이백엔드풀을*입력합니다.
12. **대상 없이 백 엔드 풀 추가에**대해 **예**를 선택합니다. 나중에 대상을 추가합니다.
13. **추가**를 선택합니다.
14. **다음:구성을**선택합니다.
15. **라우팅 규칙에서** **규칙 추가를**선택합니다.
16. **규칙 이름의**경우 *Rrule-01을*입력합니다.
17. **수신기 이름의**경우 *수신기-01을*입력합니다.
18. **프런트 엔드 IP의**경우 **비공개**를 선택합니다.
19. 나머지 기본값을 수락하고 **백 엔드 대상** 탭을 선택합니다.
20. **대상 유형에**대해 백 **엔드 풀을**선택한 다음 **appGatewayBackendPool**을 선택합니다.
21. **HTTP 설정의**경우 **새 만들기를 선택합니다.**
22. **HTTP 설정 이름의**경우 http *설정-01을*입력합니다.
23. **백 엔드 프로토콜의**경우 **HTTP를**선택합니다.
24. **백 엔드 포트의**경우 *80을*입력합니다.
25. 나머지 기본값을 수락하고 **추가를**선택합니다.
26. 라우팅 **규칙 추가** 페이지에서 추가를 **선택합니다.**
27. 다음: 태그 를 **선택합니다.**
28. **다음 을 선택: 검토 + 만들기**.
29. 요약 페이지의 설정을 검토한 다음 **만들기를** 선택하여 네트워크 리소스 및 응용 프로그램 게이트웨이를 만듭니다. 애플리케이션 게이트웨이를 만들 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공적으로 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

## <a name="add-backend-pool"></a>백 엔드 풀 추가

백 엔드 풀은 요청을 처리하는 백 엔드 서버로 요청을 라우팅하는 데 사용됩니다. 백 엔드는 NIC, 가상 시스템 규모 집합, 공용 IP 주소, 내부 IP 주소, 완전 자격을 갖춘 도메인 이름(FQDN) 및 Azure App Service와 같은 다중 테넌트 백엔드로 구성될 수 있습니다. 이 예제에서는 가상 머신을 대상 백 엔드로 사용합니다. 기존 가상 머신을 사용해도 되고 새로 만들어도 됩니다. 이 예제에서는 Azure에서 애플리케이션 게이트웨이의 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다.

이렇게 하려면 다음을 수행합니다.

1. 백 엔드 서버로 사용되는 두 개의 새 가상 컴퓨터인 *myVM* 및 *myVM2를*만듭니다.
2. 애플리케이션 게이트웨이가 성공적으로 만들어졌는지 확인하기 위해 가상 머신에 IIS를 설치합니다.
3. 백 엔드 서버를 백 엔드 풀에 추가합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. **리소스 만들기를 선택합니다.**
2. **계산을** 선택한 다음 **가상 컴퓨터를**선택합니다.
4. 가상 머신에 대해 다음 값을 입력합니다.
   - 리소스 그룹에 대한 *myResourceGroupAG를* **선택합니다.**
   - *myVM* - **가상 컴퓨터 이름**.
   - **이미지에**대한 **Windows 서버 2019 데이터 센터를** 선택합니다.
   - *azureadmin* - **사용자 이름에**대 한 .
   - *Azure123456!* **암호에**대한 .
5. 나머지 기본값을 수락하고 **다음 : 디스크를 선택합니다.**
6. 기본값을 수락하고 **다음 : 네트워킹을**선택합니다.
7. 가상 네트워크에 대해 **myVNet**이 선택되어 있고 서브넷이 **myBackendSubnet**인지 확인합니다.
8. 나머지 기본값을 수락하고 **다음 : 관리를**선택합니다.
9. **끄기**를 선택하여 부팅 진단을 사용하지 않도록 설정합니다.
10. 나머지 기본값을 수락하고 **다음 : 고급**을 선택합니다.
11. **다음 : 태그 선택**.
12. 다음 을 선택 **: 검토 + 만들기**.
13. 요약 페이지에서 설정을 검토한 다음, **만들기**를 선택합니다. VM을 만드는 데 몇 분 정도 걸릴 수 있습니다. 배포가 성공적으로 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

### <a name="install-iis"></a>IIS 설치

1. 클라우드 셸을 열고 **PowerShell으로**설정되어 있는지 확인합니다.
    ![프라이빗 프론트엔디프-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. 다음 명령을 실행하여 가상 머신에 IIS를 설치합니다.

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. 두 번째 가상 머신을 만들고, 방금 완료한 단계를 사용하여 IIS를 설치합니다. Set-AzVMExtension의 이름 및 VMName으로 myVM2를 입력합니다.

### <a name="add-backend-servers-to-backend-pool"></a>백 엔드 풀에 백 엔드 서버 추가

1. **모든 리소스를**선택한 다음 **myAppGateway**를 선택합니다.
2. **백 엔드 풀**을 선택합니다. **앱게이트웨이백엔드풀**을 선택합니다.
3. **대상 유형에서** **가상 컴퓨터를** 선택하고 **대상**아래에서 myVM과 연결된 vNIC를 선택합니다.
4. MyVM2를 추가하려면 반복합니다.
   ![프라이빗 프론트엔디프-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. **저장을 선택합니다.**

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

1. 포털의 프런트 엔드 IP 구성 페이지를 클릭하여 할당된 **프런트 엔드 IP를** 확인합니다.
    ![프라이빗 프론트엔디프-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. 개인 IP 주소를 복사한 다음 이 VNet에 연결되어 있는 동일한 VNet 또는 온-프레미스의 VM의 브라우저 주소 표시줄에 붙여넣고 응용 프로그램 게이트웨이에 액세스하려고 시도합니다.

## <a name="next-steps"></a>다음 단계

백 엔드의 상태를 모니터링하려면 [응용 프로그램 게이트웨이에 대한 백 엔드 상태 및 진단 로그를](application-gateway-diagnostics.md)참조하십시오.
