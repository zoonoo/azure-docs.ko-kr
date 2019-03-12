---
title: Azure 및 Azure Stack을 사용 하 여 지역 분산 앱 솔루션 만들기 | Microsoft Docs
description: Azure 및 Azure Stack을 사용 하 여 지역 분산 앱 솔루션을 만드는 방법에 알아봅니다.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 857aa71a4812534030ca638fd8bab11f60535ea0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536949"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>자습서: Azure 및 Azure Stack을 사용 하 여 지역 분산 앱 솔루션 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

지리적으로 분산 된 앱 패턴을 사용 하는 다양 한 메트릭을 기반으로 특정 끝점에 트래픽을 전달 하는 방법에 알아봅니다. Traffic Manager 만들기 기반 지리적 라우팅 및 끝점 구성 사용 하 여 프로필 정보 지역 요구 사항, 회사 및 국제 규정 및 데이터 요구 사항에 따라 끝점으로 라우팅되는 확인 합니다.

이 자습서에서는 샘플 환경을 빌드합니다.

> [!div class="checklist"]
> - 지리적으로 분산 된 앱을 만듭니다.
> - Traffic Manager를 사용 하 여 앱을 대상으로 합니다.

## <a name="use-the-geo-distributed-apps-pattern"></a>지리적으로 분산 된 앱 패턴 사용

지리적으로 분산 된 패턴을 사용 하 여 앱 지역에 걸쳐 있습니다. 공용 클라우드를 기본값으로 사용할 수 있습니다 하지만 사용자의 일부 데이터는 지역에 유지는 필요할 수 있습니다. 사용자 요구 사항에 따라 가장 적합 한 클라우드로 보낼 수 있습니다.

### <a name="issues-and-considerations"></a>문제 및 고려 사항

#### <a name="scalability-considerations"></a>확장성 고려 사항

이 자습서를 사용 하 여 빌드됩니다 해결책은 확장성을 수용 합니다. 그러나 다른 Azure 및 온-프레미스 기술과 솔루션을 함께 사용 하는 경우 확장성 요구를 수용할 수 있습니다. 관리자 트래픽을 통해 자동 크기 조정을 사용 하 여 하이브리드 솔루션을 만드는 방법은 [Azure를 사용 하 여 클라우드 간 크기 조정 솔루션을 만드는](azure-stack-solution-cloud-burst.md)합니다.

#### <a name="availability-considerations"></a>가용성 고려 사항

확장성 고려 하 여이 처럼이 솔루션 가용성을 직접 처리 하지 않습니다. 그러나 확장성 고려 사항에는 또한와 마찬가지로, Azure 및 온-프레미스 기술 및 솔루션을 구현할 수 있습니다 관련 된 모든 구성 요소에 대 한 고가용성 보장 하기 위해이 솔루션 내에서.

### <a name="when-to-use-this-pattern"></a>이 패턴을 사용해야 하는 경우

- 조직에 국제 분기 지역 사용자 지정 보안 및 배포 정책을 요구 합니다.

- 각 조직에서는 사무실 직원, 비즈니스 및 현지 규정 및 표준 시간대 당 활동을 보고 필요한 기능 데이터를 가져옵니다.

- 대규모를 요구하면 수평으로 앱을 확장하여 단일 지역내에서 뿐만 아니라 지역에 걸쳐 수행되는 여러 앱 배포에 맞게 큰 부하 요구 사항을 처리합니다.

### <a name="planning-the-topology"></a>토폴로지를 계획합니다.

분산된 된 앱 공간을 빌드하기 전에 도움이 될 수 있습니다 다음 기술 자료:

-   **앱의 사용자 지정 도메인:** 고객이 앱에 액세스하는 데 사용할 사용자 지정 도메인 이름은 무엇인가요? 샘플 앱에 대 한 사용자 지정 도메인 이름은 *www.scalableasedemo.com 합니다.*

-   **트래픽 관리자 도메인:** 도메인 이름을 만들 때 선택 해야는 [Azure Traffic Manager 프로필](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles)합니다. 이 이름은 *trafficmanager.net* 접미사와 결합하여 Traffic Manager에서 관리되는 도메인 항목을 등록합니다. 샘플 앱의 경우 선택한 이름은 *scalable-ase-demo*입니다. 결과적으로, Traffic Manager에서 관리 되는 전체 도메인 이름은 됩니다 *확장 가능한 ase 가리킵니다*합니다.

-   **앱 공간을 크기 조정하는 전략:** 애플리케이션 공간이 단일 Azure 지역의 여러 App Service Environment에 분산되나요? 여러 영역? 두 방법의 혼합? 결정은 고객 트래픽이 생성되는 기대치 뿐만 아니라 백 엔드 인프라를 지원하는 앱의 나머지 부분이 확장할 수 있는 방법에 기반해야 합니다. 예를 들어 100% 상태 비저장 애플리케이션의 경우 Azure 지역 마다 여러 App Service Environment의 조합을 사용하여 앱을 크게 확장할 수 있으며 여러 Azure 지역에 걸쳐 배포된 App Service Environment로 곱해집니다. 15 + 글로벌 Azure 지역에서 선택할 수 있는 고객은 전세계 하이퍼 규모의 응용 프로그램 사용 공간을 진정으로 구축할 수 있습니다. 이 문서에 사용되는 샘플 앱의 경우 세 가지 App Service Environment를 단일 Azure 지역(미국 중남부)에서 만들었습니다.

-   **App Service Environment에 대한 명명 규칙:** 각 App Service Environment에 고유한 이름이 필요합니다. 하나 또는 두 개의 App Service Environment 외 각 App Service Environment를 식별 하는 데 명명 규칙에 유용 합니다. 샘플 앱의 경우 간단한 명명 규칙을 사용했습니다. 세 가지 App Service Environment의 이름은 *fe1ase*, *fe2ase*, 및 *fe3ase*입니다.

-   **앱에 대한 명명 규칙:** 앱의 여러 인스턴스가 배포되기 때문에 배포된 앱의 인스턴스마다 이름이 필요합니다. App Service Environment를 사용 하 여 동일한 앱 이름은 여러 App Service Environment에 걸쳐 사용할 수 있습니다. 각 App Service Environment에 고유한 도메인 접미사가 있으므로 개발자는 각 환경에서 정확히 동일한 앱 이름을 다시 선택할 수 있습니다. 예를 들어, 개발자 같이 이름이 지정 된 앱을 가질 수: *myapp.foo1.p.azurewebsites.net*를 *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*등입니다. 이 시나리오에서는 앱에 대 한 각 앱 인스턴스는 고유한 이름을 갖습니다. 앱 인스턴스에 사용되는 이름은 *webfrontend1*, *webfrontend2* 및 *webfrontend3*입니다.

> [!Tip]  
> ![하이브리드 pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack은 Azure의 확장입니다. Azure Stack은 유연성 및 혁신을 온-프레미스 환경에 컴퓨팅 및 하이브리드 앱을 어디서 나 빌드 및 배포 할 수 있는 유일한 하이브리드 클라우드를 사용 하도록 설정 하는 클라우드를 제공 합니다.  
> 
> 백서 [하이브리드 응용 프로그램에 대 한 디자인 고려 사항](https://aka.ms/hybrid-cloud-applications-pillars) (배치, 확장성, 가용성, 복원 력, 관리 효율성 및 보안) 소프트웨어 품질 핵심 요소를 디자인, 배포 및 운영에 대 한 검토 하이브리드 응용 프로그램입니다. 디자인 고려 사항을 프로덕션 환경에서 문제를 최소화 되는 하이브리드 응용 프로그램 디자인을 최적화 하는 데 도움이 됩니다.

## <a name="part-1-create-a-geo-distributed-app"></a>1부: 지리적으로 분산 된 앱 만들기

이 부분에서는 웹 앱을 만들게 됩니다.

> [!div class="checklist"]
> - 웹 앱 만들기 및 게시
> - 코드를 Azure 리포지토리 추가
> - 여러 클라우드 대상에 앱 빌드를 가리킵니다.
> - 관리 하 고 CD 프로세스를 구성 합니다.

### <a name="prerequisites"></a>필수 조건

Azure 구독 및 Azure Stack 설치는 필요 합니다.

### <a name="geo-distributed-app-steps"></a>지역 분산 앱 단계

### <a name="obtain-a-custom-domain-and-configure-dns"></a>사용자 지정 도메인을 가져오고 DNS 구성

도메인에 대 한 DNS 영역 파일을 업데이트 합니다. Azure AD는 사용자 지정 도메인 이름의 소유권을 확인할 수 있습니다. 사용 하 여 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure 내에서 Azure/Office 365/외부 DNS 레코드에서 DNS 항목을 추가 하거나 [다른 DNS 등록 기관](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)합니다.

1. 공용 등록을 사용 하 여 사용자 지정 도메인을 등록 합니다.

2. 도메인에 대한 도메인 이름 등록 기관에 로그인합니다. 승인된 관리자 DNS 업데이트를 수행 해야 할 수 있습니다.

3. Azure AD가 제공한 DNS 항목을 추가 하 여 도메인에 대 한 DNS 영역 파일을 업데이트 합니다. DNS 항목은 메일 라우팅이나 웹 호스팅 등의 동작 변경 되지 않습니다.

### <a name="create-web-apps-and-publish"></a>웹 앱 만들기 및 게시

하이브리드 Azure Stack 및 Azure에 웹 앱을 배포 하 고 두 클라우드 모두에 변경 내용 푸시 auto에 CI/CD를 설정 합니다.

> [!Note]  
> 신디케이티드 실행 (Windows Server 및 SQL) 및 App Service 배포에 적절 한 이미지를 사용 하 여 azure Stack가 필요 합니다. App Service 설명서를 검토 [App Service on Azure Stack을 사용 하 여 시작 하기 전에](../azure-stack-app-service-before-you-get-started.md) Azure Stack 운영자에 대 한 섹션입니다.

#### <a name="add-code-to-azure-repos"></a>코드를 Azure 리포지토리 추가

1. Visual Studio에 로그인을 **프로젝트 만들기 권한이 있는 계정을** Azure 리포지토리에 합니다.

    하이브리드 지속적인 통합/지속적인 업데이트 (CI/CD) 응용 프로그램 코드와 인프라 코드를 적용할 수 있습니다. 사용 하 여 [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/) 모두 사설 및 호스트 된 클라우드 개발에 대 한 합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **리포지토리를 복제 합니다** 만들어 기본 웹 앱을 열어서 합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>두 클라우드 모두에서 웹 앱 배포 만들기

1.  편집 된 **WebApplication.csproj** 파일: 선택 **runtimeidentifier-** 추가한 **win10-x64**합니다. (참조 [자체 포함 배포](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 설명서입니다.)

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Azure 리포지토리 코드를 체크 인** 팀 탐색기를 사용 합니다.

2.  있는지 확인 합니다 **응용 프로그램 코드** Azure 리포지토리에 체크 인 되었습니다.

### <a name="create-the-build-definition"></a>빌드 정의 만들기

1. **Azure 파이프라인 로그인** 빌드 정의를 만들 수 있는 기능을 확인 합니다.

2. 추가 **-r win10-x64** 코드입니다. .NET Core를 사용 하 여 자체 포함된 배포를 트리거하는 데 필요한입니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image4.png)

3. **빌드 실행**합니다. 합니다 [자체 포함된 배포 빌드](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 프로세스는 Azure 및 Azure Stack에서 실행 될 수 있는 아티팩트를 게시 합니다.

**Azure 호스트 된 에이전트를 사용 하 여**

Azure 파이프라인에서 호스트 된 에이전트를 사용 하는 웹 앱 빌드 및 배포 하는 편리한 옵션입니다. 유지 관리 및 업그레이드는 Microsoft Azure에 지속적인 중단 없이 개발, 테스트 및 배포를 사용 하도록 설정 하 여 자동으로 수행 됩니다.

### <a name="manage-and-configure-the-cd-process"></a>관리 하 고 CD 프로세스를 구성 합니다.

Azure DevOps 및 Azure DevOps 서버 제공 항상 구성 및 관리 가능한 파이프라인 개발과 같은 여러 환경에 릴리스를 스테이징, QA 및 프로덕션 환경; 특정 단계에서 승인 필요를 포함 합니다.

#### <a name="create-release-definition"></a>릴리스 정의 만들기


![대체 텍스트](media/azure-stack-solution-geo-distributed/image5.png)

1.  선택 합니다 **plus** 아래에서 새 릴리스를 추가 하려면 단추를 **릴리스 탭** 빌드 및 릴리스 페이지의 온라인 VSO (Visual Studio)에서.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image6.png)

2. 적용 된 **Azure App Service 배포** 템플릿.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image7.png)

3. 추가 아티팩트 풀 다운 메뉴에서 **아티팩트 추가** Azure 클라우드 빌드 앱에 대 한 합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image8.png)

4. 파이프라인 탭을 선택 합니다 **단계를 작업** 환경에 연결 하 고 Azure 클라우드 환경 값을 설정 합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image9.png)

5. 설정 된 **환경 이름** 선택한 Azure **구독** Azure 클라우드 끝점에 대 한 합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image10.png)

6. 환경 이름 set 필수 **Azure app service 이름**합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image11.png)

7. 입력 **호스팅된 VS2017** Azure 클라우드 호스팅 환경에 대 한 에이전트 큐에서 합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image12.png)

8. Azure App Service 배포 메뉴에서 선택 된 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다. 확인을 선택 **폴더 위치**합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image13.png)

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image14.png)

9. 모든 변경 내용을 저장 하 고로 돌아가서 **릴리스 파이프라인**합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image15.png)

10. 추가 된 **새 아티팩트** Azure Stack 앱에 대 한 빌드를 선택 합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image16.png)

11. 자세한 환경 적용 한 추가 **Azure App Service 배포 합니다.**

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image17.png)

12. 새 환경 이름을 **Azure Stack.**

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image18.png)

13. Azure Stack 환경에서 찾을 **태스크** 탭 합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image19.png)

14. 선택 된 **구독** Azure Stack 끝점에 대 한 합니다.

  ![대체 텍스트](media/azure-stack-solution-geo-distributed/image20.png)

15. Azure Stack 웹 앱 이름으로 설정 합니다 **App service 이름**합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image21.png)

16. 선택 된 **Azure Stack 에이전트**합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image22.png)

17. 배포할 Azure App Service 섹션 선택 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다. 확인을 선택 **폴더 위치**합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image23.png)

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image24.png)

18. 아래 **변수** 라는 변수를 추가 하는 탭 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, 해당 값으로 설정 `true`, 범위를 지정 하 고 `Azure Stack`입니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image25.png)

19. 선택 합니다 **연속** 아티팩트와 enable 배포 트리거 아이콘 합니다 **계속** 배포 트리거.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image26.png)

20. 선택 합니다 **pre-deployment** Azure Stack 환경에서 조건 아이콘 트리거를 설정 하 고 **릴리스 후 합니다.**

21. 모든 변경 내용을 저장 합니다.

> [!Note]  
>  작업에 대 한 일부 설정을 수 자동으로 정의 된 [환경 변수](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) 템플릿에서 릴리스 정의 만들 때. 작업 설정;에서 이러한 설정은 수정할 수 없습니다. 대신 이러한 설정을 편집 하려면 부모 환경 항목을 선택 해야 합니다.

## <a name="part-2-update-web-app-options"></a>2부: 웹 앱 옵션 업데이트

[Azure App Service](https://docs.microsoft.com/azure/app-service/overview)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 

![대체 텍스트](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑
> - 사용을 * * CNAME 레코더는 **레코드** 사용자 지정 DNS 이름을 App Service에 매핑할 합니다.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑

> [!Note]  
>  모든 사용자 지정 DNS 이름에 루트 도메인을 제외 하 고 (example,northwind.com)에 대 한 CNAME을 사용 합니다.

라이브 사이트 및 해당 DNS 도메인 이름을 App Service로 마이그레이션하려면 [활성 DNS 이름을 Azure App Service로 마이그레이션](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain)을 참조하세요.

### <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

-   [App Service 앱 만들기](https://docs.microsoft.com/azure/app-service/), 또는 다른 자습서를 위해 만든 앱을 사용 합니다.

-   도메인 이름을 구입 하 고 도메인 공급자의 DNS 레지스트리에 대 한 액세스를 확인 합니다.

도메인에 대 한 DNS 영역 파일을 업데이트 합니다. Azure AD는 사용자 지정 도메인 이름의 소유권을 확인 합니다. 사용 하 여 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure 내에서 Azure/Office 365/외부 DNS 레코드에서 DNS 항목을 추가 하거나 [다른 DNS 등록 기관](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)합니다.

-   공용 등록을 사용 하 여 사용자 지정 도메인을 등록 합니다.

-   도메인에 대한 도메인 이름 등록 기관에 로그인합니다. (승인 된 관리자는 DNS 업데이트 필요할 수 있습니다.)

-   Azure AD가 제공한 DNS 항목을 추가 하 여 도메인에 대 한 DNS 영역 파일을 업데이트 합니다.

예를 들어 northwindcloud.com 및 www.northwindcloud.com에 대 한 DNS 항목을 추가 하려면 northwindcloud.com 루트 도메인에 대 한 DNS 설정을 구성 합니다.

> [!Note]  
>  사용 하 여 도메인 이름을 구입할 수는 [Azure portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain)합니다.  
> 사용자 지정 DNS 이름을 웹앱에 매핑하려면 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/)이 유료 계층(**공유**, **기본**, **표준** 또는 **프리미엄**)이어야 합니다.



### <a name="create-and-map-cname-and-a-records"></a>만들기 및 CNAME 및 A 레코드 매핑

#### <a name="access-dns-records-with-domain-provider"></a>도메인 공급자로 DNS 레코드 액세스

> [!Note]  
>  Azure DNS를 사용 하 여 Azure Web Apps에 대 한 사용자 지정 DNS 이름을 구성 합니다. 자세한 내용은 [Azure DNS를 사용하여 Azure 서비스에 대해 사용자 지정 도메인 설정 제공](https://docs.microsoft.com/azure/dns/dns-custom-domain)을 참조하세요.

1.  기본 공급자의 웹 사이트에 로그인 합니다.

2.  DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자에는 자체 DNS 레코드 인터페이스가 있습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**라는 레이블이 지정된 사이트 영역을 찾습니다.

DNS 레코드 페이지를 볼 수 있습니다 **내 도메인**합니다. 명명 된 링크를 찾습니다 **영역 파일**를 **DNS 레코드**, 또는 **고급 구성**합니다.

다음 스크린샷은 DNS 레코드 페이지의 예입니다.

![DNS 레코드 페이지 예](media/azure-stack-solution-geo-distributed/image28.png)

1.  도메인 이름 등록자에서 선택 **추가 또는 만들기** 레코드를 만듭니다. 일부 공급자에는 다른 레코드 형식을 추가하는 다양한 링크가 있습니다. 공급자의 설명서를 참조 하세요.

2.  하위 도메인을 앱의 기본 호스트 이름에 매핑할 CNAME 레코드를 추가 합니다.

  Www.northwindcloud.com 도메인 예제의 경우 이름을 매핑하는 CNAME 레코드를 추가 < 앱\_이름 >. azurewebsites.net입니다.

CNAME을 추가한 후 DNS 레코드 페이지가 다음 예제와 같이 표시 됩니다.

![Azure 앱에 대한 포털 탐색](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Azure에서 CNAME 레코드 매핑 사용

1.  새 탭에서 Azure portal에 로그인

2.  App Services로 이동 합니다.

3.  웹 앱을 선택 합니다.

4.  Azure Portal의 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다.

5.  **호스트 이름 추가** 옆에 있는 **+** 아이콘을 선택합니다.

1.  같은 정규화 된 도메인 이름을 입력 `www.northwindcloud.com`합니다.

2.  **유효성 검사**를 선택합니다.

3.  지정 된 경우, 다른 유형의 레코드를 더 추가 (`A` 또는 `TXT`) 도메인 이름 등록 기관 DNS 레코드를 합니다. Azure는 이러한 레코드의 형식 및 값을 제공 합니다.

    a.  앱의 IP 주소에 매핑할 **A** 레코드

    b.  A **TXT** 앱의 기본 호스트 이름 < app_name >에 매핑할 레코드. azurewebsites.net입니다. App Service 구성 시만이 레코드를 사용 하 여 사용자 지정 도메인 소유권을 확인 합니다. 확인 후 TXT 레코드를 삭제 합니다.

4.  도메인 등록 기관 탭에서이 작업을 완료 하 고 될 때까지 다시 유효성을 검사 합니다 **호스트 이름 추가** 단추가 활성화 됩니다.

5.  확인 * * 호스트 이름 레코드 유형이 설정 되어 **CNAME (www.example.com 또는 하위 도메인)** 합니다.

6.  **호스트 이름 추가**를 선택합니다.

7.  같은 정규화 된 도메인 이름을 입력 `northwindcloud.com`합니다.

8.  **유효성 검사**를 선택합니다.

9.  합니다 **추가** 활성화 됩니다.

10. 확인 * * 호스트 이름 레코드 유형이 설정 되어 **A 레코드 (example.com)** 합니다.

11. **호스트 이름 추가**합니다.

  새 호스트 이름 앱에 반영 되려면 약간의 시간이 걸릴 수 있습니다 **사용자 지정 도메인** 페이지입니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.
  
  ![대체 텍스트](media/azure-stack-solution-geo-distributed/image31.png) 
  
  오류 발생 시 확인 오류 알림 페이지의 맨 아래에 표시 됩니다. ![확인 오류](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  와일드 카드 도메인을 매핑할 위의 단계를 반복 될 수 있습니다 (\*. northwindcloud.com)... 따라서 각각에 대해 별도 CNAME 레코드를 만들 필요 없이이 app service에 추가 하위 도메인 추가. 이 설정을 구성 하려면 등록 기관 지침을 따릅니다.

#### <a name="test-in-a-browser"></a>브라우저에서 테스트

이전에 구성한 DNS 이름 찾습니다 (예를 들어 `northwindcloud.com`, www.northwindcloud.com 합니다.

## <a name="part-3-bind-a-custom-ssl-cert"></a>3부. 사용자 지정 SSL 인증서 바인딩

이 부분:

> [!div class="checklist"]
> - App Service에 사용자 지정 SSL 인증서 바인딩
> - 앱에 대 한 HTTPS를 적용 합니다.
> - 스크립트로 SSL 인증서 바인딩 자동화

> [!Note]  
> 필요한 경우에 고객이 Azure portal에서 SSL 인증서를 가져옵니다 하 고 웹 앱에 바인딩하십시오. [App Service 인증서 자습서](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)를 따르세요.

### <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

-   [App Service 앱 만들기](https://docs.microsoft.com/azure/app-service/)
-   [웹앱에 사용자 지정 DNS 이름 매핑](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   신뢰할 수 있는 인증 기관에서 SSL 인증서를 획득 하 고 키를 사용 하 여 요청에 서명

### <a name="requirements-for-your-ssl-certificate"></a>SSL 인증서에 대한 요구 사항

App Service에서 인증서를 사용하려면 인증서가 다음 요구 사항을 모두 충족해야 합니다.

-   신뢰할 수 있는 인증 기관에서 서명됨

-   암호로 보호된 PFX 파일로 내보냄

-   길이가 2048비트 이상인 개인 키를 포함함

-   인증서 체인의 모든 중간 인증서를 포함함

> [!Note]  
>  **Elliptic Curve Cryptography (ECC) 인증서** App Service를 사용 하 여 작동 하지만이 가이드에 포함 되지 않습니다. ECC 인증서를 만들기에 도움이 기관을 참조 하세요. 

#### <a name="prepare-the-web-app"></a>웹 앱 준비

웹 앱에 사용자 지정 SSL 인증서를 바인딩할 합니다 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/) 이어야 합니다는 **기본**, **표준**, 또는 **Premium** 계층.

#### <a name="sign-in-to-azure"></a>Azure에 로그인

1.  엽니다는 [Azure portal](https://portal.azure.com/) 웹 앱으로 이동 합니다.

2.  왼쪽된 메뉴에서 선택 **App Services**를 선택한 다음 웹 앱 이름을 선택 합니다.

![웹앱 선택](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

1.  웹 앱 페이지의 왼쪽 탐색에서 스크롤하여 합니다 **설정을** 선택한 섹션 **강화 (App Service 계획)** 합니다.

    ![강화 메뉴](media/azure-stack-solution-geo-distributed/image34.png)

1.  웹 앱에 있지 않은지 확인 합니다 **무료** 하거나 **공유** 계층입니다. 웹 앱의 현재 계층이 진한 파란색 상자로 강조 표시 됩니다.

    ![가격 책정 계층 확인](media/azure-stack-solution-geo-distributed/image35.png)

사용자 지정 SSL은 **무료** 또는 **공유** 계층에서 지원되지 않습니다. 고급, 다음 섹션의 단계에 따라 또는 **가격 책정 계층 선택** 페이지를 건너뜁니다 [업로드 및 SSL 인증서 바인딩](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)합니다.

#### <a name="scale-up-your-app-service-plan"></a>App Service 계획 강화

1.  **기본**, **표준** 또는 **프리미엄** 계층 중 하나를 선택합니다.

2.  **선택**을 선택합니다.

![가격 책정 계층 선택](media/azure-stack-solution-geo-distributed/image36.png)

알림 표시 되 면 크기 조정 작업이 완료 되었습니다.

![강화 알림](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>SSL 인증서 바인딩 및 중간 인증서 병합

여러 인증서 체인에 병합 합니다.

1. **각 인증서를 열고** 텍스트 편집기에서 수신 합니다.

2. *mergedcertificate.crt*라는 병합된 인증서의 파일을 만듭니다. 텍스트 편집기에서 각 인증서의 내용을 이 파일에 복사합니다. 사용자 인증서의 순서는 사용자의 인증서로 시작하고 루트 인증서로 끝나는 인증서 체인의 순서에 따라야 합니다. 다음 예제와 유사합니다.

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>PFX로 인증서 내보내기

인증서에서 생성 된 개인 키를 사용 하 여 병합 된 SSL 인증서를 내보냅니다.

개인 키 파일은 OpenSSL을 통해 생성 됩니다. 인증서를 PFX로 내보내려면, 자리 표시자를 대체 하 고 다음 명령을 실행 *< 개인 키 파일 >* 하 고 *< 병합 스케일 인증서 파일 >* 개인 키 경로 사용 하 여 병합 및 인증서 파일입니다.

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

메시지가 표시 되 면 App Service를 나중에 SSL 인증서를 업로드 하는 내보내기 암호를 정의 합니다.

때 IIS 또는 **Certreq.exe** 인증서 요청 생성, 로컬 컴퓨터에 인증서를 설치 하는 데 사용 되 고 [PFX 인증서를 내보내려면](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)합니다.

#### <a name="upload-the-ssl-certificate"></a>SSL 인증서 업로드

1.  선택 **SSL 설정** 웹 앱의 왼쪽된 탐색 영역에서 합니다.

2.  선택 **인증서 업로드**합니다.

3.  **PFX 인증서 파일**선택, PFX 파일입니다.

4.  4. **인증서 암호**, PFX 파일을 내보낼 때 만든 암호를 입력 합니다.

5.  **업로드**를 선택합니다.

![인증서 업로드](media/azure-stack-solution-geo-distributed/image38.png)

나타나는 App Service 인증서를 업로드 완료 되 면 합니다 **SSL 설정** 페이지입니다.

![대체 텍스트](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>SSL 인증서 바인딩

1.  에 **SSL 바인딩을** 섹션에서 **바인딩을 추가**합니다.

    > [!Note]  
    >  인증서 업로드 한 도메인 이름이에 나타나지 않으면 경우는 **Hostname** 드롭다운에서 브라우저 페이지를 새로 고쳐 보세요.

1.  에 **SSL 바인딩 추가** 페이지에서 드롭다운을 보호 하려면 도메인 이름을 선택 하 고 사용할 인증서를 사용 합니다.

2.  **SSL 유형**를 사용할 것인지 선택 [ **SNI 서버 이름 표시 ()**](https://en.wikipedia.org/wiki/Server_Name_Indication)또는 IP 기반 SSL입니다.

-   **SNI 기반 SSL**-여러 개의 SNI 기반 SSL 바인딩을 추가할 수 있습니다. 이 옵션을 사용하면 여러 SSL 인증서로 같은 IP 주소의 여러 도메인을 보호할 수 있습니다. 대부분의 최신 브라우저(Internet Explorer, Chrome, Firefox 및 Opera 포함)는 SNI를 지원합니다. [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)(서버 이름 표시)에서 더 포괄적인 브라우저 지원 정보를 찾을 수 있습니다.

-   **IP 기반 SSL**-IP 기반 SSL 바인딩 하나만 추가할 수 있습니다. 이 옵션을 사용하면 전용 공용 IP 주소를 보호하는 데 하나의 SSL 인증서만 사용할 수 있습니다. 여러 도메인을 보호 하려면 보호 동일한 SSL 인증서를 사용 하 여 모든 합니다. 이 옵션은 SSL 바인딩의 일반적인 옵션입니다.

    1.  선택 **바인딩 추가**합니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image40.png)

나타나는 App Service 인증서를 업로드 완료 되 면 합니다 **SSL 바인딩을** 섹션입니다.

![대체 텍스트](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>IP SSL에 대 한 A 레코드 다시 매핑

웹 앱에서 IP 기반 SSL을 사용 하지 않으면, 건너뜁니다 [사용자 지정 도메인에 대 한 HTTPS 테스트](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)합니다.

기본적으로 웹 앱을 공유 하는 공용 IP 주소를 사용합니다. IP 기반 SSL을 사용 하 여 인증서를 바인딩하면 App Service 웹 앱에 대 한 새롭고 전용 IP 주소를 만듭니다.

웹 앱에 A 레코드 매핑되면 전용 IP 주소로 도메인 레지스트리를 업데이트 해야 합니다.

합니다 **사용자 지정 도메인** 페이지는 새로운 전용된 IP 주소를 업데이트 합니다. 이 복사 [IP 주소](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)를 다시 매핑하려면 다음을 [레코드](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) 이 새 IP 주소에.

#### <a name="test-https"></a>HTTPS 테스트

다양 한 브라우저 https://<your.custom.domain>to로 웹 앱은 제공 했는지 확인 합니다.

![대체 텍스트](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> 인증서 유효성 검사 오류가 발생 한 경우 자체 서명 된 인증서를 원인 이거나 중간 인증서 남아 있을 수를 PFX 파일로 내보낼 때.

#### <a name="enforce-https"></a>HTTPS 적용

기본적으로 누구나 HTTP를 사용 하 여 웹 앱을 액세스할 수 있습니다. 모든 HTTP 요청을 HTTPS 포트 리디렉션될 수 있습니다.

웹 앱 페이지에서 선택 **SL 설정**합니다. 그런 다음 **HTTPS에만 해당**에서 **켜기**를 선택합니다.

![HTTPS 적용](media/azure-stack-solution-geo-distributed/image43.png)

작업이 완료 되 면 앱을 가리키는 HTTP Url 중 하나로 이동 합니다. 예: 

-   http://<app_name>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>TLS 1.1/1.2 적용

앱 허용 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 간주 되는 더 이상 보안 업계 표준에 따라 같은 기본적으로 1.0 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)합니다. 더 높은 TLS 버전을 적용하려면 다음이 단계를 수행합니다.

1.  왼쪽된 탐색 영역에서 웹 앱 페이지에서 선택 **SSL 설정**합니다.

2.  **TLS 버전**, 최소 TLS 버전을 선택 합니다.

![TLS 1.1 또는 1.2 적용](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1.  선택 **리소스 만들기** > **네트워킹** > **Traffic Manager 프로필** > **만들기**.

2.  **Traffic Manager 프로필 만들기**에 다음과 같이 입력합니다.

    1.  **이름을**, 프로필에 대 한 이름을 제공 합니다. 이 이름은 트래픽 manager.net 영역 및 DNS 이름, Traffic Manager 프로필에 액세스 하는 데 사용 되는 트래픽 manager.net 결과 내에서 고유 해야 합니다.

    2.  **라우팅 메서드**를 선택 합니다 **Geographicrouting 메서드**합니다.

    3.  **구독**,이 프로필을 만들려는 구독을 선택 합니다.

    4.  **리소스 그룹**에서 이 프로필을 배치할 새 리소스 그룹을 만듭니다.

    5.  **리소스 그룹 위치**에서 리소스 그룹의 위치를 선택합니다. 이 설정은 리소스 그룹의 위치를 나타내며 전역적으로 배포 되는 Traffic Manager 프로필에 영향이 없습니다.

    6.  **만들기**를 선택합니다.

    7.  Traffic Manager 프로필의 전역 배포가 완료 되 면 해당 리소스 그룹에 리소스의 하나로 나열 됩니다.

    ![대체 텍스트](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Traffic Manager 엔드포인트 추가

1.  포털 검색 표시줄에서 검색 된 * * Traffic Manager 프로필 * * 선택 결과에서 traffic manager 프로필에 고 이전 섹션에서 만든 이름은 표시 합니다.

2.  **Traffic Manager 프로필**를 **설정** 섹션에서 **끝점**합니다.

3.  **추가**를 선택합니다.

4.  Azure Stack 끝점을 추가합니다.

5.  에 대 한 **형식**를 선택 **외부 끝점**합니다.

6.  제공 된 **이름을** Azure Stack의 이름 이상적으로이 끝점에 대 한 합니다.

7.  정규화 된 도메인 이름 (**FQDN**), Azure Stack Web App에 대 한 사용 하 여 외부 URL입니다.

8.  지역 매핑 선택 리소스 위치한 예를 들어, 영역/대륙 **유럽입니다.**

9.  국가/지역 드롭다운 표시 되는, 예를 들어,이 끝점에 적용 되는 국가 선택 **독일**합니다.

10. **사용 안 함으로 추가**를 선택 취소 상태로 유지합니다.

11. **확인**을 선택합니다.

12. Azure 끝점을 추가 합니다.

    1.  에 대 한 **형식**를 선택 **Azure 끝점**합니다.

    2.  제공 된 **이름을** 이 끝점에 대 한 합니다.

    3.  에 대 한 **대상 리소스 유형**를 선택 **App Service**합니다.

    4.  에 대 한 **대상 리소스**를 선택 **앱 서비스 선택** 동일한 구독에서 웹 앱의 목록을 표시 합니다. **리소스**, 첫 번째 끝점으로 사용할 서비스 앱을 선택 합니다.

13. 지역 매핑 선택 리소스 위치한 예를 들어, 영역/대륙 **북아메리카/중앙 아메리카/카리브 해.**

14. 국가/지역 드롭다운 표시 되는, 아래에서이 이름을 비워둡니다 모두 위의 지역 그룹화 선택 합니다.

15. **사용 안 함으로 추가**를 선택 취소 상태로 유지합니다.

16. **확인**을 선택합니다.

  > [!Note]  
  >  리소스에 대 한 기본 끝점으로 사용할 모든 (세계) 지리적 범위를 사용 하 여 하나 이상의 끝점을 만듭니다.

1.  두 엔드포인트 추가가 완료되면 **온라인**인 모니터링 상태와 함께 **Traffic Manager 프로필**에 표시됩니다.

  ![대체 텍스트](media/azure-stack-solution-geo-distributed/image46.png)

**글로벌 엔터프라이즈 Azure 지역 배포 기능에 의존**

Azure Traffic Manager 및 지리 별 끝점을 통해 데이터 트래픽을 지역 규정 준수 및 데이터 규정을 준수 하 게 유지 하 고 원격 위치에서 로컬 비즈니스 성공에 중요 한 글로벌 기업과 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure 클라우드 패턴에 대 한 자세한 내용은 참조 하세요 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
