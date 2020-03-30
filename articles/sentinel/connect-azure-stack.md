---
title: Azure 스택 가상 컴퓨터를 Azure Sentinel에 온보기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 스택 가상 컴퓨터에서 Azure 모니터, 업데이트 및 구성 관리 가상 컴퓨터 확장을 프로비전 하 고 Sentinel으로 모니터링을 시작 하는 방법을 보여 줍니다.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588521"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Azure 스택 가상 컴퓨터를 Azure Sentinel에 연결




Azure Sentinel을 사용하면 Azure 및 Azure 스택에서 실행되는 VM을 한 곳에서 모니터링할 수 있습니다. Azure 스택 컴퓨터를 Azure Sentinel에 온보킹하려면 먼저 기존 Azure 스택 가상 컴퓨터에 가상 컴퓨터 확장을 추가해야 합니다. 

Azure 스택 컴퓨터를 연결 한 후 데이터를 기반으로 통찰력을 표시 하는 대시보드갤러리에서 선택 합니다. 이러한 대시보드는 필요에 따라 쉽게 사용자 지정할 수 있습니다.



## <a name="add-the-virtual-machine-extension"></a>가상 컴퓨터 확장 추가 

Azure 스택에서 실행 중인 가상 컴퓨터에 **Azure 모니터, 업데이트 및 구성 관리** 가상 컴퓨터 확장을 추가합니다. 

1. 새 브라우저 탭에서 Azure [스택 포털에](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)로그인합니다.
2. **가상 시스템** 페이지로 이동하여 Azure Sentinel으로 보호할 가상 컴퓨터를 선택합니다. Azure 스택에서 가상 컴퓨터를 만드는 방법에 대 한 자세한 내용은 참조 [Windows 서버 VM Azure 스택 포털을 사용](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) 하 여 Windows 서버 VM 또는 Azure 스택 [포털을 사용 하 여 Linux 서버 VM 만들기.](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)
3. **확장**을 섡택합니다. 이 가상 머신에 설치된 가상 머신 확장 목록이 표시됩니다.
4. 추가 탭을 **클릭합니다.** **새 리소스** 메뉴 블레이드가 열리고 사용 가능한 가상 시스템 확장 목록을 표시합니다. 
5. Azure **모니터, 업데이트 및 구성 관리** 확장을 선택하고 **만들기를**클릭합니다. **설치 확장** 구성 창이 열립니다.

   ![Azure 모니터, 업데이트 및 구성 관리 설정](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > 마켓플레이스에 나열된 **Azure 모니터, 업데이트 및 구성 관리** 확장이 표시되지 않으면 Azure 스택 연산자에게 문의하여 사용할 수 있도록 합니다.

6. Azure Sentinel 메뉴에서 **고급** 작업 영역 **Advanced**설정 다음에 고급 작업을 선택하고 **작업 영역 ID** 및 작업 영역 **키(기본 키)를**복사합니다. 
1. Azure 스택 **설치 확장** 창에서 표시된 필드에 붙여 넣기 및 **확인을**클릭합니다.
1. 확장 설치가 완료되면 해당 상태는 **프로비저닝 성공**으로 표시됩니다. Azure Sentinel 포털에 가상 시스템이 표시되는 데 최대 1시간이 걸릴 수 있습니다.

Windows용 에이전트 설치 및 구성에 대한 자세한 내용은 [Windows 컴퓨터 연결을](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)참조하십시오.

Linux 에이전트 문제 해결 방법은 [Azure Log Analytics Linux 에이전트 문제 해결](../azure-monitor/platform/agent-linux-troubleshoot.md)을 참조하세요.

**Azure의**Azure Sentinel 포털에서 가상 시스템 아래의 상태와 함께 모든 VM 및 컴퓨터에 대한 개요가 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요 없는 경우 Azure Stack 포털을 통해 가상 머신에서 확장을 제거할 수 있습니다.

확장을 제거하려면:

1. **Azure Stack 포털**을 엽니다.
2. **가상 머신** 페이지로 이동하고 확장을 제거할 가상 머신을 선택합니다.
3. **확장**, **Microsoft.EnterpriseCloud.Monitoring** 확장을 차례로 선택합니다.
4. 설치 **를**클릭하고 선택을 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [일반적인 오류 형식 어플라이언스](connect-common-event-format.md)의 데이터를 Azure Sentinel로 스트리밍합니다.
