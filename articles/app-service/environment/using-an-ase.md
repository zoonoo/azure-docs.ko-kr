---
title: 앱 서비스 환경 사용 및 관리
description: 앱 서비스 환경에서 앱을 만들고 게시하고 확장하는 방법을 알아봅니다. 이 문서에서 모든 일반적인 작업을 찾습니다.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7ca6882aea7225dcb97c9f913267b2543de07fef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133131"
---
# <a name="use-an-app-service-environment"></a>App Service 환경 사용

ASE(앱 서비스 환경)는 고객의 Azure 가상 네트워크 인스턴스에서 서브넷에 Azure 앱 서비스를 배포하는 것입니다. ASE는 다음으로 구성됩니다.

- **프런트 엔드**: 앱 서비스 환경에서 HTTP 또는 HTTPS가 종료되는 위치입니다.
- **작업자**: 앱을 호스팅하는 리소스입니다.
- **데이터베이스**: 환경을 정의하는 정보를 보유합니다.
- **저장소**: 고객이 게시한 앱을 호스팅하는 데 사용됩니다.

앱 액세스를 위해 외부 또는 내부 가상 IP(VIP)가 있는 ASE를 배포할 수 있습니다. 외부 VIP가 있는 배포를 일반적으로 *외부 ASE라고*합니다. 내부 VIP가 있는 배포를 ILB(내부 로드 밸런서)를 사용하기 때문에 *ILB ASE라고* 합니다. ILB ASE에 대해 자세히 알아보려면 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

## <a name="create-an-app-in-an-ase"></a>ASE에서 앱 만들기

ASE에서 앱을 만들려면 일반적으로 앱을 만들 때와 동일한 프로세스를 사용하지만 몇 가지 작은 차이점이 있습니다. 새 App Service 계획을 만들 때는 다음을 수행합니다.

- 앱을 배포하기 위한 지리적 위치를 선택하는 대신 ASE를 위치로 선택합니다.
- ASE에서 만든 모든 앱 서비스 요금제는 격리된 가격 책정 계층에만 있을 수 있습니다.

ASE가 없는 경우 [앱 서비스 환경 만들기의][MakeExternalASE]지침에 따라 ASE를 만들 수 있습니다.

ASE에서 앱을 만들려면

1. **리소스** > 웹 +**모바일** > 웹**앱**만들기를 선택합니다.

1. 앱의 이름을 입력합니다. ASE에서 앱 서비스 계획을 이미 선택한 경우 앱의 도메인 이름에는 ASE의 도메인 이름이 반영됩니다.

    ![앱 이름 선택][1]

1. 구독을 선택합니다.

1. 새 리소스 그룹의 이름을 입력하거나 **기존 항목 사용**을 선택하고 드롭다운 목록에서 이름을 선택합니다.

1. OS를 선택합니다.

1. ASE에서 기존 App Service 계획을 선택하거나 다음 단계를 통해 새 App Service 계획을 만듭니다.

    a. Azure 포털 왼쪽 메뉴에서 **웹 앱에 > 리소스 만들기를**선택합니다.

    b. 구독을 선택합니다.

    다. 리소스 그룹을 선택하거나 만듭니다.

    d. 웹 앱의 이름을 입력합니다.

    e. **코드** 또는 **도커 컨테이너를**선택합니다.

    f. 런타임 스택을 선택합니다.

    g. **Linux** 또는 **Windows**를 선택합니다. 

    h. **지역** 드롭다운 목록에서 ASE를 선택합니다. 

    i. 새 앱 서비스 요금제(앱 서비스 요금제)를 선택하거나 만듭니다. 새 앱 서비스 계획을 만드는 경우 적절한 **격리된** SKU 크기를 선택합니다.

    ![격리 가격 책정 계층][2]

    > [!NOTE]
    > Linux 앱 및 Windows 앱은 동일한 앱 서비스 계획에 있을 수 없지만 동일한 앱 서비스 환경에 있을 수 있습니다.
    >

1. **검토 + 만들기를**선택하고 정보가 올바른지 확인한 다음 을 **선택합니다.**

## <a name="how-scale-works"></a>확장이 작동하는 방식

모든 App Service 앱은 App Service 계획에서 실행됩니다. App Service 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다. 앱을 확장할 때 앱 서비스 요금제와 동일한 요금제의 모든 앱도 확장됩니다.

앱 서비스 계획을 확장하면 필요한 인프라가 자동으로 추가됩니다. 인프라를 추가하는 동안 운영 규모를 확장하는 데 시간이 지연됩니다. 여러 가지 규모 작업을 순서대로 수행하는 경우 첫 번째 인프라 규모 요청이 수행되고 다른 인프라는 큐에 대기됩니다. 첫 번째 배율 작업이 완료되면 다른 인프라 요청이 모두 함께 작동합니다. 또한 인프라가 추가되면 앱 서비스 계획이 적절하게 할당됩니다. 새 앱 서비스 계획을 만드는 것은 추가 하드웨어를 요청하기 때문에 그 자체로 확장 작업입니다.

다중 테넌트 앱 서비스에서는 리소스 풀을 지원하기 위해 쉽게 사용할 수 있으므로 크기 조정이 즉시 가능합니다. ASE에는 이러한 버퍼가 없으며 필요에 따라 리소스가 할당됩니다.

ASE에서 앱 서비스 계획을 최대 100개의 인스턴스까지 확장할 수 있습니다. ASE는 해당 ASE의 모든 앱 서비스 계획에서 최대 201개의 총 인스턴스를 가질 수 있습니다.

## <a name="ip-addresses"></a>IP 주소

앱 서비스는 앱에 전용 IP 주소를 할당할 수 있습니다. 이 기능은 기존 사용자 지정 SSL 인증서를 Azure 앱 서비스에 바인딩할 때 설명한 대로 IP 기반 [SSL을 구성한][ConfigureSSL]후 사용할 수 있습니다. ILB ASE에서는 IP 기반 SSL에 사용할 IP 주소를 더 추가할 수 없습니다.

외부 ASE를 사용하면 다중 테넌트 앱 서비스와 동일한 방식으로 앱에 대한 IP 기반 SSL을 구성할 수 있습니다. ASE에는 항상 하나의 예비 주소가 있으며 최대 30개의 IP 주소가 있습니다. 하나를 사용할 때마다 다른 주소가 추가되므로 항상 주소를 쉽게 사용할 수 있습니다. 다른 IP 주소를 할당하려면 시간 지연이 필요합니다. 이 지연으로 인해 IP 주소를 빠르게 연속해서 추가할 수 없습니다.

## <a name="front-end-scaling"></a>프런트 엔드 확장

앱 서비스 계획을 확장하면 작업자가 자동으로 추가되어 지원됩니다. 모든 ASE는 프런트 엔드 2개를 포함하는 상태로 작성됩니다. 프런트 엔드는 15개의 앱 서비스 계획 인스턴스 의 모든 집합에 대해 하나의 프런트 엔드 속도로 자동으로 확장됩니다. 예를 들어 각각 5개의 인스턴스가 있는 3개의 앱 서비스 계획이 있는 경우 총 15개의 인스턴스와 3개의 프런트 엔드가 있습니다. 총 30개의 인스턴스로 확장하면 4개의 프런트 엔드가 있습니다. 이 패턴은 확장할 때 계속됩니다.

기본적으로 할당된 프런트 엔드 수는 보통 부하에 적합합니다. 5개의 인스턴스마다 비율을 하나의 프런트 엔드로 낮출 수 있습니다. 프런트 엔드의 크기를 변경할 수도 있습니다. 기본적으로 단일 코어입니다. Azure 포털에서 크기를 대신 두 개 또는 네 개의 코어로 변경할 수 있습니다.

비율 또는 프런트 엔드 크기를 변경하는 요금이 부과됩니다. 자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요. ASE의 부하 용량을 개선하려는 경우 배율 비율을 조정하기 전에 먼저 2코어 프론트 엔드로 확장하여 더 많은 개선을 얻을 수 있습니다. 프런트 엔드의 핵심 크기를 변경하면 ASE가 업그레이드되며 정규 업무 시간 이외에 수행해야 합니다.

프런트 엔드 리소스는 ASE의 HTTP/HTTPS 엔드포인트입니다. 기본 프런트 엔드 구성에서는 프런트 엔드당 메모리 사용량이 일관되게 약 60%입니다. 프런트 엔드를 확장하는 주된 이유는 주로 HTTPS 트래픽에 의해 구동되는 CPU 사용량입니다.

## <a name="app-access"></a>앱 액세스

외부 ASE에서 앱 만들기에 사용되는 도메인 접미사는 *.&lt; asename&gt;.p.azurewebsites.net*. ASE의 이름이 _외부 ase이고_ 해당 ASE에서 _contoso라는_ 앱을 호스팅하는 경우 다음 URL에서 해당 앱에 도달합니다.

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

외부 ASE를 만드는 방법에 대한 자세한 내용은 [앱 서비스 환경 만들기][MakeExternalASE]를 참조하십시오.

ILB ASE에서 앱 생성에 사용되는 도메인 접미사는 *.&lt; asename&gt;.appserviceenvironment.net*. ASE의 이름이 _ilb-ase이고_ 해당 ASE에서 _contoso라는_ 앱을 호스팅하는 경우 다음 URL에서 해당 앱에 도달합니다.

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

ILB ASE를 만드는 방법에 대한 자세한 내용은 [ILB ASE 만들기 및 사용을][MakeILBASE]참조하십시오.

SCM URL은 Kudu 콘솔에 액세스하거나 웹 배포를 사용하여 앱을 게시하는 데 사용됩니다. Kudu 콘솔에 대한 자세한 내용은 [Azure App Service용 Kudu 콘솔][Kudu]을 참조하세요. Kudu 콘솔은 디버깅, 파일 업로드, 파일 편집 등을 위한 웹 UI를 제공합니다.

## <a name="publishing"></a>게시

ASE에서 다중 테넌트 앱 서비스와 마찬가지로 다음 방법으로 게시할 수 있습니다.

- 웹 배포
- FTP
- 연속 적분(CI)
- Kudu 콘솔에서 끌어서 놓기
- 비주얼 스튜디오, 이클립스 또는 IntelliJ IDEA와 같은 IDE

외부 ASE를 사용하면 이러한 게시 옵션이 모두 동일한 방식으로 작동합니다. 자세한 내용은 [Azure App Service의 배포][AppDeploy]를 참조하세요.

게시는 ILB ASE와 크게 다르며, 게시 끝점은 모두 ILB를 통해서만 사용할 수 있습니다. ILB는 Virtual Network의 ASE 서브넷에 있는 프라이빗 IP에 있습니다. ILB에 대한 네트워크 액세스 권한이 없는 경우 해당 ASE에 앱을 게시할 수 없습니다. [ILB ASE 만들기 및 사용에서][MakeILBASE]설명한 대로 시스템의 앱에 대해 DNS를 구성해야 합니다. 이 요구 사항에는 SCM 끝점이 포함됩니다. 끝점이 제대로 정의되지 않으면 게시할 수 없습니다. IID는 ILB에 직접 게시할 수 있는 네트워크 액세스 권한이 있어야 합니다.

추가 변경 없이 GitHub 및 Azure DevOps와 같은 인터넷 기반 CI 시스템은 게시 끝점에 인터넷에 액세스할 수 없기 때문에 ILB ASE에서 작동하지 않습니다. ILB ASE를 포함하는 가상 네트워크에 자체 호스팅 릴리스 에이전트를 설치하여 Azure DevOps에서 ILB ASE에 게시를 활성화할 수 있습니다. 또는 Dropbox와 같은 끌어오기 모델을 사용하는 CI 시스템을 사용할 수도 있습니다.

ILB ASE의 앱에 대한 게시 엔드포인트에서는 ILB ASE가 만들어진 도메인을 사용합니다. 앱의 게시 프로필과 앱의 포털 창(개요 **Overview** > 필수 항목 및 속성에서도)에서 볼 **Properties**수**있습니다.**

## <a name="storage"></a>스토리지

ASE에는 ASE의 모든 앱에 대해 1TB의 스토리지가 있습니다. 격리된 가격 책정 SKU의 앱 서비스 요금제에는 기본적으로 250GB의 제한이 있습니다. 앱 서비스 계획이 5개 이상인 경우 ASE의 1TB 제한을 초과하지 않도록 주의하십시오. 하나의 앱 서비스 요금제에서 250GB 제한을 초과하는 경우 지원팀에 문의하여 앱 서비스 요금제 제한을 최대 1TB로 조정합니다. 계획 한도가 조정되더라도 ASE의 모든 앱 서비스 계획에서 여전히 1TB의 제한이 있습니다.

## <a name="logging"></a>로깅

ASE를 Azure 모니터와 통합하여 ASE에 대한 로그를 Azure 저장소, Azure 이벤트 허브 또는 로그 분석으로 보낼 수 있습니다. 이러한 항목은 오늘 기록됩니다.

| 상황 | 메시지 |
|---------|----------|
| ASE가 비정상입니다. | 잘못된 가상 네트워크 구성으로 인해 지정된 ASE가 비정상입니다. 비정상 상태가 계속되면 ASE가 일시 중단됩니다. 여기에 정의된 지침이 다음과 https://docs.microsoft.com/azure/app-service/environment/network-info같은지 확인합니다. |
| ASE 서브넷이 거의 공간이 부족합니다. | 지정된 ASE는 거의 공간이 없는 서브넷에 있습니다. 남은 {0} 주소가 있습니다. 이러한 주소가 모두 소진되면 ASE의 크기를 조정할 수 없습니다.  |
| ASE가 총 인스턴스 한도에 가까워지고 있습니다. | 지정된 ASE가 ASE의 총 인스턴스 제한에 가까워지고 있습니다. 현재 최대 {0} 201개의 인스턴스의 앱 서비스 계획 인스턴스가 포함되어 있습니다. |
| ASE가 종속성에 도달할 수 없습니다. | 지정된 ASE에 연결할 {0}수 없습니다.  여기에 정의된 지침이 다음과 https://docs.microsoft.com/azure/app-service/environment/network-info같은지 확인합니다. |
| ASE가 일시 중단됨 | 지정된 ASE가 일시 중단됩니다. ASE 정지는 계정 부족 또는 잘못된 가상 네트워크 구성으로 인한 것일 수 있습니다. 근본 원인을 해결하고 ASE가 트래픽을 계속 제공하도록 다시 시작합니다. |
| ASE 업그레이드가 시작되었습니다. | 지정된 ASE로의 플랫폼 업그레이드가 시작되었습니다. 확장 작업의 지연을 예상합니다. |
| ASE 업그레이드가 완료되었습니다. | 지정된 ASE로의 플랫폼 업그레이드가 완료되었습니다. |
| 스케일 작업이 시작되었습니다. | 앱 서비스 계획(){0}확장이 시작되었습니다. 원하는 상태 {1} {2} : 나는 노동자.
| 스케일 작업이 완료되었습니다. | 앱 서비스 계획(){0}확장이 완료되었습니다. 현재 상태 {1} {2} : 나는 노동자. |
| 스케일 작업이 실패했습니다. | 앱 서비스 계획(){0}확장에 실패했습니다. 현재 상태 {1} {2} : 나는 노동자. |

ASE에 로깅을 사용하려면 다음을 수행하십시오.

1. 포털에서 **진단 설정으로 이동합니다.**
1. **진단 설정 추가를**선택합니다.
1. 로그 통합에 대한 이름을 제공합니다.
1. 원하는 로그 대상을 선택하고 구성합니다.
1. **앱 서비스환경플랫폼로그**선택 .

![ASE 진단 로그 설정][4]

로그 애널리틱스와 통합하는 경우 ASE 포털에서 로그를 선택하고 **AppServiceEnvironmentPlatformLog에**대한 쿼리를 만들어 **로그를** 볼 수 있습니다.

## <a name="upgrade-preference"></a>업그레이드 환경 설정

여러 ASE가 있는 경우 일부 ASE를 다른 ASE보다 업그레이드할 수 있습니다. ASE **호스팅환경 리소스 관리자** 개체 내에서 업그레이드에 대한 값을 설정할 수 **있습니다.** **업그레이드기본 설정은** 템플릿, ARMClient 또는 https://resources.azure.com을 사용하여 구성할 수 있습니다. 세 가지 가능한 값은 다음과 같습니다.

- **없음**: Azure는 특정 일괄 처리없이 ASE를 업그레이드합니다. 이 값이 기본값입니다.
- **조기**: ASE는 앱 서비스 업그레이드의 상반기에 업그레이드됩니다.
- **늦은**: ASE는 앱 서비스 업그레이드의 하반기에 업그레이드됩니다.

을 사용하는 https://resources.azure.com경우 다음 단계를 수행하여 업그레이드기본 설정 값을 **설정합니다.**

1. resources.azure.com 이동하여 Azure 계정으로 로그인합니다.
1. \/\[구독 구독\]\/이름\/\[리소스그룹 리소스 그룹 이름\]\/\/공급자 Microsoft.Web\/hostingEnvironment\]\/\[ASE 이름에 대한 리소스를 이동합니다.
1. 맨 위에 있는 **읽기/쓰기를** 선택합니다.
1. **편집**을 선택합니다.
1. **업그레이드를** 원하는 세 값 중 하나에 대한 기본 설정을 설정합니다.
1. **패치를**선택합니다.

![리소스 azure com 표시][5]

"초기" ASE가 "늦은" ASE 보다 먼저 업그레이드되기 때문에 **업그레이드기본 설정** 기능이 여러 개의 ASE가 있는 경우 가장 좋습니다. 여러 개의 ASA가 있는 경우 개발 및 테스트 ASS를 "초기"로 설정하고 프로덕션 ASA를 "늦은"으로 설정해야 합니다.

## <a name="pricing"></a>가격 책정

*격리라고* 하는 SKU 의 가격 책정은 AsEs에서만 사용할 수 있습니다. ASE에서 호스팅되는 모든 앱 서비스 계획은 격리된 가격 책정 SKU에 있습니다. 앱 서비스 요금제의 격리 요금은 지역에 따라 다를 수 있습니다.

앱 서비스 요금제 의 가격 외에도 ASE 자체에 대한 고정 요금이 있습니다. 고정 요금은 ASE 크기에 따라 변경되지 않습니다. 15개의 앱 서비스 계획 인스턴스마다 하나의 추가 프런트 엔드의 기본 척도 비율로 ASE 인프라에 대한 비용을 지불합니다.

15개의 앱 서비스 계획 인스턴스마다 하나의 프런트 엔드의 기본 배율 속도가 충분히 빠르지 않은 경우 프런트 엔드가 추가되는 비율 또는 프런트 엔드의 크기를 조정할 수 있습니다. 이 비율 또는 크기를 조정할 경우 기본적으로 추가되지 않는 프런트 엔드 코어에 대해 비용을 지불하게 됩니다.

예를 들어 확장 비율을 10으로 조정하면 App Service 계획에서 인스턴스 10개마다 프런트 엔드 하나가 추가됩니다. 고정 비용을 지불하는 경우에는 인스턴스 15개마다 프런트 엔드 하나가 추가되는 확장 비율이 사용됩니다. 확장 비율이 10인 경우에는 App Service 계획 인스턴스 10개에 대해 추가되는 세 번째 프런트 엔드의 요금을 지불하게 됩니다. 인스턴스 수가 15개가 될 때는 요금을 지불하지 않아도 됩니다. 이 경우에는 프런트 엔드가 자동으로 추가되기 때문입니다.

프론트 엔드의 크기를 두 개의 코어로 조정하지만 비율을 조정하지 않으면 추가 코어에 대한 비용을 지불합니다. ASE는 두 개의 프런트 엔드로 만들어지므로 자동 크기 조정 임계값 미만이라도 크기를 2코어 프론트 엔드로 늘리면 두 개의 추가 코어에 대해 비용을 지불할 수 있습니다.

자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요.

## <a name="delete-an-ase"></a>ASE 삭제

ASE를 삭제하려면 다음을 수행합니다.

1. **앱 서비스 환경** 창 상단에서 **삭제를** 선택합니다.

1. ASE의 이름을 입력하여 ASE 삭제를 확인합니다. ASE를 삭제하면 ASE 내의 모든 콘텐츠도 삭제됩니다.

    ![ASE 삭제][3]

1. **확인**을 선택합니다.

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
