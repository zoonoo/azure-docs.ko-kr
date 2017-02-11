---
title: "IntelliTrace 및 Visual Studio를 사용하여 게시된 클라우드 서비스 디버깅 | Microsoft Docs"
description: "IntelliTrace 및 Visual Studio를 사용하여 게시된 클라우드 서비스 디버깅"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a7b2f3053be09e8cb6e768796b6969eba725885


---
# <a name="debugging-a-published-cloud-service-with-intellitrace-and-visual-studio"></a>IntelliTrace 및 Visual Studio를 사용하여 게시된 클라우드 서비스 디버깅
## <a name="overview"></a>개요
IntelliTrace를 사용하여 Azure에서 실행할 때 역할 인스턴스에 대한 광범위한 정보를 기록할 수 있습니다. 문제의 원인을 찾아야 하는 경우 Azure에서 실행 중인 것처럼 Visual Studio에서 코드를 단계별로 거쳐 IntelliTrace 로그를 사용할 수 있습니다. 실제로 Azure에서 Azure 응용 프로그램을 클라우드 서비스로 실행 중일 때 IntelliTrace는 키 코드 실행 및 환경 데이터를 기록하여 Visual Studio에서 기록된 데이터를 재생할 수 있습니다. 대체 방법으로 원격 디버깅을 사용하여 Azure에서 실행 중인 클라우드 서비스로 직접 연결할 수 있습니다. [클라우드 서비스 디버깅](http://go.microsoft.com/fwlink/p/?LinkId=623041)을 참조하십시오.

> [!IMPORTANT]
> IntelliTrace는 디버그 시나리오 전용이며 프로덕션 배포용으로 사용할 수 없습니다.
> 
> [!NOTE]
> Visual Studio Enterprise가 설치되어 있으며 Azure 응용 프로그램 대상 .NET Framework 4 이상 버전이 있는 경우 IntelliTrace를 사용할 수 있습니다. IntelliTrace는 Azure 역할에 대한 정보를 수집합니다. 이러한 역할에 대한 가상 컴퓨터는 항상 64비트 운영 체제를 실행합니다.
> 
> 

## <a name="to-configure-an-azure-application-for-intellitrace"></a>IntelliTrace에 대한 Azure 응용 프로그램 구성
Azure 응용 프로그램에 IntelliTrace를 사용하려면 Visual Studio Azure 프로젝트에서 응용 프로그램을 만들고 게시해야 합니다. Azure에 게시하기 전에 Azure 응용 프로그램에 대한 IntelliTrace를 구성해야 합니다. IntelliTrace를 구성하지 않고 응용 프로그램을 게시하지만 이 작업을 수행하기로 결정하는 경우 Visual Studio에서 응용 프로그램을 다시 게시해야 합니다. 자세한 내용은 [Azure Tools를 사용하여 클라우드 서비스 게시](http://go.microsoft.com/fwlink/p/?LinkId=623012)를 참조하십시오.

1. Azure 응용 프로그램을 배포할 준비가 되면 프로젝트의 빌드 대상이 **디버그**로 설정되어 있는지 확인합니다.
2. 솔루션 탐색기에서 Azure 프로젝트에 대한 바로 가기 메뉴를 열고 **게시**를 선택합니다.
   
    Publish Azure Application 대화 상자가 나타납니다.
3. 클라우드에 게시될 때 응용 프로그램에 대한 IntelliTrace 로그를 수집하려면 **IntelliTrace 사용** 확인란을 선택합니다.
   
   > [!NOTE]
   > Azure 응용 프로그램을 게시할 때 IntelliTrace 또는 프로파일링을 사용할 수 있습니다. 둘 다 사용할 수는 없습니다.
   > 
   > 
4. 기본 IntelliTrace 구성을 사용자 지정하려면 **설정** 하이퍼링크를 선택합니다.
   
    다음 그림에 표시된 것처럼 IntelliTrace 설정 대화 상자가 나타납니다. 기록할 이벤트, 호출 정보를 수집할지 여부, 로그를 수집할 모듈 및 프로세스, 기록에 할당할 공간의 크기를 지정할 수 있습니다. IntelliTrace에 대한 자세한 내용은 [IntelliTrace로 디버깅](http://go.microsoft.com/fwlink/?LinkId=214468)을 참조하세요.
   
    ![VST_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

IntelliTrace 로그는 IntelliTrace 설정에 지정된 최대 크기(기본 크기는 250MB)의 순환 로그 파일입니다. IntelliTrace 로그는 가상 컴퓨터의 파일 시스템에서 파일에 수집됩니다. 로그를 요청하는 경우 스냅숏이 해당 시점에 수행되며 로컬 컴퓨터에 다운로드됩니다.

Azure 응용 프로그램을 Azure에 게시한 후 다음 그림에 표시된 것처럼 서버 탐색기의 Azure 계산 노드에서 IntelliTrace가 활성화되었는지를 확인할 수 있습니다.

![VST_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="downloading-intellitrace-logs-for-a-role-instance"></a>역할 인스턴스에 대한 IntelliTrace 로그 다운로드
**서버 탐색기**의 **Cloud Services** 노드에서 역할 인스턴스에 대한 IntelliTrace 로그를 다운로드할 수 있습니다. 관심 있는 인스턴스를 찾을 때까지 **Cloud Services** 노드를 확장하고, 이 인스턴스에 대한 바로 가기 메뉴를 연 다음 **IntelliTrace 로그 보기**를 선택합니다. IntelliTrace 로그는 로컬 컴퓨터의 디렉터리에 있는 파일에 다운로드됩니다. IntelliTrace 로그를 요청할 때마다 새 스냅숏이 생성됩니다.

로그가 다운로드되는 경우 Visual Studio는 Azure 활동 로그 창에서 작업의 진행률을 표시합니다. 다음 그림에 표시된 것과 같이 작업에 대한 품목을 확장하여 자세한 사항을 볼 수 있습니다.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

IntelliTrace 로그를 다운로드하는 동안 Visual Studio에서 작업을 계속할 수 있습니다. 로그 다운로드가 완료되면 Visual Studio에서 자동으로 열립니다.

> [!NOTE]
> IntelliTrace 로그는 프레임워크가 생성하고 이후에 처리하는 예외를 포함할 수 있습니다. 내부 프레임워크 코드는 안전하게 무시할 수 있도록 역할을 시작할 때의 일반적인 한 부분으로 이러한 예외를 생성합니다.
> 
> 

## <a name="see-also"></a>참고 항목
[클라우드 서비스 디버깅](https://msdn.microsoft.com/library/ee405479.aspx)




<!--HONumber=Nov16_HO3-->


