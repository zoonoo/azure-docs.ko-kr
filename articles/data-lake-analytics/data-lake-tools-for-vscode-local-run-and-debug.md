---
title: "Azure Data Lake Tools - Visual Studio Code를 사용한 U-SQL 로컬 실행 및 로컬 디버그 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio Code를 사용하여 로컬 실행 및 로컬 디버그하는 방법을 알아봅니다."
Keywords: "VScode,Azure Data Lake Tools,로컬 실행,로컬 디버그,로컬 디버그,저장소 파일 미리 보기,저장소 경로로 업로드"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Visual Studio Code로 U-SQL 로컬 실행 및 로컬 디버그

## <a name="prerequisites"></a>필수 조건
- Azure Data Lake Tool for Visual Studio Code. 자세한 내용은 [Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)을 참조하세요.
- C# for Visual Studio Code(U-SQL 로컬 디버그를 수행하려는 경우)

   ![Data Lake Tools for Visual Studio Code가 vscodeCsharp 설치](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > U-SQL 로컬 실행 및 디버그 기능은 현재 Windows 사용자만 지원합니다. 


## <a name="set-up-u-sql-local-run-environment"></a>U-SQL 로컬 실행 환경 설정

1. **Ctrl+Shift+P**를 눌러 명령 팔레트를 열고 **ADL: Download LocalRun Dependency**를 입력하여 패키지를 다운로드합니다.  

   ![DownloadLocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. 아래의 출력 패널에 표시된 경로에서 종속성 패키지를 찾은 다음 BuildTools를 설치하고 Win10SDK 10240을 설치합니다. 예:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- BuildTools 설치 – 마법사의 지시에 따라 설치합니다.   

  ![InstallBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- Win10SDK 10240 설치 - 설치 지침에 따라 설치합니다.  

  ![InstallWin10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. 환경 변수 설정 - **SCOPE_CPP_SDK** 환경 변수를 다음으로 설정합니다.  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
환경 변수 설정을 적용하려면 운영 체제를 다시 시작해야 합니다.  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>로컬 실행 서비스 시작 및 로컬 계정에 U-SQL 작업 제출 
처음 사용하는 경우 아직 입력하지 않았으면 **ADL: Download Localrun Dependency** 패키지를 입력하라는 메시지가 표시됩니다.
1. **Ctrl+Shift+P**를 눌러 명령 팔레트를 열고 **ADL: Start Local Run Service**를 입력합니다.
2. 처음으로 EULA에 동의합니다. 

   ![EULA에 동의 ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. cmd 콘솔이 표시됩니다. 처음 사용하는 경우 3을 입력한 다음 데이터 입력 및 출력을 위한 로컬 폴더 경로를 입력해야 합니다. 다른 옵션은 기본값을 사용하면 됩니다. 

   ![Data Lake Tools for Visual Studio Code가 cmd 로컬 실행](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Ctrl+Shift+P를 눌러 명령 팔레트를 열고 **ADL: Submit Job**을 입력한 다음 **Local**을 선택하여 작업을 로컬 계정에 제출합니다.

   ![Data Lake Tools for Visual Studio Code 로컬 선택](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. 작업 제출 후 출력 창에서 jobUrl을 클릭하여 제출 세부 정보를 확인할 수 있습니다. 더 자세한 작업 정보를 알고 싶은 경우 CMD 콘솔에서 7을 입력하여 CMD 콘솔에서 작업 제출 상태도 볼 수 있습니다.

   ![Data Lake Tools for Visual Studio Code 로컬 실행 출력](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code 로컬 실행 cmd 상태](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>U-SQL 작업에 대한 로컬 디버그 시작  
처음 사용하는 경우 아직 입력하지 않았으면 **ADL: Download Localrun Dependency** 패키지를 입력하라는 메시지가 표시됩니다.
  
1. **Ctrl+Shift+P**를 눌러 명령 팔레트를 열고 **ADL:Start Local Run Service**를 입력합니다. Cmd 창이 표시됩니다. **DataRoot**가 설정되어 있는지 확인합니다.
3. C# 코드 숨김에서 중단점을 설정합니다.
4. 스크립트 편집기로 돌아가 **Ctrl+Shift+P**를 눌러 명령 팔레트를 열고 **Local Debug**를 입력하여 로컬 디버그 서비스를 시작합니다.

![Data Lake Tools for Visual Studio Code 로컬 디버그 결과](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>다음 단계
- Azure Data Lake Tools for Visual Studio Code 사용에 대해서는 [Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)을 참조하세요.
- Data Lake Analytics 시작 정보는 [자습서: Azure Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)을 참조하세요.
- Data Lake Tools for Visual Studio 사용에 대한 자세한 내용은 [자습서: Data Lake Tools for Visual Studio로 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
- 어셈블리를 개발에 대한 정보는 [Azure Data Lake Analytics 작업에 U-SQL 어셈블리 개발](data-lake-analytics-u-sql-develop-assemblies.md)을 참조하세요.
