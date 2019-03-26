---
title: SQL Server Express에서 SQL Server로 Azure AD Connect 데이터베이스를 이동합니다. | Microsoft Docs
description: 이 문서에서는 로컬 SQL Server Express 서버에서 원격 SQL Server로 Azure AD Connect 데이터베이스를 이동하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3485639062f33d013152cd30f6785df4d1c79790
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437784"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>SQL Server Express에서 SQL Server로 Azure AD Connect 데이터베이스 이동 

이 문서에서는 로컬 SQL Server Express 서버에서 원격 SQL Server로 Azure AD Connect 데이터베이스를 이동하는 방법을 설명합니다.  이 작업을 수행하려면 다음 절차를 사용하면 됩니다.

## <a name="about-this-scenario"></a>이 시나리오 정보
다음은 이 시나리오에 대한 간략한 정보입니다.  이 시나리오에서 Azure AD Connect 버전(1.1.819.0)을 단일 Windows Server 2016 도메인 컨트롤러에 설치합니다.  해당 데이터베이스에 대해 기본 제공 SQL Server 2012 Express Edition을 사용합니다.  데이터베이스는 SQL Server 2017 서버로 이동됩니다.

![](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Azure AD Connect 데이터베이스 이동
다음 단계를 사용하여 원격 SQL Server로 Azure AD Connect 데이터베이스를 이동합니다.

1. Azure AD Connect 서버에서로 **서비스**로 이동하고 **Microsoft Azure AD Sync** 서비스를 중지합니다.
2. **%Program Files%\Microsoft Azure AD Sync/데이터/** 폴더를 찾고 **ADSync.mdf** 및 **ADSync_log.ldf** 파일을 원격 SQL Server에 복사합니다.
3. **Microsoft Azure AD Sync** 서비스를 Azure AD Connect 서버에서 다시 시작합니다.
4. 제어판 - 프로그램 - 프로그램 및 기능으로 이동하여 Azure AD Connect를 제거합니다.  Microsoft Azure AD Connect를 선택하고 위쪽에서 제거합니다.
5. 원격 SQL server에서 SQL Server Management Studio를 엽니다.
6. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 연결을 선택합니다.
7. **데이터베이스 연결** 화면에서 **추가**를 클릭하고 ADSync.mdf 파일로 이동합니다.  **확인**을 클릭합니다.
   ![](media/how-to-connect-install-move-db/move2.png)

8. 데이터베이스가 연결되면 Azure AD Connect 서버로 다시 이동하고 Azure AD Connect를 설치합니다.
9. MSI 설치가 완료되면 Azure AD Connect 마법사가 기본 모드 설치를 시작합니다. 종료 아이콘을 클릭하여 화면을 닫습니다.
   ![시작](./media/how-to-connect-install-move-db/db1.png)
10. 새 명령 프롬프트 또는 PowerShell 세션을 시작합니다. 폴더로 이동 \<드라이브 > Azure AD Connect \program files\Microsoft 합니다. .\AzureADConnect.exe /useexistingdatabase 명령을 실행하여 Azure AD Connect 마법사를 “기존 데이터베이스 사용” 설치 모드로 시작합니다.
    ![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. Azure AD Connect 시작 화면이 표시됩니다. 사용 조건 및 개인 정보 취급 방침에 동의하면 **계속**을 클릭합니다.
    ![시작](./media/how-to-connect-install-move-db/db3.png)
12. **필수 구성 요소 설치** 화면에서 **기존 SQL Server 사용** 옵션을 사용할 수 있습니다. ADSync 데이터베이스를 호스팅하는 SQL Server의 이름을 지정합니다. ADSync 데이터베이스를 호스팅하는 데 사용되는 SQL 엔진 인스턴스가 SQL Server에 있는 기본 인스턴스가 아닌 경우 SQL 엔진 인스턴스 이름을 지정해야 합니다. 또한 SQL 검색을 사용하지 않는 경우 SQL 엔진 인스턴스 포트 번호를 지정해야 합니다. 예:          
    ![시작](./media/how-to-connect-install-move-db/db4.png)           

13. **Azure AD에 연결** 화면에서 Azure AD 디렉터리의 전역 관리자의 자격 증명을 제공해야 합니다. 기본 onmicrosoft.com 도메인의 계정을 사용하는 것이 좋습니다. 이 계정은 Azure AD에서 서비스 계정을 만드는 데에만 사용되며 마법사를 완료한 후에는 사용되지 않습니다.
    ![연결](./media/how-to-connect-install-move-db/db5.png)
 
14. **디렉터리 연결** 화면에서 디렉터리 동기화에 대해 구성된 기존 AD 포리스트가 옆에 빨간색 십자가 아이콘과 함께 나열됩니다. 온-프레미스 AD 포리스트에서 변경 내용을 동기화하려면 AD DS 계정이 필요합니다. 자격 증명이 암호화되고 이전 Azure AD Connect 서버에서만 해독할 수 있으므로 Azure AD Connect 마법사는 ADSync 데이터베이스에 저장된 AD DS 계정의 자격 증명을 검색하는 데 사용할 수 없습니다. **자격 증명 변경**을 클릭하여 AD 포리스트에 대한 AD DS 계정을 지정합니다.
    ![Directories](./media/how-to-connect-install-move-db/db6.png)
 
 
15. 팝업 대화 상자에서 (i) 엔터프라이즈 관리자 자격 증명을 제공하고 Azure AD Connect에서 AD DS 계정을 만들도록 하거나 (ii) 직접 AD DS 계정을 만들고 Azure AD Connect에 해당 자격 증명을 제공할 수 있습니다. 옵션을 선택하고 필요한 자격 증명을 제공한 후 **확인**을 클릭하여 팝업 대화 상자를 닫습니다.
    ![시작](./media/how-to-connect-install-move-db/db7.png)
 
 
16. 자격 증명이 제공되면 빨간색 십자가 아이콘이 녹색 체크 표시 아이콘으로 바뀝니다. **다음**을 클릭합니다.
    ![시작](./media/how-to-connect-install-move-db/db8.png)
 
 
17. **구성 준비** 화면에서 **설치**를 클릭합니다.
    ![시작](./media/how-to-connect-install-move-db/db9.png)
 
 
18. 설치가 완료되면 Azure AD Connect 서버가 자동으로 준비 모드에 사용할 수 있게 설정됩니다. 준비 모드를 해제하기 전에 예기치 않은 변경 내용에 대한 서버 구성 및 보류 중인 내보내기를 검토하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
- [기존 ADSync 데이터베이스를 사용하여 Azure AD Connect 설치](how-to-connect-install-existing-database.md)
- [ISQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치](how-to-connect-install-sql-delegation.md)

