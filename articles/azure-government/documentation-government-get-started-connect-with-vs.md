---
title: "Visual Studio를 사용하여 Azure Government에 연결 | Microsoft Docs"
description: "Visual Studio와 연결하여 Azure Government에서 구독을 관리하는 방법에 대한 정보"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>Visual Studio를 통해 연결
개발자는 솔루션을 구축하는 동안 Azure 구독을 쉽게 관리하기 위해 Visual Studio를 사용합니다.  Visual Studio의 현재 사용자 인터페이스에서는 Azure Government에 대한 연결을 구성할 수 없습니다.  

### <a name="updating-visual-studio-for-azure-government"></a>Azure Government를 위한 Visual Studio 업데이트
Visual Studio에서 Azure Government에 연결할 수 있게 하려면 레지스트리를 업데이트해야 합니다.

1. Visual Studio를 닫습니다.
2. **VisualStudioForAzureGov.reg**라는 텍스트 파일을 만듭니다.
3. **VisualStudioForAzureGov.reg**에 다음 텍스트를 복사하여 붙여넣습니다.
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. 해당 파일을 저장한 다음 두 번 클릭하여 실행합니다.  파일을 레지스트리에 병합할지 묻는 메시지가 표시됩니다.
5. Visual Studio를 실행하고 [클라우드 탐색기](../vs-azure-tools-resources-managing-with-cloud-explorer.md) 사용을 시작합니다.

> [!NOTE]
> 이 레지스트리 키가 설정되면 Azure Government 구독에만 액세스할 수 있습니다.  현재 Visual Studio가 Azure 공용 대신 Azure Government에 연결되어 있기 때문에 이전에 구성했지만 작동하지 않는 구독이 계속 표시됩니다.  변경 사항을 되돌리려면 다음 섹션을 참조하세요.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Visual Studio 연결을 Azure Government로 되돌리기
Visual Studio에서 Azure 공용에 연결할 수 있게 하려면 Azure Government에 연결할 수 있는 레지스트리 설정을 제거해야 합니다.

1. Visual Studio를 닫습니다.
2. **VisualStudioForAzureGov_Remove.reg**라는 텍스트 파일을 만듭니다.
3. **VisualStudioForAzureGov_Remove.reg**에 다음 텍스트를 복사하여 붙여넣습니다.
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. 해당 파일을 저장한 다음 두 번 클릭하여 실행합니다.  파일을 레지스트리에 병합할지 묻는 메시지가 표시됩니다.
5. Visual Studio를 실행합니다.

> [!NOTE]
> 이 레지스트리 키가 되돌려지면 Azure Government 구독이 표시되지만 액세스할 수 없습니다.  그러면 해당 구독을 안전하게 제거할 수 있습니다.
> 
> 

