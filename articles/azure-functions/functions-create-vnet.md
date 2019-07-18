---
title: Azure virtual network를 사용 하 여 Azure Functions 통합
description: 함수는 Azure 가상 네트워크에 연결 하는 방법을 보여 주는 단계별 자습서
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0a31b58a3c843a2add0c84dc1a3ad4ab6417815e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612879"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>자습서: Azure virtual network를 사용 하 여 Functions 통합

이 자습서는 Azure Functions를 사용 하 여 Azure 가상 네트워크의 리소스에 연결할 방법을 보여 줍니다. 가상 네트워크에서 WordPress를 실행 하는 VM을 둘 다 인터넷에 액세스할 수 있는 함수를 만들어야 합니다.

> [!div class="checklist"]
> * 프리미엄 계획에서 함수 앱 만들기
> * 가상 네트워크의 VM에 WordPress 사이트 배포
> * 함수 앱을 가상 네트워크에 연결
> * WordPress 리소스에 액세스 하는 함수 프록시 만들기
> * 가상 네트워크 내에서 WordPress 파일 요청

> [!NOTE]  
> 이 자습서에서는 프리미엄 요금제에서 함수 앱을 만듭니다. 이 호스팅 요금제는 현재 미리 보기로 제공에서 됩니다. 자세한 내용은 [프리미엄 요금제]합니다.

## <a name="topology"></a>토폴로지

다음 다이어그램은 사용자가 만든 솔루션의 아키텍처를 보여줍니다.

 ![가상 네트워크 통합을 위한 UI](./media/functions-create-vnet/topology.png)

VNet 통합 기능을 포함 하는 Azure App Service에서 웹 앱과 동일한 호스팅 기능이 제공 하는 프리미엄 계획으로 실행 하는 함수입니다. 참조 문제 해결 및 고급 구성을 포함 하 여 VNet 통합에 자세히 알아보려면 [Azure virtual network와 앱 통합](../app-service/web-sites-integrate-with-vnet.md)합니다.

## <a name="prerequisites"></a>전제 조건

이 자습서에서는 IP 주소 및 서브넷 지정을 파악 하는 것입니다. 시작할 수 있습니다 [주소 지정 및 서브넷의 기본 사항을 다루는이 문서에서는](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)합니다. 많은 자세한 문서 및 비디오 온라인으로 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="create-a-function-app-in-a-premium-plan"></a>프리미엄 계획에서 함수 앱 만들기

함수 앱을 먼저 만듭니다는 [프리미엄 요금제]합니다. 이 계획 하는 동안 가상 네트워크 통합을 지 원하는 서버 리스 확장을 제공 합니다.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

오른쪽 위 모서리에서 고정 아이콘을 선택 하 여 함수 앱을 대시보드에 고정할 수 있습니다. 고정 쉽게 VM을 만든 후이 함수 앱으로 돌아갑니다.

## <a name="create-a-vm-inside-a-virtual-network"></a>가상 네트워크 내에서 VM 만들기

다음으로 가상 네트워크 내에서 WordPress를 실행 하는 미리 구성 된 VM을 만듭니다 ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) Jetware에서). WordPress VM의 저렴 한 비용 및 편의 때문에 사용 됩니다. 이와 동일한 시나리오는 REST Api, App Service Environment 및 기타 Azure 서비스와 같은 가상 네트워크에서 모든 리소스를 사용 하 여 작동합니다. 

1. 포털에서 선택 **+ 리소스 만들기** 검색 필드 형식에서 왼쪽된 탐색 창에서 `WordPress LEMP7 Max Performance`, Enter 키를 누릅니다.

1. 선택할 **Wordpress LEMP Max Performance** 검색 결과에서. 소프트웨어 플랜 선택 **CentOS에 대 한 Wordpress LEMP Max Performance** 으로 **소프트웨어 계획** 선택한 **만들기**합니다.

1. 에 **기본 사항** 탭에서 이미지 아래의 표에 지정 된 대로 VM 설정을 사용 합니다.

    ![VM을 만들기 위한 기본 사항 탭](./media/functions-create-vnet/create-vm-1.png)

    | 설정      | 제안된 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | 선택 `myResourceGroup`, 또는 함수 앱을 사용 하 여 만든 리소스 그룹입니다. 호스팅 계획 및 함수 앱의 경우 WordPress VM을 동일한 리소스 그룹을 사용 하 여 쉽게이 자습서를 사용 하 여 완료 되 면 리소스를 정리할 수 있습니다. |
    | **가상 머신 이름** | VNET-Wordpress | VM 이름을 리소스 그룹에서 고유 해야 합니다. |
    | **[Region](https://azure.microsoft.com/regions/)** | (유럽) 서유럽 | VM에 액세스 하는 함수를 거의 또는 가까운 지역을 선택 합니다. |
    | **Size** | B1s | 선택할 **크기를 변경** 고 1 개의 vCPU 및 1GB의 메모리가 있는 B1s 표준 이미지를 선택 합니다. |
    | **인증 유형** | 암호 | 암호 인증을 사용 하려면 지정 해야는 **사용자 이름**, 보안 **암호**를 차례로 **암호 확인**합니다. 이 자습서에서는 문제를 해결 하려면 필요한 경우가 아니면 VM에 로그인 할 필요가 없습니다. |

1. 선택 된 **네트워킹** 탭 하 고 가상 네트워크 구성에서 선택 **새로 만들기**합니다.

1. **가상 네트워크 만들기**, 이미지 아래 표의 설정을 사용 합니다.

    ![네트워킹 탭에는 VM 만들기](./media/functions-create-vnet/create-vm-2.png)

    | 설정      | 제안된 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **이름** | myResourceGroup-vnet | 가상 네트워크에 대해 생성 된 기본 이름을 사용할 수 있습니다. |
    | **주소 범위** | 10.10.0.0/16 | 가상 네트워크에 대 한 단일 주소 범위를 사용 합니다. |
    | **서브넷 이름** | 자습서-Net | 서브넷의 이름입니다. |
    | **주소 범위** (서브넷) | 10.10.1.0/24   | 서브넷 크기를 얼마나 많은 인터페이스는 서브넷에 추가할 수를 정의 합니다. 이 서브넷은 WordPress 사이트에서 사용 됩니다.  `/24` 서브넷 254 호스트 주소를 제공 합니다. |

1. 선택 **확인** 가상 네트워크를 만듭니다.

1. 에 **네트워킹** 탭을 선택 **None** 에 대 한 **공용 IP**입니다.

1. 선택 합니다 **관리** 탭에서 **진단 저장소 계정**, 함수 앱을 사용 하 여 만든 저장소 계정을 선택 합니다.

1. **검토 + 만들기**를 선택합니다. 유효성 검사에는 다음이 완료 되 면 선택 **만들기**합니다. VM 만들기 프로세스는 몇 분 정도 걸립니다. 만든된 VM을 가상 네트워크만 액세스할 수 있습니다.

1. VM을 만든 후에 선택할 **리소스로 이동** 새 VM에 대 한 페이지를 보려면 선택한 **네트워킹** 아래의 **설정**합니다.

1. 확인 되지 않습니다 **공용 IP**입니다. 메모를 **개인 IP**, 함수 앱에서 VM에 연결 하는 데 사용할 수 있는 합니다.

    ![VM에 대 한 네트워킹 설정](./media/functions-create-vnet/vm-networking.png)

이제 WordPress 사이트 가상 네트워크 내에 완전히 배포 해야 합니다. 이 사이트에 공용 인터넷에서 액세스할 수 없습니다.

## <a name="connect-your-function-app-to-the-virtual-network"></a>함수 앱을 가상 네트워크에 연결

가상 네트워크의 VM에서 실행 하는 WordPress 사이트를 사용 하 여 해당 가상 네트워크에 함수 앱을 이제 연결할 수 있습니다.

1. 새 함수 앱 선택 **플랫폼 기능** > **네트워킹**합니다.

    ![함수 앱에서 네트워킹을 선택 합니다.](./media/functions-create-vnet/networking-0.png)

1. 아래 **VNet 통합**를 선택 **구성 하려면 여기를 클릭 하십시오.** 합니다.

    ![네트워크 기능을 구성 하는 것에 대 한 상태](./media/functions-create-vnet/Networking-1.png)

1. 가상 네트워크 통합 페이지에서 선택 **VNet 추가 (미리 보기)** 합니다.

    ![VNet 통합 미리 보기 추가](./media/functions-create-vnet/networking-2.png)

1. **네트워크 기능 상태**, 이미지 아래 표의 설정을 사용 합니다.

    ![함수 앱 가상 네트워크를 정의 합니다.](./media/functions-create-vnet/networking-3.png)

    | 설정      | 제안된 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | 이 가상 네트워크는 이전에 만든 것입니다. |
    | **서브넷** | 새 서브넷 만들기 | 사용 하 여 함수 앱에 대 한 가상 네트워크에 서브넷을 만듭니다. VNet 통합은 빈 서브넷을 사용 하도록 구성 되어야 합니다. 함수 다른 서브넷에 VM 사용 하는 중요 하지 않습니다. 가상 네트워크에는 자동으로 두 서브넷 간에 트래픽을 라우팅합니다. |
    | **서브넷 이름** | Net 함수 | 새 서브넷의 이름입니다. |
    | **Virtual network 주소 블록** | 10.10.0.0/16 | WordPress 사이트에서 사용 하 여 동일한 주소 블록을 선택 합니다. 정의 된 하나의 주소 블록을 하나만 있으면 됩니다. |
    | **주소 범위** | 10.10.2.0/24   | 서브넷 크기를 프리미엄 계획 함수 앱을 확장할 수 있는 인스턴스의 총 수를 제한 합니다. 이 예제에서는 `/24` 254 사용 가능한 호스트 주소를 사용 하 여 서브넷입니다. 이 서브넷은 오버 프로 비전 하지만 쉽게 계산할 수입니다. |

1. 선택 **확인** 서브넷을 추가 합니다. 함수 앱 페이지를 반환 하는 VNet 통합 및 네트워크 기능 상태 페이지를 닫습니다.

함수 앱에서 WordPress 사이트가 실행 되 고 있는 가상 네트워크를 액세스할 수 있습니다. 다음을 사용 하 여 [Azure Functions 프록시](functions-proxies.md) WordPress 사이트에서 파일을 반환 합니다.

## <a name="create-a-proxy-to-access-vm-resources"></a>VM 리소스에 액세스 하려면 프록시 만들기

사용 하도록 설정 하는 VNet 통합을 사용 하 여 가상 네트워크에서 실행 중인 VM에 요청을 전달 하도록 함수 앱의 프록시를 만들 수 있습니다.

1. 함수 앱 선택 **프록시** >  **+** , 다음 이미지 아래의 표에 프록시 설정을 사용 합니다.

    ![프록시 설정을 정의 합니다.](./media/functions-create-vnet/create-proxy.png)

    | 설정  | 제안된 값  | 설명      |
    | -------- | ---------------- | ---------------- |
    | **이름** | PlAnT | 이름을 임의의 값일 수 있습니다. 프록시를 식별 하는 것이 됩니다. |
    | **경로 템플릿** | /plant | VM 리소스에 매핑되는 경로입니다. |
    | **백 엔드 URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | 대체 `<YOUR_VM_IP>` 앞서 만든 WordPress VM의 IP 주소를 사용 하 여 합니다. 이 매핑은 사이트에서 단일 파일을 반환합니다. |

1. 선택 **만들기** 함수 앱으로 프록시를 추가 합니다.

## <a name="try-it-out"></a>체험

1. 브라우저에서으로 사용 하는 URL에 액세스 하려고 시도 합니다 **백 엔드 URL**합니다. 예상 대로 요청 시간이 초과 됩니다. 제한 시간을에 WordPress 사이트는 가상 네트워크 및 인터넷이 아닌 경우에 연결 되어 발생 합니다.

1. 복사 합니다 **프록시 URL** 값에서 새 프록시를 브라우저의 주소 표시줄에 붙여 넣습니다. 가상 네트워크 내에서 실행 되는 WordPress 사이트에서 반환 된 이미지가입니다.

    ![WordPress 사이트에서 반환 하는 공장 이미지 파일](./media/functions-create-vnet/plant.png)

함수 앱은 인터넷 및 가상 네트워크에 연결 됩니다. 프록시는 공용 인터넷을 통해 요청을 받습니다 이며 다음 연결 된 가상 네트워크에 해당 요청을 전달 하려면 간단한 HTTP 프록시로 작동 합니다. 프록시 릴레이 합니다 다시 응답 공개적으로 인터넷을 통해.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 WordPress 사이트 프록시를 사용 하 여 함수 앱에 의해 호출 되는 API로 사용 됩니다. 이 시나리오는 쉽게 설정 하 고 시각화할 수 있기 때문에 유용한 자습서를 만듭니다. 가상 네트워크 내에 배포 된 다른 API를 사용할 수 있습니다. 또한 만들 수는 함수 가상 네트워크 내에 배포 된 Api를 호출 하는 코드를 사용 하 여 합니다. 보다 현실적인 시나리오는 데이터 클라이언트 Api를 사용 하 여 가상 네트워크에 배포 된 SQL Server 인스턴스를 호출 하는 함수입니다.

프리미엄 계획에서 실행 되는 functions PremiumV2 계획에서 웹 앱으로 동일한 기본 App Service 인프라를 공유 합니다. 에 대 한 모든 설명서 [Azure App Service의 웹 앱](../app-service/overview.md) Premium 계획 함수에 적용 됩니다.

> [!div class="nextstepaction"]
> [함수에서 네트워킹 옵션에 자세히 알아보기](./functions-networking-options.md)

[프리미엄 요금제]: functions-scale.md#premium-plan
