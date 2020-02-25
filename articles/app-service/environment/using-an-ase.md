---
title: App Service Environment 사용 및 관리
description: Azure App Service 환경에서 앱을 만들고, 게시 하 고, 크기를 조정 하는 방법입니다. 한 문서에서 일반적인 작업을 찾습니다.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565676"
---
# <a name="use-an-app-service-environment"></a>App Service Environment 사용 #

ASE (App Service Environment)는 고객의 Azure 가상 네트워크에 있는 서브넷에 Azure App Service를 배포 하는 것입니다. ASE는 다음과 같은 요소로 구성 됩니다.

- **프런트**엔드: 프런트 엔드는 App Service 환경에서 HTTP/HTTPS가 종료 되는 위치입니다.
- **작업자**: 작업자는 앱을 호스트하는 리소스입니다.
- **데이터베이스**: 데이터베이스에는 환경을 정의하는 정보가 들어 있습니다.
- **스토리지**: 스토리지는 고객이 게시한 앱을 호스트하는 데 사용됩니다.

앱 액세스를 위해 외부 또는 내부 VIP를 사용 하 여 ASE를 배포할 수 있습니다. 외부 VIP를 사용하는 배포를 대개 외부 ASE라고 합니다. 내부 버전은 내부 ILB(부하 분산 장치)를 사용하므로 ILB ASE라고 합니다. ILB ASE에 대 한 자세한 내용은 [ILB Ase 만들기 및 사용][MakeILBASE]을 참조 하세요.

## <a name="create-an-app-in-an-ase"></a>ASE에서 앱 만들기 ##

ASE에서 앱을 만들려는 경우 일반적으로 앱을 만들 때 사용하는 것과 같은 프로세스를 사용합니다. 그러나 두 프로세스 간에는 몇 가지 세부적인 차이점이 있습니다. 새 App Service 계획 (ASP)을 만드는 경우:

- 앱을 배포하기 위한 지리적 위치를 선택하는 대신 ASE를 위치로 선택합니다.
- ASE에서 만든 모든 App Service 계획은 격리 된 가격 책정 계층에만 있을 수 있습니다.

ASE가 없는 경우 [App Service 환경 만들기][MakeExternalASE]의 지침에 따라 만들 수 있습니다.

ASE에서 앱을 만들려면

1. **리소스 만들기** > **웹 + 모바일** > **웹앱**을 선택합니다.

2. 앱의 이름을 입력합니다. ASE에서 이미 App Service 계획을 선택한 경우 앱의 도메인 이름에 ASE의 도메인 이름이 반영됩니다.

    ![앱 이름 선택][1]

1. 구독을 선택합니다.

1. 새 리소스 그룹의 이름을 입력하거나 **기존 항목 사용**을 선택하고 드롭다운 목록에서 이름을 선택합니다.

1. OS를 선택합니다. 

1. ASE에서 기존 App Service 계획을 선택하거나 다음 단계를 통해 새 App Service 계획을 만듭니다.

    a. Azure Portal 왼쪽 메뉴에서 **리소스 만들기 > 웹 앱**을 선택 합니다.

    b. 구독을 선택합니다.
    
    c. 리소스 그룹을 선택 하거나 만듭니다.
    
    . 웹 앱의 이름을 제공 합니다.
    
    e. 코드 또는 DockerContainer를 선택 합니다.
    
    f. 런타임 스택을 선택 합니다.
    
    g. Linux 또는 Windows를 선택 합니다. 
    
    h. **지역** 드롭다운 목록에서 ASE를 선택 합니다. 
    
    i. 새 App Service 계획을 선택 하거나 만듭니다. 새 App Service 계획을 만드는 경우 적절 한 **격리** 된 SKU 크기를 선택 합니다.
    
    ![격리 가격 책정 계층][2]

    > [!NOTE]
    > Linux 앱과 Windows 앱이 동일한 App Service 계획에는 있을 수 없지만 동일한 App Service Environment에는 있을 수 있습니다. 
    >

2. **검토 + 만들기** 를 선택 하 고 정보가 올바르면 **만들기** 를 선택 합니다.

## <a name="how-scale-works"></a>확장이 작동하는 방식 ##

모든 App Service 앱은 App Service 계획에서 실행됩니다. App Service 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다. 앱을 확장하면 App Service 계획이 확장되므로 같은 계획 내에 있는 모든 앱이 확장됩니다.

App Service 계획의 크기를 조정 하는 경우 필요한 인프라가 자동으로 추가 됩니다. 인프라가 추가되는 동안 확장 작업의 시간이 지연됩니다. 여러 크기 조정 작업을 순서 대로 수행 하는 경우 첫 번째 인프라 규모 요청이 처리 되 고 다른 작업은 큐에 대기 됩니다. 첫 번째 크기 조정 작업이 완료 되 면 다른 인프라 요청은 모두 함께 작동 합니다. 인프라가 추가 되 면 App Service 요금제가 적절 하 게 할당 됩니다. 새 App Service 계획을 만드는 작업은 추가 하드웨어를 요청 하는 크기 조정 작업입니다. 

다중 테 넌 트 App Service에서는 리소스 풀을 쉽게 지원할 수 있기 때문에 크기 조정이 즉시 실행 됩니다. ASE에는 이러한 버퍼가 없으며 리소스는 필요 시에 할당됩니다.

ASE에서 App Service 계획을 최대 100 개의 인스턴스로 확장할 수 있습니다. ASE에는 ASE에 있는 모든 App Service 계획에 대해 최대 201 개의 인스턴스가 포함 될 수 있습니다. 

## <a name="ip-addresses"></a>IP 주소 ##

App Service에는 앱에 전용 IP 주소를 할당하는 기능이 있습니다. [기존 사용자 지정 ssl 인증서를 Azure App Service에 바인딩][ConfigureSSL]에 설명 된 대로 IP 기반 ssl을 구성한 후에는 전용 ip를 앱에 할당 하는 기능을 사용할 수 있습니다. ILB ASE에서 IP 기반 SSL에 사용할 추가 IP 주소를 추가할 수 없습니다.

외부 ASE를 사용 하 여 다중 테 넌 트 App Service에서 수행 하는 것과 동일한 방식으로 앱에 대 한 IP 기반 SSL을 구성할 수 있습니다. ASE에는 항상 최대 30 개의 IP 주소가 있습니다. IP 주소를 사용할 때마다 다른 주소가 추가되므로 주소 하나를 항상 즉시 사용할 수 있습니다. 다른 IP 주소를 할당할 때는 시간이 지연되므로, IP 주소를 연속해서 빠르게 추가할 수는 없습니다.

## <a name="front-end-scaling"></a>프런트 엔드 확장 ##

App Service 계획을 확장 하는 경우 작업자를 지원 하기 위해 자동으로 추가 됩니다. 모든 ASE는 두 개의 프런트 엔드를 사용 하 여 생성 됩니다. 프런트 엔드는 총 15 개의 App Service 계획 인스턴스에 대해 하나의 프런트 엔드 속도로 자동으로 확장 됩니다. 각각 5 개의 인스턴스를 포함 하는 3 개의 App Service 계획이 있는 경우 총 15 개의 인스턴스와 3 개의 프런트 엔드가 있습니다. 총 30 개의 인스턴스로 크기를 조정 하는 경우 4 개의 프런트 엔드가 있습니다. 

기본적으로 할당 되는 프런트 엔드 수는 보통 로드에 적합 합니다. 5 개 인스턴스 마다 하나의 프런트 엔드가 비율을 낮게 변경할 수 있습니다. 프런트 엔드의 크기를 변경할 수도 있습니다. 기본적으로 단일 코어입니다. 대신 포털의 프런트 엔드 크기를 2 또는 4 개의 코어 크기로 변경할 수 있습니다. 비율 또는 프런트 엔드 크기를 변경 하는 비용이 청구 됩니다. 자세한 내용은 [Azure App Service 가격 책정][Pricing]을 참조 하세요. ASE의 로드 용량을 개선 하려는 경우 확장 비율을 조정 하기 전에 먼저 두 코어 프런트 엔드로 크기를 조정 하 여 더 향상 된 기능을 얻을 수 있습니다. 프런트 엔드의 핵심 크기를 변경 하면 ASE가 업그레이드 되 고 정기적인 업무 시간 외에 수행 되어야 합니다.

프런트 엔드 리소스는 ASE의 HTTP/HTTPS 끝점입니다. 기본 프런트 엔드 구성에서는 프런트 엔드 당 메모리 사용량이 지속적으로 60%를 기준으로 합니다. 프런트 엔드의 크기를 조정 하는 주된 이유는 주로 HTTPS 트래픽으로 구동 되는 CPU입니다.

## <a name="app-access"></a>앱 액세스 ##

외부 ASE에서 앱을 만드는 데 사용 되는 도메인 접미사는 *&lt;asename&gt;. p.azurewebsites.net*입니다. ASE 이름이 _external ase_ 이 고 해당 ase에서 _contoso_ 라는 앱을 호스트 하는 경우 다음 url에서 해당 앱에 연결 합니다.

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

외부 ASE를 만드는 방법에 대 한 자세한 내용은 [App Service 환경 만들기][MakeExternalASE] 를 참조 하세요.

ILB ASE에서 앱을 만드는 데 사용 되는 도메인 접미사는 *&lt;asename&gt;. appserviceenvironment.net*. ASE가 _ilb-ase_ 이름이 고 해당 ase에서 _contoso_ 라는 앱을 호스트 하는 경우 다음 url에서 해당 앱에 연결 합니다.

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

ILB ASE를 만드는 방법에 대 한 자세한 내용은 [ILB Ase 만들기 및 사용][MakeILBASE]을 참조 하세요. 

Scm URL은 Kudu 콘솔에 액세스 하거나 웹 배포를 사용 하 여 앱을 게시 하는 데 사용 됩니다. Kudu 콘솔에 대 한 자세한 내용은 [Kudu console for Azure App Service][Kudu]를 참조 하세요. Kudu 콘솔은 디버깅, 파일 업로드, 파일 편집 등을 위한 웹 UI를 제공합니다.

## <a name="publishing"></a>게시 ##

다중 테넌트 App Service와 마찬가지로 ASE에서는 다음을 사용하여 게시할 수 있습니다.

- 웹 배포
- FTP
- 연속 통합
- Kudu 콘솔에서 끌어서 놓기
- IDE(예: Visual Studio, Eclipse 또는 IntelliJ IDEA)

외부 ASE에서는 이러한 게시 옵션이 모두 동일하게 동작합니다. 자세한 내용은 [Azure App Service 배포][AppDeploy]를 참조 하세요. 

게시에서 중요한 차이점은 ILB ASE와 관련된 내용입니다. ILB ASE에서 게시 엔드포인트는 모두 ILB를 통해서만 사용할 수 있습니다. ILB는 Virtual Network의 ASE 서브넷에 있는 프라이빗 IP에 있습니다. ILB에 대한 네트워크 액세스 권한이 없는 경우 해당 ASE에 앱을 게시할 수 없습니다. [ILB ASE 만들기 및 사용][MakeILBASE]에서 설명한 대로 시스템에서 앱에 대 한 DNS를 구성 해야 합니다. 여기에 SCM 엔드포인트가 포함됩니다. SCM 끝점이 올바르게 정의되어 있지 않으면 게시할 수 없습니다. 또한 직접 ILB에 게시하려면 IDE에 ILB에 대한 네트워크 액세스 권한이 있어야 합니다.

기본적으로, GitHub 및 Azure DevOps와 같은 인터넷 기반 CI 시스템은 인터넷에 액세스할 수 없기 때문에 ILB ASE와 작동 하지 않습니다. ILB ASE가 포함 된 VNet에 자체 호스팅 릴리스 에이전트를 설치 하 여 Azure DevOps에서 ILB ASE에 게시를 사용 하도록 설정할 수 있습니다. 또는 Dropbox와 같은 끌어오기 모델을 사용 하는 CI 시스템을 사용할 수도 있습니다.

ILB ASE의 앱에 대한 게시 엔드포인트에서는 ILB ASE가 만들어진 도메인을 사용합니다. 해당 도메인은 앱의 게시 프로필과 앱의 포털 블레이드(**개요** > **필수** 및 **속성**)에서 확인할 수 있습니다. 

## <a name="storage"></a>스토리지

ASE에는 ASE 내의 모든 앱에 대해 1TB의 저장소가 있습니다. 격리 된 SKU App Service 계획은 기본적으로 250 GB로 제한 됩니다. App Service 계획이 5 개 이상 있는 경우 ASE의 1TB 제한을 초과 하지 않도록 주의 해야 합니다. App Service 계획 하나에서 250 GB 이상의 제한이 필요한 경우 지원에 문의 하 여 App Service 계획 제한을 최대 1TB로 조정 합니다. 계획 제한이 조정 될 때 ASE의 모든 App Service 계획에 대해 1TB의 제한이 있습니다. 

## <a name="logging"></a>로깅 ##

ASE를 Azure Monitor와 통합 하 여 ASE에 대 한 로그를 저장소, 이벤트 허브 또는 Log Analytics에 보낼 수 있습니다. 오늘 로깅되는 항목은 다음과 같습니다.

| 적합 | 메시지 |
|---------|----------|
| ASE가 비정상입니다. | 잘못 된 가상 네트워크 구성으로 인해 지정 된 ASE가 비정상 상태입니다. 비정상 상태가 지속 되 면 ASE가 일시 중단 됩니다. 여기에 정의 된 지침을 참조 하십시오. https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 서브넷의 공간이 거의 부족 합니다. | 지정 된 ASE가 공간을 거의 벗어난 서브넷에 있습니다. {0} 남은 주소가 있습니다. 이러한 주소가 모두 사용 되 면 ASE를 확장할 수 없게 됩니다.  |
| ASE가 총 인스턴스 제한에 도달 함 | 지정 된 ASE가 ASE의 총 인스턴스 제한에 도달 했습니다. 현재 최대 201 인스턴스의 {0} App Service 계획 인스턴스를 포함 합니다. |
| ASE가 종속성에 도달할 수 없습니다. | 지정 된 ASE가 {0}에 연결할 수 없습니다.  여기에 정의 된 지침을 참조 하십시오. https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE가 일시 중지 되었습니다. | 지정한 ASE가 일시 중지 되었습니다. ASE 일시 중단은 계정 shortfall 또는 잘못 된 가상 네트워크 구성으로 인해 발생할 수 있습니다. 근본 원인을 해결 하 고 ASE를 다시 시작 하 여 트래픽을 계속 처리 합니다. |
| ASE 업그레이드가 시작 되었습니다. | 지정 된 ASE로의 플랫폼 업그레이드가 시작 되었습니다. 크기 조정 작업에서 지연 발생 |
| ASE 업그레이드가 완료 되었습니다. | 지정 된 ASE로의 플랫폼 업그레이드가 완료 되었습니다. |
| 크기 조정 작업이 시작 되었습니다. | {0}(App Service 계획)의 확장이 시작 되었습니다. 필요한 상태: {1} I{2} 작업자 
| 크기 조정 작업이 완료 되었습니다. | App Service 계획 ({0})의 확장이 완료 되었습니다. 현재 상태: {1} I{2} 작업자 |
| 크기 조정 작업이 실패 했습니다. | App Service 계획 ({0})의 크기를 조정 하지 못했습니다. 현재 상태: {1} I{2} 작업자 |

ASE에서 로깅을 사용 하도록 설정 하려면: 

1. 포털에서 진단 설정으로 이동 합니다.  
1. 진단 설정 추가를 선택 합니다.
1. 로그 통합에 대 한 이름 제공
1. 원하는 로그 대상을 확인 하 고 구성 합니다. 
1. Appservice환경 Platformlogs 확인

![ASE 진단 로그 설정][4]

Log Analytics와 통합 하는 경우 ASE 포털에서 로그를 선택 하 고 Appservice환경 Platformlogs에 대 한 쿼리를 만들어 로그를 볼 수 있습니다. 

## <a name="upgrade-preference"></a>업그레이드 기본 설정 ##

Ase 여러 개 있는 경우 다른 Ase 이전에 몇 가지를 업그레이드 하는 것이 좋습니다. ASE HostingEnvironment 리소스 관리자 개체 내에서 UpgradePreference 설정에 대 한 값을 설정할 수 있습니다. UpgradePreference 설정은 template, ARMClient 또는 https://resources.azure.com를 통해 구성할 수 있습니다.  선택할 수 있는 세 가지 값은 다음과 같습니다.

* 없음-기본값이 며 Azure에서 특정 일괄 처리 없이 ASE를 업그레이드 함을 의미 합니다.
* 초기에는 ASE가 App Service 업그레이드의 처음 절반에서 업그레이드 됨을 의미 합니다.
* 런타임에는 ASE가 App Service 업그레이드의 두 번째 절반에서 업그레이드 됨을 의미 합니다.

https://resources.azure.com를 사용 하는 경우 다음을 수행 하 여 upgradePreferences 설정 값을 설정할 수 있습니다.

1. Resources.azure.com로 이동 하 여 Azure 계정으로 로그인
1. 구독\/\[구독 이름\]\/resourceGroups\/\[\]공급자 \/\/공급자\/\/ASE 이름을 차례로 탐색 \[\]
1. 위쪽에서 읽기/쓰기를 선택 합니다.
1. 편집 선택
1. UpgradePreference 설정 값을 세 가지 선택 항목 중 원하는 항목으로 변경 합니다.
1. 패치 선택

![azure com 표시 리소스][5]

업그레이드 기본 설정 기능을 사용 하는 경우 "초기" 업그레이드 된 Ase "Ase" Ase 이전에 업그레이드 되는 경우에는 매우 적합 합니다. 여러 Ase 있는 경우 개발/테스트 Ase를 "초기"로 설정 하 고 프로덕션 Ase를 "후기"로 설정 해야 합니다.

## <a name="pricing"></a>가격 ##

**격리** 라고 하는 가격 책정 SKU는 ASE 에서만 사용할 수 있습니다. ASE에서 호스트 되는 모든 App Service 계획은 격리 가격 책정 SKU에 있습니다. 격리된 App Service 계획 요금은 지역마다 다를 수 있습니다. 

App Service 계획에 대한 가격 외에 ASE 자체에 대한 고정 요금이 있습니다. 플랫 속도는 ASE의 크기와는 변경 되지 않으며, 15 개의 App Service 계획 인스턴스 마다 하나의 추가 프런트 엔드에 대 한 기본 크기 조정 속도로 ASE 인프라를 지불 합니다.  

App Service 계획 인스턴스 15 개 마다 하나의 프런트 엔드에 대 한 기본 배율 비율이 충분히 빠르지 않을 경우 프런트 엔드가 추가 되는 비율 또는 프런트 엔드의 크기를 조정할 수 있습니다.  비율 또는 크기를 조정 하면 기본적으로 추가 되지 않는 프런트 엔드 코어에 대 한 비용을 지불 하 게 됩니다.  

예를 들어 배율 비율을 10으로 조정 하면 App Service 계획의 10 개 인스턴스 마다 프런트 엔드가 추가 됩니다. 플랫 요금은 15 개 인스턴스마다 하나의 프런트 엔드에 대 한 크기 조정 비율을 포함 합니다. 배율 비율이 10 이면 10 개의 App Service 계획 인스턴스에 대해 추가 된 세 번째 프런트 엔드에 대 한 요금이 청구 됩니다. 인스턴스 수가 15개가 될 때는 요금을 지불하지 않아도 됩니다. 이 경우에는 프런트 엔드가 자동으로 추가되기 때문입니다.

프런트 엔드의 크기를 두 코어로 조정 했지만 비율을 조정 하지 않는 경우 추가 코어에 대 한 비용을 지불 합니다.  ASE는 두 개의 프런트 엔드를 사용 하 여 생성 되므로 자동 크기 조정 임계값 보다 낮은 경우에도 두 개의 코어 프런트 엔드로 크기를 늘리면 두 개의 추가 코어에 대해 비용이 청구 됩니다.

자세한 내용은 [Azure App Service 가격 책정][Pricing]을 참조 하세요.

## <a name="delete-an-ase"></a>ASE 삭제 ##

ASE를 삭제하려면 다음을 수행합니다. 

1. **App Service Environment** 블레이드 위쪽의 **삭제**를 사용합니다. 

1. ASE의 이름을 입력하여 ASE 삭제를 확인합니다. ASE를 삭제하면 해당 ASE 내의 콘텐츠도 모두 삭제됩니다. 

    ![ASE 삭제][3]

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
