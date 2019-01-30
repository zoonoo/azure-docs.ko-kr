---
title: Azure 및 Azure Stack을 사용 하 여 하이브리드 클라우드 배포 | Microsoft Docs
description: Azure 및 Azure Stack을 사용 하 여 하이브리드 클라우드를 배포 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 5f142192571bdd15a33575a425d75baf3e5caea2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243490"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>자습서: Azure 및 Azure Stack을 사용 하 여 하이브리드 클라우드 솔루션 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이 자습서에서는 Azure 공용 클라우드 및 Azure Stack 사설 클라우드를 사용 하는 하이브리드 클라우드 솔루션을 배포 하는 방법을 보여 줍니다.

하이브리드 클라우드 솔루션을 사용 하 여 공용 클라우드의 확장성을 사용 하 여 규정 준수에 대 한 이점은 사설 클라우드를 결합할 수 있습니다. 또한 개발자는 Microsoft 개발자 에코 시스템을 활용 하 고 클라우드 및 온-프레미스 환경에서 자신의 기술을 적용할 수 있습니다.

## <a name="overview-and-assumptions"></a>개요 및 가정

개발자는 공용 클라우드 및 사설 클라우드에 동일한 웹 응용 프로그램 배포를 수 있는 워크플로 설정 하려면이 자습서를 수행할 수 있습니다. 이 응용 프로그램은 사설 클라우드에 호스트 되는 비 인터넷 라우팅 가능 네트워크에 액세스할 수 됩니다. 이러한 웹 응용 프로그램 모니터링 및 트래픽이 급증 하는 경우 프로그램을 공용 클라우드로 트래픽을 리디렉션하는 DNS 레코드를 수정 합니다. 트래픽 급증 하기 전에 수준으로 떨어지면 트래픽은 사설 클라우드를 다시 라우팅됩니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> - 하이브리드 연결 된 SQL Server 데이터베이스 서버를 배포 합니다.
> - 하이브리드 네트워크에 글로벌 Azure에서 웹 앱을 연결 합니다.
> - 클라우드 간 크기 조정에 대 한 DNS를 구성 합니다.
> - 클라우드 간 크기 조정에 대 한 SSL 인증서를 구성 합니다.
> - 구성 하 고 웹 응용 프로그램을 배포 합니다.
> - Traffic Manager 프로필을 만들고 클라우드 간 크기 조정에 대 한 구성 합니다.
> - Application Insights 모니터링 및 향상 된 트래픽에 대 한 경고를 설정 합니다.
> - 전역 Azure 및 Azure Stack 간에 전환 하는 자동 트래픽을 구성 합니다.

### <a name="assumptions"></a>가정

이 자습서에서는 전역 Azure 및 Azure Stack에 대 한 기본적인 지식이 있다고 가정 합니다. 에 대 한 자세한 자습서를 시작 하기 전에 하려는 경우 다음이 문서를 검토 합니다.

 - [Azure 소개](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack의 주요 개념](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

또한이 자습서는 Azure 구독이 있다고 가정 합니다. 구독자가 없는 경우 [무료 계정을 만들](https://azure.microsoft.com/free/) 시작 하기 전에 합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작 하기 전에 다음 요구 사항을 충족할 수 있는지 확인 합니다.

- Azure Stack 개발 키트 (ASDK) 또는 Azure Stack 통합 시스템에서 구독 합니다. Azure Stack 개발 키트를 배포 하려면의 지침을 따릅니다 [설치 관리자를 사용 하 여 ASDK 배포](../asdk/asdk-deploy.md)합니다.
- Azure Stack 설치에는 다음이 설치 되어 있어야 합니다.
  - Azure App Service입니다. 배포 환경에서 Azure App Service를 구성 하 여 Azure Stack 운영자를 사용 하 여 작동 합니다. 이 자습서에 App Service에는 적어도 한 개 (1) 사용할 수 있는 전용된 작업자 역할이 있어야 합니다.
  - Windows Server 2016 이미지
  - Microsoft SQL Server 이미지를 사용 하 여 Windows Server 2016
  - 적절 한 계획 및 제품은
 - 웹 응용 프로그램에 대 한 도메인 이름입니다. 도메인 이름이 없는 경우 InMotion, Bluehost, GoDaddy 등 도메인 공급자에서 구입할 수 있습니다.
- LetsEncrypt 같은 신뢰할 수 있는 인증서 기관에서 도메인에 대 한 SSL 인증서입니다.
- SQL Server 데이터베이스와 통신 하 고 Application Insights를 지 원하는 웹 응용 프로그램입니다. 다운로드할 수 있습니다 합니다 [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) GitHub에서 샘플 앱입니다.
- Azure 가상 네트워크와 Azure Stack 가상 네트워크 간 하이브리드 네트워크입니다. 자세한 지침은 [Azure 및 Azure Stack을 사용 하 여 하이브리드 클라우드 연결을 구성](azure-stack-solution-hybrid-connectivity.md)합니다.

- Azure Stack에서 빌드 에이전트를 사용 하는 하이브리드 지속적인 통합/지속적인 배포 (CI/CD) 파이프라인 개인. 자세한 내용은 참조 하세요. [Azure 및 Azure Stack 응용 프로그램을 사용 하 여 하이브리드 클라우드 id 구성](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>하이브리드 연결 된 SQL Server 데이터베이스 서버를 배포 합니다.

1. Azure Stack 사용자 포털에 로그인 합니다.

2. 에 **대시보드**를 선택 **Marketplace**합니다.

    ![Azure Stack Marketplace](media/azure-stack-solution-hybrid-cloud/image1.png)

3. **Marketplace**를 선택 **Compute**를 선택한 후 **자세한**합니다. 아래 **자세한**를 선택 합니다 **체험 용 SQL Server 라이선스: Windows Server에서 SQL Server 2017 Developer** 이미지입니다.

    ![가상 컴퓨터 이미지 선택](media/azure-stack-solution-hybrid-cloud/image2.png)

4. **무료 SQL Server 라이선스: Windows Server에서 SQL Server 2017 Developer** 선택 **만들기**합니다.

5. **기본 사항 > 기본 설정 구성**를 제공는 **이름** 가상 머신 (VM)에 대 한를 **사용자 이름** SQL Server SA에 대 한 및 **암호** SA에 대 한 합니다.  **구독** 드롭 다운 목록에서 배포 하는 구독을 선택 합니다. 에 대 한 **리소스 그룹**를 사용 하 여 **기존 항목 선택** Azure Stack 웹 앱으로 동일한 리소스 그룹에 VM을 배치 합니다.

    ![VM의 기본 설정 구성](media/azure-stack-solution-hybrid-cloud/image3.png)

6. 아래 **크기**, VM 크기를 선택 합니다. 이 자습서에서는 A2_Standard 또는 DS2_V2_Standard는 것이 좋습니다.

7. 아래 **설정 > 선택적 기능 구성**, 다음 설정을 구성 합니다.

    - **Storage 계정**. 필요한 경우 새 계정을 만듭니다.
    - **가상 네트워크**

      > [!Important]  
      > SQL Server VM에 VPN gateway와 동일한 가상 네트워크에 배포 해야 합니다.

    - **공용 IP 주소**. 기본 설정을 사용할 수 있습니다.
    - **네트워크 보안 그룹** (NSG). 새 NSG를 만듭니다.
    - **확장 및 모니터링**합니다. 기본 설정을 유지합니다.
    - **진단 저장소 계정이**입니다. 필요한 경우 새 계정을 만듭니다.
    - 선택 **확인** 여 구성을 저장 합니다.

    ![선택적 기능 구성](media/azure-stack-solution-hybrid-cloud/image4.png)

1. 아래 **SQL Server 설정을**, 다음 설정을 구성 합니다.
   - 에 대 한 **SQL 연결**선택 하 여 **공용 (인터넷)** 합니다.
   - 에 대 한 **포트**, 기본값을 유지 **1433**합니다.
   - 에 대 한 **SQL 인증**를 선택 **사용**합니다.

     > [!Note]  
     > SQL 인증을 사용 하도록 설정 하면 해당 자동-채워야에서 구성한 "SQLAdmin" 정보를 사용 하 여 **기본 사항**합니다.

   - 나머지 설정에 대 한 기본값을 그대로 유지 합니다. **확인**을 선택합니다.

    ![SQL Server 설정 구성](media/azure-stack-solution-hybrid-cloud/image5.png)

9. 온 **요약**가상 머신 구성을 검토 하 고 선택한 **확인** 배포를 시작 합니다.

    ![구성 요약](media/azure-stack-solution-hybrid-cloud/image6.png)

10. 새 VM을 만드는 데 시간이 걸립니다. Vm의 상태를 볼 수 있습니다 **가상 머신**합니다.

    ![가상 머신](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Azure 및 Azure Stack에서 웹 앱 만들기

Azure App Service를 실행 하 고 웹 응용 프로그램 관리를 간소화 합니다. Azure를 사용 하 여 일관 된 Azure Stack 이기 때문에 App Service 환경 모두에서 실행할 수 있습니다. 응용 프로그램을 호스트 하는 App Service를 사용 합니다.

### <a name="create-web-apps"></a>웹 앱 만들기

1. 지침에 따라 Azure에서 웹 앱 만들기 [Azure에서 App Service 계획 관리](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan)합니다. 하이브리드 네트워크와 동일한 구독 및 리소스 그룹에서 웹 앱을 배치 해야 합니다.

2. Azure Stack에서 (1) 이전 단계를 반복 합니다.

### <a name="add-route-for-azure-stack"></a>Azure Stack에 대 한 경로 추가

Azure Stack에서 App Service는 사용자가 응용 프로그램에 액세스할 수 있도록 공용 인터넷에서 라우팅할 수 있어야 합니다. Azure Stack에 인터넷에서 액세스할 경우 Azure Stack 웹 앱에 대 한 공용 IP 주소 또는 URL의 메모를 확인 합니다.

ASDK를 사용 하는 경우 [정적 NAT 매핑을 구성](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) 가상 환경 외부의 App Service를 노출 합니다.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>하이브리드 네트워크를 Azure에서 웹 앱 연결

Azure에서 프런트 엔드 웹 및 Azure Stack에서 SQL Server 데이터베이스 간의 연결을 제공 하려면 웹 앱 Azure 및 Azure Stack 간에 하이브리드 네트워크에 연결 합니다. 연결을 사용 하도록 설정 해야 합니다.

- 지점 및 사이트 간 연결 구성
- 웹앱 구성
- Azure Stack에서 로컬 네트워크 게이트웨이 수정 합니다.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>지점-사이트 간 연결에 대 한 Azure virtual network 구성

하이브리드 네트워크의 Azure 쪽에서 가상 네트워크 게이트웨이 Azure App Service를 사용 하 여 통합에 대 한 지점-사이트 간 연결을 허용 해야 합니다.

1. Azure에서 가상 네트워크 게이트웨이 페이지로 이동 합니다. 아래 **설정을**를 선택 **지점 및 사이트 간 구성**합니다.

    ![지점 대 사이트간 옵션](media/azure-stack-solution-hybrid-cloud/image8.png)

2. 선택 **지금 구성** 지점 및 사이트를 구성 합니다.

    ![시작 지점-사이트 간 구성](media/azure-stack-solution-hybrid-cloud/image9.png)

3. 에 **지점 및 사이트 간** 구성 페이지에서 입력에 사용 하려는 개인 IP 주소 범위 **주소 풀**합니다.

   > [!Note]  
   > 지정 하는 범위가 이미 사용 하는 전역 Azure 또는 Azure Stack 구성 요소에서 하이브리드 네트워크의 서브넷 주소 범위와 겹치지 않는지 확인 합니다.

   아래 **터널 종류**, 선택 취소 합니다 **IKEv2 VPN**합니다. 선택 **저장할** 지점-사이트 간 구성이 완료 되도록 합니다.

   ![지점 및 사이트 설정](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>하이브리드 네트워크를 사용 하 여 Azure App Service 응용 프로그램 통합

1. 응용 프로그램을 Azure VNet에 연결 하려면의 지침을 따릅니다 [VNet 통합 사용](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration)합니다.

2. 이동할 **설정을** 웹 응용 프로그램을 호스팅하는 App Service 계획에 대 한 합니다. **설정**에서 **네트워킹**을 선택합니다.

    ![네트워킹 구성](media/azure-stack-solution-hybrid-cloud/image11.png)

3. **VNET 통합**를 선택 **관리 하려면 여기를 클릭**합니다.

    ![VNET 통합 관리](media/azure-stack-solution-hybrid-cloud/image12.png)

4. 구성 하려는 VNET을 선택 합니다. 아래 **IP 주소 라우팅에 VNET**, Azure VNet, Azure Stack VNet 및 지점-사이트 간 주소 공간에 대 한 IP 주소 범위를 입력 합니다. 선택 **저장할** 의 유효성을 검사 하 여 이러한 설정을 저장 합니다.

    ![라우팅할 IP 주소 범위](media/azure-stack-solution-hybrid-cloud/image13.png)

Azure Vnet과 App Service 통합 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure Virtual Network와 앱 통합](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)합니다.

### <a name="configure-the-azure-stack-virtual-network"></a>Azure Stack virtual network 구성

Azure Stack virtual network의 로컬 네트워크 게이트웨이 App Service 지점-사이트 간 주소 범위에서 트래픽을 라우팅하도록 구성 해야 합니다.

1. Azure Stack에서로 이동 **로컬 네트워크 게이트웨이**합니다. **설정**에서 **구성**을 선택합니다.

    ![게이트웨이 구성 옵션](media/azure-stack-solution-hybrid-cloud/image14.png)

2. **주소 공간**, Azure.l 선택에서 가상 네트워크 게이트웨이에 지점-사이트 간 주소 범위를 입력 **저장** 의 유효성을 검사 하 여이 구성을 저장 합니다.

    ![지점-사이트 간 주소 공간](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>클라우드 간 크기 조정에 대 한 DNS 구성

클라우드 간 응용 프로그램에 대 한 DNS를 올바르게 구성 하면 사용자는 웹 앱의 글로벌 Azure 및 Azure Stack 인스턴스를 액세스할 수 있습니다. 이 자습서에 대 한 DNS 구성을 로드 증가 또는 감소 하는 경우 Azure Traffic Manager 트래픽을 라우팅을 수도 있습니다.

이 자습서는 Azure DNS를 사용 하 여 DNS 관리. (App Service 도메인 작동 하지 않습니다.)

### <a name="create-subdomains"></a>하위 도메인을 만들려면

Traffic Manager DNS Cname에 의존 하기 때문에 하위 도메인을 끝점에 트래픽을 라우팅할 수 필요 합니다. DNS 레코드 및 매핑되는 도메인에 대 한 자세한 내용은 참조 하세요. [Traffic Manager로 도메인 매핑](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

Azure에 대 한 끝점을 만든 하위 도메인을 사용자가 다음 사용 웹 앱에 액세스할 수 있습니다. 이 자습서에서는 사용할 수 있습니다 **app.northwind.com**, 있지만 사용자 고유의 도메인을 기반으로이 값을 사용자 지정 해야 합니다.

Azure Stack 끝점에 대 한 A 레코드를 사용 하 여 하위 도메인을 만들려고 해야 합니다. 사용할 수 있습니다 **azurestack.northwind.com**합니다.

### <a name="configure-a-custom-domain-in-azure"></a>Azure에서 사용자 지정 도메인 구성

1. 추가 된 **app.northwind.com** 하 여 Azure 웹 앱에 호스트 이름 [Azure App Service에 CNAME 매핑](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)합니다.

### <a name="configure-custom-domains-in-azure-stack"></a>Azure Stack에서 사용자 지정 도메인 구성

1. 추가 된 **azurestack.northwind.com** 하 여 Azure Stack 웹 앱에 호스트 이름 [Azure App Service에 A 레코드 매핑](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record)합니다. App Service 응용 프로그램에 대 한 인터넷 라우팅 가능 IP 주소를 사용 합니다.

2. 추가 된 **app.northwind.com** 하 여 Azure Stack 웹 앱에 호스트 이름 [Azure App Service에 CNAME 매핑](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)합니다. 에서 구성한 이전 단계 (1) 대상으로 CNAME에 대 한 호스트 이름을 사용 합니다.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>클라우드 간 크기 조정에 대 한 SSL 인증서 구성

웹 응용 프로그램에 의해 수집 된 중요 한 데이터 전송, SQL database에 안전 하 게 보호 되는지 확인 해야 합니다.

모든 들어오는 트래픽에 대 한 SSL 인증서를 사용 하 여 Azure 및 Azure Stack 웹 응용 프로그램을 구성할 수 있습니다.

### <a name="add-ssl-to-azure-and-azure-stack"></a>Azure 및 Azure SSL 추가할 스택

Azure에 SSL을 추가 합니다.

1. 얻은 SSL 인증서가 만든 하위 도메인에 대 한 유효한 지 확인 합니다. (것은 와일드 카드 인증서를 사용 하 여.)

2. 지침에 따라 Azure에서의 **웹 앱을 준비** 및 **SSL 인증서 바인딩** 부분을 [Azure Web Apps에 기존 사용자 지정 SSL 인증서 바인딩](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) 문서입니다. 선택 **SNI 기반 SSL** 으로 **SSL 유형**합니다.

3. 모든 트래픽을 HTTPS 포트로 리디렉션하십시오. 지침에 따라 합니다 **HTTPS 적용** 섹션을 [Azure Web Apps에 기존 사용자 지정 SSL 인증서 바인딩](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) 문서.

Azure Stack에 SSL을 추가 합니다.

- Azure에 사용 하는 1 ~ 3 단계를 반복 합니다.

## <a name="configure-and-deploy-the-web-application"></a>구성 하 고 웹 응용 프로그램 배포

응용 프로그램 코드를 올바른 Application Insights 인스턴스로 원격 분석을 보고를 구성 하 고 올바른 연결 문자열을 사용 하 여 웹 응용 프로그램을 구성 합니다. Application Insights에 대 한 자세한 내용은를 참조 하세요. [Application Insights 란?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Application Insights 추가

1. Microsoft Visual Studio에서 웹 응용 프로그램을 엽니다.

2. [Application Insights 추가](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry) 를 프로젝트에 Application Insights 웹 트래픽 증가 또는 감소 하는 경우 경고를 만드는 데 사용 하는 원격 분석을 전송 합니다.

### <a name="configure-dynamic-connection-strings"></a>동적 연결 문자열 구성

웹 응용 프로그램의 각 인스턴스에 다른 메서드를 사용 하 여 SQL database에 연결 됩니다. 응용 프로그램이 Azure에서 SQL Server 가상 컴퓨터 (VM)의 개인 IP 주소를 사용 하 고 Azure Stack에서 응용 프로그램을 SQL Server VM의 공용 IP 주소를 사용 합니다.

> [!Note]  
> Azure Stack 통합 시스템에서 공용 IP 주소는 인터넷 라우팅 수 없습니다. 에 Azure Stack 개발 키트 ASDK (), 공용 IP 주소는 ASDK 외부 라우팅할 수 없습니다.

응용 프로그램의 각 인스턴스에 다른 연결 문자열을 전달 하려면 App Service 환경 변수를 사용할 수 있습니다.

1. Visual Studio에서 응용 프로그램을 엽니다.

2. Startup.cs를 열고 다음 코드 블록을 찾습니다.

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Appsettings.json 파일에 정의 된 연결 문자열을 사용 하는 다음 코드를 사용 하 여 이전 코드 블록을 바꿉니다.

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>App Service 응용 프로그램 설정 구성

1. Azure 및 Azure Stack에 대 한 연결 문자열을 만듭니다. 문자열에 사용 되는 IP 주소를 제외 하 고 동일 해야 합니다.

2. Azure 및 Azure Stack에서 적절 한 연결 문자열을 추가 [응용 프로그램 설정 대로](https://docs.microsoft.com/azure/app-service/web-sites-configure) 웹 응용 프로그램에서 사용 하 여 `SQLCONNSTR\_` 이름에 접두사로 합니다.

3. **저장** 웹 앱 설정 및 응용 프로그램 다시 시작 합니다.

## <a name="enable-automatic-scaling-in-global-azure"></a>전역 Azure 자동 크기 조정을 사용 하도록 설정

App Service environment에서 웹 앱을 만들 때 인스턴스 하나를 사용 하 여 시작 합니다. 더 많은 계산 리소스가 앱에 대 한 제공 하는 인스턴스를 추가 하려면 자동으로 확장할 수 있습니다. 마찬가지로, 자동 규모 감축을 줄일 수 있습니다 인스턴스 수가 앱 요구 합니다.

> [!Note]  
> 규모 확장 및 규모 감축을 구성 하려면 App Service 계획 해야 합니다. 계획에 없는 경우 다음 단계를 시작 하기 전에 만들어야 합니다.

### <a name="enable-automatic-scale-out"></a>자동 확장을 사용 하도록 설정

1. Azure에서 확장 하 고 선택한 사이트에 대 한 App Service 계획을 찾으실 **수평 확장 (App Service 계획)** 합니다.

    ![확장](media/azure-stack-solution-hybrid-cloud/image16.png)

2. 선택 **자동 크기 조정 사용**합니다.

    ![자동 크기 조정 사용](media/azure-stack-solution-hybrid-cloud/image17.png)

3. 에 대 한 이름을 입력 **자동 크기 조정 설정 이름**합니다. 에 대 한 합니다 **기본** 자동 크기 조정 규칙을 선택 **메트릭에 따라 크기 조정**합니다. 설정 된 **인스턴스 제한** 에 **최소: 1**, **최대: 10**, 및 **기본: 1**.

    ![자동 크기 조정 구성](media/azure-stack-solution-hybrid-cloud/image18.png)

4. 선택 **규칙 추가 +** 합니다.

5. **메트릭 원본**를 선택 **현재 리소스**합니다. 규칙에 대 한 다음 조건 및 작업을 사용 합니다.

**조건**

1. 아래 **시간 집계** 선택 **평균**합니다.

2. 아래 **메트릭 이름**를 선택 **CPU 비율**합니다.

3. 아래 **연산자**를 선택 **보다 큰**입니다.

   - 설정 된 **임계값** 하 **50**합니다.
   - 설정 된 **기간** 하 **10**합니다.

**작업**

1. 아래 **작업이**를 선택 **늘릴 수**입니다.

2. 설정 된 **인스턴스 수** 하 **2**합니다.

3. 설정 된 **정지** 하 **5**합니다.

4. **추가**를 선택합니다.

5. 선택 된 **규칙 추가 +** 합니다.

6. **메트릭 원본**, 선택 **현재 리소스입니다.**

   > [!Note]  
   > 현재 리소스는 App Service 계획의 이름/GUID를 포함 하며 **리소스 종류** 및 **리소스** 드롭 다운 목록을 흐리게 표시 됩니다.

### <a name="enable-automatic-scale-in"></a>자동 크기 조정 사용

트래픽 감소 하는 경우 Azure 웹 응용 프로그램 비용을 줄이기 위해 활성 인스턴스 수를 자동으로 줄일 수 있습니다. 이 작업은 응용 프로그램 사용자에 미치는 영향을 최소화 하기 위해 확장 보다 덜 공격적입니다.

1. 로 이동 합니다 **기본** 선택 조건 확장 **규칙을 추가 하는 +** 합니다. 규칙에 대 한 다음 조건 및 작업을 사용 합니다.

**조건**

1. 아래 **시간 집계** 선택 **평균**합니다.

2. 아래 **메트릭 이름**를 선택 **CPU 비율**합니다.

3. 아래 **연산자**를 선택 **미만**합니다.

   - 설정 된 **임계값** 하 **30**합니다.
   - 설정 된 **기간** 하 **10**합니다.

**작업**

1. 아래 **작업이**를 선택 **기준으로 개수 줄이기**합니다.

   - 설정 된 **인스턴스 수** 하 **1**합니다.
   - 설정 된 **정지** 하 **5**합니다.

2. **추가**를 선택합니다.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Traffic Manager 프로필을 만들고 클라우드 간 크기 조정 구성

Traffic Manager 프로필을 Azure에서 만들고 구성한 다음 클라우드 간 확장을 사용할 수 있는 끝점입니다.

### <a name="create-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1. 선택 **리소스 만들기**
2. 선택 **네트워킹**
3. 선택 **Traffic Manager 프로필** 하 고 다음을 구성 합니다.

   - **이름을**, 프로필의 이름을 입력 합니다. 이 이름은 **해야** trafficmanager.net 영역에서 고유 해야 하 고 새 DNS 이름 (예를 들어 northwindstore.trafficmanager.net)를 만드는 데 사용 됩니다.
   - 에 대 한 **라우팅 메서드**를 선택 합니다 **가중치**합니다.
   - 에 대 한 **구독**,이 프로필에서 만들려는 구독을 선택 합니다.
   - **리소스 그룹**,이 프로필에 대 한 새 리소스 그룹을 만듭니다.
   - **리소스 그룹 위치**에서 리소스 그룹의 위치를 선택합니다. 이 설정은 리소스 그룹의 위치를 나타내며 전역적으로 배포 되는 Traffic Manager 프로필에 영향이 없습니다.

4. **만들기**를 선택합니다.

    ![Traffic Manager 프로필 만들기](media/azure-stack-solution-hybrid-cloud/image19.png)

 Traffic Manager 프로필의 전역 배포가 완료 되 면 아래에서 만든 리소스 그룹에 대 한 리소스 목록에 표시 됩니다.

### <a name="add-traffic-manager-endpoints"></a>Traffic Manager 엔드포인트 추가

1. 만든 Traffic Manager 프로필을 검색 합니다. (프로필에 대 한 리소스 그룹으로 이동한 경우 프로필 선택.)

2. **Traffic Manager 프로필**아래에 있는 **설정**를 선택 **끝점**합니다.

3. **추가**를 선택합니다.

4. **끝점 추가**, Azure Stack에 대 한 다음 설정을 사용 합니다.

   - 에 대 한 **형식**를 선택 **외부 끝점**합니다.
   - 입력 한 **이름을** 이 끝점에 대 한 합니다.
   - 에 대 한 **정규화 된 도메인 이름 (FQDN) 또는 IP** Azure Stack 웹 앱에 대 한 외부 URL을 입력 합니다.
   - 에 대 한 **가중치**, 기본값을 유지 **1**합니다. 이 가중치 정상이 면이 끝점으로 이동 하는 모든 트래픽이 발생 합니다.
   - 둡니다 **사용 안 함으로 추가** 선택 취소 합니다.

5. 선택 **확인** 여 Azure Stack 끝점을 저장 합니다.

다음 Azure 끝점을 구성할 수 있습니다.

1. 온 **Traffic Manager 프로필**를 선택 **끝점**합니다.
2. **+추가**를 선택합니다.
3. **끝점 추가**를 Azure에 대 한 다음 설정을 사용 합니다.

   - 에 대 한 **형식**를 선택 **Azure 끝점**합니다.
   - 입력 한 **이름을** 이 끝점에 대 한 합니다.
   - 에 대 한 **대상 리소스 유형**를 선택 **App Service**합니다.
   - 에 대 한 **대상 리소스**를 선택 **앱 서비스 선택** 동일한 구독에 웹 앱의 목록을 보려면.
   - **리소스**에서 첫 번째 엔드포인트로 추가할 앱 서비스를 선택합니다.
   - 에 대 한 **가중치**를 선택 **2**합니다. 이 기본 끝점을 정상 상태가 아니면 다시 트래픽이 트리거되면를 전달 하는 규칙/경고가 있는 경우이 끝점으로 이동 하는 모든 트래픽이 발생 합니다.
   - 둡니다 **사용 안 함으로 추가** 선택 취소 합니다.

4. 선택 **확인** Azure 끝점을 저장 합니다.

에 나열 된 구성 요소를 모두 끝점 **Traffic Manager 프로필** 선택 하면 **끝점**합니다. 다음 화면 캡처에 각각에 대 한 상태 및 구성 정보를 사용 하 여 두 개의 끝점을 보여 줍니다.

![엔드포인트](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Application Insights 모니터링 및 경고 설정

Azure Application Insights를 사용 하면 응용 프로그램을 모니터링 하 고 구성 하는 조건을 기반으로 경고를 보낼 수 있습니다. 몇 가지 예는: 응용 프로그램 사용할 수 없는, 오류 발생 또는 성능 문제가 표시 됩니다.

Application Insights 메트릭 경고를 만들려면 사용 합니다. 이러한 경고를 트리거할 때 인스턴스를 Azure로 확장, Azure Stack에서 자동으로 전환 되며 다시 Azure에 웹 응용 프로그램 스택 규모를 확장 합니다.

### <a name="create-an-alert-from-metrics"></a>메트릭에서 경고 만들기

이 자습서에서는 리소스 그룹으로 이동 하 고 열려는 Application Insights 인스턴스를 선택 **Application Insights**합니다.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

이 뷰를 사용 하 여 경고에서 경고 확장 및 확장을 만드는 것입니다.

### <a name="create-the-scale-out-alert"></a>경고 확장 만들기

1. 아래 **구성**를 선택 **경고 (클래식)** 합니다.
2. **메트릭 경고 추가(클래식)** 를 선택합니다.
3. **규칙 추가**, 다음을 구성 합니다.

   - 에 대 한 **이름을**를 입력 **Azure 클라우드로 버스트**합니다.
   - A **설명을** 선택 사항입니다.
   - 아래 **소스**를 **에 대 한 경고**를 선택 **메트릭**합니다.
   - 아래 **조건을**, Traffic Manager 프로필의 이름과 Traffic Manager 프로필에 대 한 리소스 그룹에 대 한 구독의 리소스를 선택 합니다.

4. 에 대 한 **메트릭을**를 선택 **요청 속도**합니다.
5. 에 대 한 **조건을**를 선택 **보다 큰**입니다.
6. 에 대 한 **임계값**를 입력 **2**합니다.
7. 에 대 한 **기간**를 선택 **지난 5 분 동안**합니다.
8. 아래 **을 통해 알림**:
   - 확인란 **전자 메일 소유자, 참가자 및 판독기**합니다.
   - 에 대 한 전자 메일 주소를 입력 **추가 관리자 email(s)** 합니다.

9. 메뉴 모음에서 선택 **저장할**합니다.

### <a name="create-the-scale-in-alert"></a>경고 확장 만들기

1. 아래 **구성**를 선택 **경고 (클래식)** 합니다.
2. **메트릭 경고 추가(클래식)** 를 선택합니다.
3. **규칙 추가**, 다음을 구성 합니다.

   - 에 대 한 **이름을**를 입력 **Azure Stack에 다시 확장**합니다.
   - A **설명을** 선택 사항입니다.
   - 아래 **소스**를 **에 대 한 경고**를 선택 **메트릭**합니다.
   - 아래 **조건을**, Traffic Manager 프로필의 이름과 Traffic Manager 프로필에 대 한 리소스 그룹에 대 한 구독의 리소스를 선택 합니다.

4. 에 대 한 **메트릭을**를 선택 **요청 속도**합니다.
5. 에 대 한 **조건을**를 선택 **미만**합니다.
6. 에 대 한 **임계값**를 입력 **2**합니다.
7. 에 대 한 **기간**를 선택 **지난 5 분 동안**합니다.
8. 아래 **을 통해 알림**:
   - 확인란 **전자 메일 소유자, 참가자 및 판독기**합니다.
   - 에 대 한 전자 메일 주소를 입력 **추가 관리자 email(s)** 합니다.

9. 메뉴 모음에서 선택 **저장할**합니다.

다음 화면 캡처는 규모 확장 및 규모 감축에 대 한 경고를 표시 합니다.

   ![경고(클래식)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Azure 및 Azure Stack 간에 트래픽 리디렉션

Azure 및 Azure Stack 간에 전환 하 여 웹 앱 트래픽 수동 또는 자동 전환을 구성할 수 있습니다.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Azure 및 Azure Stack 간에 수동 전환을 구성합니다

웹 사이트에서 구성 된 임계값에 도달 하면 알림을 받게 됩니다. 수동으로 Azure로 트래픽을 리디렉션하려면 다음 단계를 사용 합니다.

1. Azure portal에서 Traffic Manager 프로필을 선택 합니다.

    ![Traffic Manager 엔드포인트](media/azure-stack-solution-hybrid-cloud/image20.png)

2. **엔드포인트**를 선택합니다.
3. 선택 된 **Azure 끝점**합니다.
4. 아래 **상태** 를 선택 **Enabled**를 선택한 후 **저장**.

    ![Azure 끝점을 사용 하도록 설정](media/azure-stack-solution-hybrid-cloud/image23.png)

5. 온 **끝점** Traffic Manager 프로필에 대 한 선택 **외부 끝점**합니다.
6. 아래 **상태** 를 선택 **사용 안 함**를 선택한 후 **저장**합니다.

    ![Azure Stack 끝점을 사용 하지 않도록 설정](media/azure-stack-solution-hybrid-cloud/image24.png)

끝점 구성 요소를 응용 프로그램 트래픽이 Azure Stack 웹 앱 대신 Azure 스케일 아웃 웹 앱으로 이동 합니다.

 ![끝점 변경](media/azure-stack-solution-hybrid-cloud/image25.png)

Azure Stack으로 흐름을 반대로 하려면 앞의 단계를 사용 합니다.

- Azure Stack 끝점을 사용 하도록 설정
- Azure 끝점을 사용 하지 않도록 설정

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Azure 및 Azure Stack 간 자동 전환 구성

응용 프로그램에서 실행 하는 경우 Application Insights 모니터링을 사용할 수도 있습니다는 [서버 리스](https://azure.microsoft.com/overview/serverless-computing/) Azure Functions에서 제공 하는 환경입니다.

이 시나리오에서는 함수 앱을 호출 하는 webhook를 사용 하려면 Application Insights를 구성할 수 있습니다. 이 앱이 자동으로 사용 하거나 경고에 대 한 응답에서 끝점을 사용 하지 않도록 설정 합니다.

자동 트래픽 전환을 구성 하는 가이드로 다음 단계를 사용 합니다.

1. Azure 함수 앱을 만듭니다.
2. HTTP에서 트리거한 함수를 만듭니다.
3. Resource Manager, Web Apps 및 Traffic Manager에 대 한 Azure Sdk를 가져옵니다.
4. 코드를 개발 합니다.

   - Azure 구독에 인증 합니다.
   - Azure 또는 Azure Stack에 트래픽을 Traffic Manager 끝점을 설정/해제 하는 매개 변수를 사용 합니다.

5. 코드를 저장 하 고 적절 한 매개 변수를 사용 하 여 함수 앱의 URL을 추가 합니다 **웹 후크** Application Insights 경고 규칙 설정의 섹션입니다.
6. Application Insights 경고가 발생할 때 트래픽은 자동으로 리디렉션됩니다.

## <a name="next-steps"></a>다음 단계

- Azure 클라우드 패턴에 대 한 자세한 내용은 참조 하세요 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
