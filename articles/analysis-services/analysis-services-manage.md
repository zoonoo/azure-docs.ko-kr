---
title: "Azure Analysis Services 관리 | Microsoft Docs"
description: "Azure에서 Analysis Services 서버를 관리하는 방법을 알아봅니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: fdc4e495fb3fb99022b0f7c487001b2fba42cccc
ms.lasthandoff: 02/16/2017


---
# <a name="manage-analysis-services"></a>Analysis Services 관리
Azure에 Analysis Services 서버를 만들었으면 즉시 또는 조만간에 수행해야 하는 몇 가지 운영 및 관리 작업이 있습니다. 예를 들어 데이터 새로 고침 처리를 실행하거나, 서버의 모델에 대한 액세스 권한이 있는 사용자를 제어하거나, 서버의 상태를 모니터링합니다. 일부 관리 작업은 Azure 포털에서만, 일부 다른 작업은 SSMS(SQL Server Management Studio)에서만, 일부 작업은 둘 중 하나에서 수행할 수 있습니다.

## <a name="azure-portal"></a>Azure 포털
[Azure 포털](http://portal.azure.com/)은 서버를 만들며 삭제하고, 서버 리소스를 모니터링하며, 크기를 변경하고, 서버에 대한 액세스 권한이 있는 사용자를 관리할 수 있는 포털입니다.  몇 가지 문제가 발생하면 지원 요청을 제출할 수도 있습니다.

![Azure에서 서버 이름 가져오기](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Azure에서 서버를 연결하는 것은 조직에서 서버 인스턴스를 연결하는 것과 같습니다. SSMS에서는 데이터 처리와 같이 동일한 작업을 많이 수행하거나 처리 스크립트를 만들고 역할을 관리하고 PowerShell을 사용할 수 있습니다. [SSMS를 다운로드하여 설치합니다.](#download-and-install-ssms)
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 큰 차이점 중 하나는 서버에 연결하는 데 사용하는 인증에 있습니다. Azure Analysis Services 서버에 연결하려면 **Active Directory 암호 인증**을 선택해야 합니다.

 SSMS를 사용할 때 처음으로 서버에 연결하기 전에 Analysis Services 관리자 그룹에 사용자 이름이 포함되어 있는지 확인합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [서버 관리자](#server-administrators)를 참조하세요.

### <a name="to-connect-with-ssms"></a>SSMS로 연결
1. 먼저 서버 이름을 가져온 후에 연결할 수 있습니다. **Azure 포털** > 서버 > **개요** > **서버 이름**에서 서버 이름을 복사합니다.
   
    ![Azure에서 서버 이름 가져오기](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSMS > **개체 탐색기**에서 **연결** > **Analysis Services**를 클릭합니다.
3. **서버에 연결** 대화 상자에서 서버 이름을 붙여넣은 다음 **인증**에서 다음 중 하나를 선택합니다.
   
    **Active Directory 통합 인증** - Active Directory에서 Azure Active Directory 페더레이션에 대해 SSO(Single Sign-On)를 사용합니다.
   
    **Active Directory 암호 인증** - 조직의 계정을 사용합니다. 예를 들어 비도메인 가입 컴퓨터에서 연결하는 경우에 이 옵션을 사용합니다.
   
    참고: Active Directory 인증이 표시되지 않으면 [최신 버전의 SSMS](#download-and-install-ssms)로 업데이트해야 할 수 있습니다.
   
    ![SSMS에서 연결](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Azure에서 SSMS를 사용하여 서버를 관리하는 것은 온-프레미스 서버를 관리하는 것과 매우 비슷하므로 여기서 자세한 내용을 다룰 필요가 없습니다. 필요한 도움말은 MSDN의 [Analysis Services 인스턴스 관리](https://msdn.microsoft.com/library/hh230806.aspx)에 모두 있습니다.

## <a name="server-administrators-and-database-users"></a>서버 관리자 및 데이터베이스 사용자
Azure Analysis Services에서는 두 가지 유형의 사용자, 서버 관리자 및 데이터베이스 사용자가 있습니다. 두 가지 유형의 사용자 모두 Azure Active Directory에 포함되어야 하며 조직 전자 메일 주소 또는 UPN으로 지정해야 합니다. 이는 Windows 도메인 사용자 이름으로 서버 관리자 및 데이터베이스 사용자를 지원하는 온-프레미스 테이블 형식 모델 데이터베이스와 다릅니다. 자세한 내용은 [Azure Analysis Services의 사용자 관리](analysis-services-manage-users.md)를 참조하세요.

## <a name="download-and-install-ssms"></a>SSMS 다운로드 및 설치
[SQL Server Management Studio를 다운로드합니다](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

다운로드 페이지에는 두 가지 옵션이 제공됩니다.
 
* 릴리스 후보 - Azure Analysis Services 미리 보기에만 연결할 때 사용하는 것이 좋습니다. 이 릴리스는 최신 업데이트를 받으며 Azure Analysis Services 미리 보기에서 가장 잘 작동합니다.
* 현재 프로덕션 릴리스 - 프로덕션 서버와 Azure Analysis Services 미리 보기 모두에 연결할 때 사용하는 것이 좋습니다. 이 릴리스를 사용할 경우 Azure Active Directory 인증을 사용하도록 레지스트리를 편집해야 할 수 있습니다.

## <a name="enable-azure-active-directory-authentication"></a>Azure Active Directory 인증 사용 설정

레지스트리에서 SSMS의 Azure Active Directory 인증 기능을 사용하도록 설정하려면 EnableAAD.reg라는 텍스트 파일을 만든 후 다음을 복사하여 붙여넣습니다.

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

저장한 다음 파일을 실행합니다.

## <a name="troubleshooting-connection-problems"></a>연결 문제 해결
SSMS를 사용하여 서버에 연결할 때(3단계) 페더레이션 되지 않은 계정 또는 Azure Active Directory에 없는 계정을 사용하여 로그인하려고 하는데 연결할 수 없는 경우 로그인 캐시를 지워야 할 수 있습니다. 다음 단계를 수행하기 전에 SSMS를 닫습니다.

1. 파일 탐색기에서 `C:\Users\<user_name>\AppData\Local\`로 이동합니다.
2. **AADCacheOM** 폴더를 삭제합니다.
3. **Local** 폴더에서 이름이 **omlibs-tokens-cache**로 시작하는 .dat 파일을 검색합니다. 찾은 경우 삭제합니다.
4. SSMS를 열고 위의 [SSMS로 연결](#to-connect-with-ssms) 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계
새 서버에 테이블 형식 모델을 아직 배포하지 않았으면 지금이야말로 좋은 기회입니다. 자세한 내용은 [Azure Analysis Services에 배포](analysis-services-deploy.md)를 참조하세요.

서버에 모델을 배포한 경우에는 클라이언트 또는 브라우저를 통해 연결할 준비가 된 것입니다. 자세한 내용은 [Azure Analysis Services 서버에서 데이터 가져오기](analysis-services-connect.md)를 참조하세요.


