---
title: "Azure Functions 런타임 설치 | Microsoft Docs"
description: "Azure Functions 런타임을 설치하는 방법"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---

# <a name="install-the-azure-functions-runtime-preview"></a>Azure Functions 런타임 미리 보기 설치

Azure Functions 런타임 미리 보기를 설치하려는 경우 다음 단계를 수행해야 합니다.

1. 컴퓨터가 최소 요구 사항을 충족하는지 확인
1. [Azure Functions 런타임 미리 보기 설치 관리자](https://aka.ms/azafr) 다운로드 
1. Azure Functions 런타임 미리 보기 설치
1. Azure Functions 런타임 미리 보기 구성 완료

## <a name="prerequisites"></a>필수 조건

Azure Functions 런타임 미리 보기를 설치하기 전에 다음이 필요합니다.

1. Microsoft Windows Server 2016 또는 Microsoft Windows 10 작성자 업데이트(Professional 또는 Enterprise Edition)를 실행하는 컴퓨터
1. 네트워크 내에서 실행되는 SQL Server 인스턴스.  최소 버전 요구 사항은 SQL Server Express입니다.

## <a name="install-the-azure-functions-runtime-preview"></a>Azure Functions 런타임 미리 보기 설치

Azure Functions 런타임 미리 보기 설치 관리자는 Azure Functions 런타임 미리 보기 관리 및 작업자 역할의 설치 과정을 안내합니다.  동일한 컴퓨터에 관리 및 작업자 역할을 설치할 수 있습니다.  그러나 함수를 더 추가하는 경우 여러 작업자에 함수를 확장하기 위해 추가 컴퓨터에 더 많은 작업자 역할을 배포해야 합니다.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>동일한 컴퓨터에 관리 및 작업자 역할 설치

1. Azure Functions 런타임 미리 보기 설치 관리자를 실행합니다.

    ![Azure Functions 런타임 미리 보기 설치 관리자][1]

1. **다음**을 클릭하여 설치 관리자의 첫 번째 단계를 진행합니다.
1. **EULA**의 약관을 읽은 후에 **확인란을 선택**하여 조건에 동의하고 **다음 클릭**하여 계속 진행합니다.
1. **함수 관리 역할** 및/또는 **함수 작업자 역할** 중에서 이 컴퓨터에 설치하려는 역할을 선택하고 **다음**을 클릭합니다.

    ![Azure Functions 런타임 미리 보기 설치 관리자 - 역할 선택][3]

    > [!NOTE]
    > 이 작업을 위해 여러 컴퓨터에 **함수 작업자 역할**을 설치하고 지침에 따른 후 설치 관리자에서 **함수 작업자 역할**만 선택합니다.

1. **다음**을 클릭하여 **Azure Functions 런타임 설치 관리자**를 통해 컴퓨터에서 설치를 진행합니다.
1. 완료되면 설치 관리자가 **Azure Functions 런타임 구성 도구**를 시작합니다.

    ![Azure Functions 런타임 미리 보기 설치 관리자 완료][5]

    > [!NOTE]
    > **Windows 10**을 설치하려고 하며 **컨테이너** 기능을 이전에 사용하도록 설정하지 않은 경우 **Azure Functions 런타임** 설치 관리자에서 설치를 완료하기 위해 컴퓨터를 다시 부팅하라는 메시지를 표시합니다.

## <a name="configure-the-azure-functions-runtime"></a>Azure Functions 런타임 구성

Azure Functions 런타임 설치를 완료하려면 구성을 완료해야 합니다.

1. **Azure Functions 런타임 구성 도구**는 컴퓨터에 설치된 역할을 보여 줍니다.

    ![Azure Functions 런타임 미리 보기 구성 도구][6]

1. **데이터베이스** 탭을 클릭하고 **SQL Server 인스턴스에 대한 연결 세부 정보**를 입력한 후 **적용**을 클릭합니다.  이 작업은 Azure Functions 런타임이 런타임을 지원할 데이터베이스를 만드는 데 필요합니다.
    
    ![Azure Functions 런타임 미리 보기 데이터베이스 구성][7]

1. **자격 증명** 탭을 클릭합니다.  이 화면에서 모든 Azure Functions를 호스트하기 위해 FileShare에서 사용할 2개의 새 자격 증명을 만들어야 합니다.  **파일 공유 소유자** 및 **파일 공유 사용자** 에 대해 **사용자 이름 및 암호** 조합을 지정하고 **적용**을 클릭합니다.

    ![Azure Functions 런타임 미리 보기 자격 증명][8]

1. **파일 공유** 탭을 클릭합니다.  이 화면에서 **파일 공유 위치**의 세부 정보를 지정해야 합니다.  이 위치가 자동으로 생성될 수도 있고 기존 파일 공유를 사용하고 **적용**을 클릭할 수도 있습니다.  새 파일 공유 위치를 선택하는 경우 Azure Functions 런타임에서 사용할 디렉터리를 지정해야 합니다.
    
    ![Azure Functions 런타임 미리 보기 파일 공유][9]

1. **IIS** 탭을 클릭합니다.  이 탭에는 Azure Functions 런타임 설치 중에 생성될 IIS의 웹 사이트 세부 정보가 표시됩니다.  **적용**을 클릭하여 완료합니다.

    ![Azure Functions 런타임 미리 보기 IIS][10]

1. **서비스** 탭을 클릭합니다.  이 탭에는 Azure Functions 런타임 설치의 서비스 상태가 표시됩니다.  초기 구성 후에 **Azure Functions 호스트 활성화 서비스**가 실행되지 않을 경우 **서비스 시작**을 클릭합니다.

    ![Azure Functions 런타임 미리 보기 구성 완료][11]

1. 마지막으로 `https://<machinename>/`으로서 **Azure Functions 런타임 포털**에 이동합니다.

    ![Azure Functions 런타임 미리 보기 포털][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
