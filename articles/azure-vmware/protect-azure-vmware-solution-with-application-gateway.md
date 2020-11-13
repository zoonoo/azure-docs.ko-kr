---
title: Azure 애플리케이션 Gateway를 사용 하 여 Azure VMware 솔루션에서 웹 앱 보호
description: Azure VMware 솔루션에서 실행 되는 웹 앱을 안전 하 게 노출 하도록 Azure 애플리케이션 게이트웨이를 구성 합니다.
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 7956ea51421f5cfa893942401c1d9a5871039689
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578491"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Azure 애플리케이션 Gateway를 사용 하 여 Azure VMware 솔루션에서 웹 앱 보호

[Azure 애플리케이션 게이트웨이](https://azure.microsoft.com/services/application-gateway/) 는 웹 응용 프로그램에 대 한 트래픽을 관리할 수 있는 계층 7 웹 트래픽 부하 분산 장치입니다. Azure VMware Solution v1.0 및 v2.0 모두에서 제공 됩니다. 두 버전 모두 Azure VMware 솔루션에서 실행 되는 웹 앱으로 테스트 되었습니다.

기능은 다음과 같습니다. 
- 쿠키 기반 세션 선호도
- URL 기반 라우팅
- WAF(웹 애플리케이션 방화벽)

전체 기능 목록은 [Azure 애플리케이션 게이트웨이 기능](../application-gateway/features.md)을 참조 하세요. 

이 문서에서는 웹 서버 팜 앞에 응용 프로그램 Gateway를 사용 하 여 Azure VMware 솔루션에서 실행 되는 웹 앱을 보호 하는 방법을 보여 줍니다. 

## <a name="topology"></a>토폴로지
이 다이어그램에서는 Azure IaaS Vm (가상 머신), Azure 가상 머신 확장 집합 또는 온-프레미스 서버를 보호 하는 데 Application Gateway를 사용 하는 방법을 보여 줍니다. Application Gateway는 Azure VMware 솔루션 Vm을 온-프레미스 서버로 처리 합니다. 

![Application Gateway는 Azure VMware 솔루션 Vm을 보호 합니다.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure 애플리케이션 게이트웨이는 현재 Azure VMware 솔루션 Vm에서 실행 되는 웹 앱을 노출 하는 유일한 지원 방법입니다.

이 다이어그램에서는 Azure VMware 솔루션 웹 응용 프로그램으로 Application Gateway의 유효성을 검사 하는 데 사용 되는 테스트 시나리오를 보여 줍니다.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="웹 응용 프로그램을 실행 하는 Azure VMware 솔루션과 Application Gateway 통합" border="false":::

Application Gateway 인스턴스는 전용 서브넷의 허브에 배포 됩니다. Azure 공용 IP 주소를 포함 합니다. 가상 네트워크에 대 한 표준 DDoS 보호를 활성화 하는 것이 좋습니다. 웹 서버는 NSX T0 및 T1 라우터 뒤에 있는 Azure VMware 솔루션 사설 클라우드에서 호스팅됩니다. Azure VMware 솔루션은 [express 경로 Global Reach](../expressroute/expressroute-global-reach.md) 를 사용 하 여 허브 및 온-프레미스 시스템과 통신할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정.
- Azure VMware 솔루션 사설 클라우드를 배포 하 고 실행 합니다.

## <a name="deployment-and-configuration"></a>배포 및 구성

1. Azure Portal에서 **Application Gateway** 를 검색 하 고 **응용 프로그램 게이트웨이 만들기** 를 선택 합니다.

2. 다음 그림에 나와 있는 것 처럼 기본적인 세부 정보를 제공 합니다. 그런 후 **다음: 프런트 엔드>** 를 선택 합니다. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Application Gateway 만들기":::

3. 프런트 엔드 IP 주소 유형을 선택 합니다. 공용의 경우 기존 공용 IP 주소를 선택 하거나 새 공용 IP 주소를 만듭니다. **다음: 백 엔드>** 를 선택 합니다.

    > [!NOTE]
    > 표준 및 WAF (웹 응용 프로그램 방화벽) Sku만 개인 프런트 엔드 지원 됩니다.

4. Azure VMware 솔루션 인프라에서 실행 되는 Vm의 백 엔드 풀을 추가 합니다. Azure VMware 솔루션 사설 클라우드에서 실행 되는 웹 서버에 대 한 세부 정보를 제공 하 고 **추가** 를 선택 합니다.  그런 후 **다음: 구성>** 를 선택 합니다.

1. **구성** 탭에서 **라우팅 규칙 추가** 를 선택 합니다.

6. **수신기** 탭에서 수신기에 대 한 세부 정보를 제공 합니다. HTTPS를 선택한 경우 PFX 파일 또는 기존 Azure Key Vault 인증서에서 인증서를 제공 해야 합니다. 

7. **백 엔드 대상** 탭을 선택 하 고 이전에 만든 백 엔드 풀을 선택 합니다. **HTTP 설정** 필드에서 **새로 추가** 를 선택 합니다.

8. HTTP 설정에 대 한 매개 변수를 구성 합니다. **추가** 를 선택합니다.

9. 경로 기반 규칙을 구성 하려면 **여러 대상 추가를 선택 하 여 경로 기반 규칙을 만듭니다**. 

10. 경로 기반 규칙을 추가 하 고 **추가** 를 선택 합니다. 반복 하 여 경로 기반 규칙을 추가 합니다. 

11. 경로 기반 규칙을 추가 하는 작업이 완료 되 면 **추가** 를 다시 선택 합니다. 그런 후 **다음: 태그>** 를 선택 합니다. 

12. 태그를 추가 하 고 다음을 선택 합니다 **. 검토 +>만들기** 를 선택 합니다.

13. Application Gateway에서 유효성 검사가 실행 됩니다. 성공적으로 수행 되 면 **만들기** 를 선택 하 여 배포 합니다.

## <a name="configuration-examples"></a>구성 예제

이 섹션에서는 이러한 사용 사례에 대 한 백 엔드 풀로 Azure VMware 솔루션 Vm을 사용 하 여 Application Gateway를 구성 하는 방법을 알아봅니다. 

- [여러 사이트 호스팅](#hosting-multiple-sites)
- [URL로 라우팅](#routing-by-url)

### <a name="hosting-multiple-sites"></a>여러 사이트 호스팅

이 절차에서는 기존 application gateway의 Azure VMware 솔루션 사설 클라우드에서 실행 중인 Vm을 사용 하 여 백 엔드 주소 풀을 정의 하는 방법을 보여 줍니다. 

>[!NOTE]
>이 절차에서는 여러 도메인이 있다고 가정 하므로 www.contoso.com 및 www.fabrikam.com의 예제를 사용 합니다.


1. 사설 클라우드에서 Vm의 두 가지 풀을 만듭니다. 하나는 Contoso와 두 번째 Fabrikam을 나타냅니다. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Vm을 만듭니다.":::

    이 자습서를 설명 하기 위해 설치 된 인터넷 정보 서비스 (IIS) 역할이 설치 된 Windows Server 2016을 사용 했습니다. Vm을 설치한 후에는 다음 PowerShell 명령을 실행 하 여 각 Vm에서 IIS를 구성 합니다. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. 기존 응용 프로그램 게이트웨이 인스턴스의 왼쪽 메뉴에서 **백 엔드 풀** 을 선택 하 고  **추가** 를 선택한 다음 새 풀의 세부 정보를 입력 합니다. 오른쪽 창에서 **추가** 를 선택 합니다.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="백 엔드 풀을 추가 합니다." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. **수신기** 섹션에서 각 웹 사이트에 대 한 새 수신기를 만듭니다. 각 수신기에 대 한 세부 정보를 입력 하 고 **추가** 를 선택 합니다.

4. 왼쪽에서 **HTTP 설정** 을 선택 하 고 왼쪽 창에서 **추가** 를 선택 합니다. 세부 정보를 입력 하 여 새 HTTP 설정을 만들고 **저장** 을 선택 합니다.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="세부 정보를 입력 하 여 새 HTTP 설정을 만들고 저장을 선택 합니다." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. 왼쪽 메뉴의 **규칙** 섹션에서 규칙을 만듭니다. 각 규칙을 해당 수신기와 연결 합니다. **추가** 를 선택합니다.

6. 해당 하는 백 엔드 풀 및 HTTP 설정을 구성 합니다. **추가** 를 선택합니다.

7. 연결을 테스트 합니다. 선호 하는 브라우저를 열고 Azure VMware 솔루션 환경에서 호스트 되는 다른 웹 사이트로 이동 합니다 (예:) http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="연결을 테스트 합니다.":::

### <a name="routing-by-url"></a>URL로 라우팅

이 절차에서는 기존 application gateway의 Azure VMware 솔루션 사설 클라우드에서 실행 중인 Vm을 사용 하 여 백 엔드 주소 풀을 정의 하는 방법을 보여 줍니다. 그런 다음, 웹 트래픽이 풀의 적절한 서버에 도착하도록 하는 라우팅 규칙을 만듭니다.

1. 사설 클라우드에서 웹 팜을 나타내는 가상 컴퓨터 풀을 만듭니다. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Azure VMware 솔루션에서 Vm의 풀을 만듭니다.":::

    이 자습서를 설명 하기 위해 IIS 역할이 설치 된 Windows Server 2016이 사용 되었습니다. Vm을 설치한 후에는 다음 PowerShell 명령을 실행 하 여 각 VM에 IIS 자습서를 구성 합니다. 

    첫 번째 가상 컴퓨터 contoso-web-01은 기본 웹 사이트를 호스팅합니다.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    두 번째 가상 컴퓨터 contoso-web-02는 이미지 사이트를 호스팅합니다.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    세 번째 가상 컴퓨터 contoso-웹-03은 비디오 사이트를 호스팅합니다.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. 기존 application gateway 인스턴스에 3 개의 새 백 엔드 풀을 추가 합니다. 

   1. 왼쪽 메뉴에서 **백 엔드 풀** 을 선택합니다. 
   1. **추가** 를 선택 하 고 첫 번째 풀 인 **contoso-웹** 의 세부 정보를 입력 합니다. 
   1. 하나의 VM을 대상으로 추가 합니다. 
   1. **추가** 를 선택합니다. 
   1. **Contoso 이미지** 및 **contoso-비디오** 에 대해이 프로세스를 반복 하 여 하나의 고유 VM을 대상으로 추가 합니다. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="3 개의 새 백 엔드 풀을 추가 합니다." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. **수신기** 섹션에서 포트 8080을 사용 하 여 기본 유형의 새 수신기를 만듭니다.

4. 왼쪽 탐색 영역에서 **HTTP 설정** 을 선택 하 고 왼쪽 창에서 **추가** 를 선택 합니다. 세부 정보를 입력 하 여 새 HTTP 설정을 만들고 **저장** 을 선택 합니다.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="HTP 설정 구성":::

5. 왼쪽 메뉴의 **규칙** 섹션에서 규칙을 만듭니다. 각 규칙을 이전에 만든 수신기에 연결 합니다. 그런 다음 기본 백 엔드 풀 및 HTTP 설정을 구성 합니다. **추가** 를 선택합니다.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="왼쪽 메뉴의 규칙 섹션에서 규칙을 만듭니다.":::

6. 구성을 테스트합니다. Azure Portal에서 응용 프로그램 게이트웨이에 액세스 하 고 **개요** 섹션에서 공용 IP 주소를 복사 합니다. 

   1. 새 브라우저 창을 열고 URL을 입력 `http://<app-gw-ip-address>:8080` 합니다. 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Azure Portal에서 구성을 테스트 합니다.":::

   1. URL을 `http://<app-gw-ip-address>:8080/images/test.htm`로 변경합니다.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="URL을 변경 합니다.":::

   1. URL을 다시로 변경 `http://<app-gw-ip-address>:8080/video/test.htm` 합니다.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="URL을 다시 변경 합니다.":::

## <a name="next-steps"></a>다음 단계

추가 구성 예제는 [Azure 애플리케이션 Gateway 설명서](../application-gateway/index.yml) 를 검토 하세요.