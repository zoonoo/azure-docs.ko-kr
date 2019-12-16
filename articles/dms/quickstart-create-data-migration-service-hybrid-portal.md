---
title: '빠른 시작: Azure Portal을 사용하여 Azure Database Migration Service 하이브리드 모드 인스턴스 만들기 | Microsoft Docs'
description: Azure Portal을 사용하여 하이브리드 모드에서 Azure Database Migration Service 인스턴스 만들기
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 12/06/2019
ms.openlocfilehash: a124c33f15318f1b9b22a750a1de15601823afa3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890694"
---
# <a name="quickstart-create-an-instance-of-azure-database-migration-service-in-hybrid-mode-using-the-azure-portal-preview"></a>빠른 시작: Azure Portal(미리 보기)을 사용하여 하이브리드 모드에서 Azure Database Migration Service 인스턴스 만들기

Azure Database Migration Service 하이브리드 모드는 클라우드에서 실행 중인 Azure Database Migration Service 인스턴스와 온-프레미스에서 호스트되는 마이그레이션 작업자를 함께 사용하여 데이터베이스 마이그레이션을 관리합니다. 하이브리드 모드는 온-프레미스 네트워크와 Azure 간에 사이트 간 연결이 부족하거나 사이트 간 연결 대역폭이 제한된 경우에 특히 유용합니다.

이 빠른 시작에서는 Azure Portal을 사용하여 하이브리드 모드에서 Azure Database Migration Service의 인스턴스를 만듭니다. 그런 다음, 온-프레미스 네트워크에서 Hybrid Worker를 다운로드, 설치 및 설정합니다. 미리 보기 중에 Azure Database Migration Service 하이브리드 모드를 사용하여 SQL Server 온-프레미스 인스턴스에서 Azure SQL Database으로 데이터를 마이그레이션할 수 있습니다.

> [!IMPORTANT]
> Azure Database Migration Service 하이브리드 설치 관리자에는 .NET 4.7.2 이상이 필요합니다. 최신 버전의 .NET을 찾으려면 [.NET Framework 다운로드](https://dotnet.microsoft.com/download/dotnet-framework) 페이지를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

웹 브라우저를 열고 [Microsoft Azure Portal](https://portal.azure.com/)로 이동한 다음 자격 증명을 입력하여 포털에 로그인합니다.

기본 보기는 서비스 대시보드입니다.

## <a name="register-the-resource-provider"></a>리소스 공급자 등록

첫 번째 Azure Database Migration Service 인스턴스를 만들기 전에 Microsoft.DataMigration 리소스 공급자를 등록합니다.

1. Azure Portal에서 **구독**을 선택하고, Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![검색 리소스 공급자](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>서비스 인스턴스 만들기

1. +**리소스 만들기**를 선택하여 Azure Database Migration Service의 인스턴스를 만듭니다.

2. Marketplace에서 "migration"을 검색하고 **Azure Database Migration Service**를 선택한 다음, **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

3. **마이그레이션 서비스 만들기** 화면에서:

    - Azure Database Migration Service 인스턴스를 식별할 수 있도록 기억하기 쉽고 고유한 **서비스 이름**을 선택합니다.
    - 인스턴스를 만들려는 Azure **구독**을 선택합니다.
    - 기존 **리소스 그룹**을 선택하거나 새 리소스 그룹을 만듭니다.
    - 원본 또는 대상 서버에 가장 가까운 **위치**를 선택합니다.

    > [!IMPORTANT]
    > 미리 보기 중에는 하이브리드 모드가 미국 동부 지역에서만 지원됩니다. Hybrid Worker가 온-프레미스 네트워크에 설치되어 있으므로 다른 지역의 대상으로 마이그레이션하는 경우에도 성능에 거의 영향을 주지 않습니다.

    - **서비스 모드**에서 **하이브리드(미리 보기)** 를 선택합니다.

      ![마이그레이션 서비스 만들기 - 기본 사항](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. **검토 + 만들기**를 선택합니다.

5. **검토 + 만들기** 탭에서 용어를 검토하고, 제공된 기타 정보를 확인한 다음, **만들기**를 선택합니다.

    ![마이그레이션 서비스 만들기 - 검토 + 만들기](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    잠시 후에 하이브리드 모드에서 Azure Database Migration Service 인스턴스가 만들어지고 사용할 준비가 됩니다. Azure Database Migration Service 인스턴스가 다음 이미지와 같이 표시됩니다.

    ![Azure Database Migration Service 하이브리드 모드 인스턴스](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. 서비스를 만든 후 **속성**을 선택하고, **리소스 ID** 상자에 표시된 값을 복사합니다. 이 값은 Azure Database Migration Service Hybrid Worker를 설치하는 데 사용합니다.

    ![Azure Database Migration Service 하이브리드 모드 속성](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Azure 앱 등록 ID 만들기

온-프레미스 Hybrid Worker가 클라우드의 Azure Database Migration Service와 통신하는 데 사용할 수 있는 Azure 앱 등록 ID를 만들어야 합니다.

1. Azure Portal에서 **Azure Active Directory**를 선택한 다음, **앱 등록**을 선택하고 **새 등록**을 선택합니다.
2. 애플리케이션의 이름을 지정한 다음, **지원되는 계정 유형**에서 지원할 계정 유형을 선택하고 애플리케이션을 사용할 수 있는 사용자를 지정합니다.

    ![Azure Database Migration Service 하이브리드 모드 등록 애플리케이션](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. **리디렉션 URI(선택 사항)** 필드에 대해 기본값을 그대로 사용하고 **등록**을 선택합니다.

4. 앱 ID 등록이 완료된 후 **애플리케이션(클라이언트) ID**를 기록해 둡니다. 이 ID는 Hybrid Worker를 설치하는 데 사용됩니다.

5. Azure Portal에서 Azure Database Migration Service로 이동하고, **액세스 제어(IAM)** 를 선택한 다음, **역할 할당 추가**를 선택하여 앱 ID에 참가자 액세스 권한을 할당합니다.

    ![Azure Database Migration Service 하이브리드 모드 참가자 역할 할당](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. 역할로 **참가자**를 선택하고, **Azure AD 사용자 또는 서비스 주체**에 액세스 권한을 할당한 후 앱 ID 이름을 선택합니다.

    ![Azure Database Migration Service 하이브리드 모드 참가자 역할 할당 세부 정보](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. **저장**을 선택하여 Azure Database Migration Service 리소스에 앱 ID에 대한 역할 할당을 저장합니다.

## <a name="download-and-install-the-hybrid-worker"></a>Hybrid worker 다운로드 및 설치

1. Azure Portal에서 Azure Database Migration Service 인스턴스로 이동합니다.

2. **설정**에서 **하이브리드**를 선택한 다음, **설치 관리자 다운로드**를 선택하여 Hybrid Worker를 다운로드합니다.

    ![Azure Database Migration Service Hybrid Worker 다운로드](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Azure Database Migration Service Hybrid Worker를 호스트할 서버에서 ZIP 파일의 압축을 풉니다.

    > [!IMPORTANT]
    > Azure Database Migration Service 하이브리드 설치 관리자에는 .NET 4.7.2 이상이 필요합니다. 최신 버전의 .NET을 찾으려면 [.NET Framework 다운로드](https://dotnet.microsoft.com/download/dotnet-framework) 페이지를 참조하세요.

4. 설치 폴더에서 **dmsSettings.json** 파일을 찾아 열고, **ApplicationId** 및 **resourceId**를 지정한 다음, 파일을 저장합니다.

    ![Azure Database Migration Service Hybrid Worker 설정](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)
 
5. 다음 명령을 사용하여 Azure Database Migration Service가 Hybrid Worker의 통신을 인증하는 데 사용할 수 있는 인증서를 생성합니다.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    인증서가 Installer 폴더에 생성됩니다.

    ![Azure Database Migration Service Hybrid Worker 인증서](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. Azure Portal에서 앱 ID로 이동한 후 **관리** 아래에서 **인증서 및 비밀**을 선택하고 **인증서 업로드**를 선택하여 생성한 퍼블릭 인증서를 선택합니다.

    ![Azure Database Migration Service Hybrid Worker 인증서 업로드](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. 다음 명령을 실행하여 온-프레미스 서버에 Azure Database Migration Service Hybrid Worker를 설치합니다.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

8. 오류가 발생하지 않고 설치 관리자가 실행되면 서비스는 Azure Database Migration Service에 온라인 상태를 표시하며, 이것은 데이터베이스를 마이그레이션할 준비가 되었음을 나타냅니다.

    ![Azure Database Migration Service 온라인](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Azure Database Migration Service 하이브리드 모드 제거

현재는 다음 명령을 사용하여 온-프레미스 서버의 Azure Database Migration Service Hybrid Worker 설치 관리자를 통해서만 Azure Database Migration Service 하이브리드 모드를 제거할 수 있습니다.

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [SQL Server에서 Azure SQL Database Managed Instance 온라인으로 마이그레이션](tutorial-sql-server-managed-instance-online.md)
> [SQL Server를 Azure SQL Database 오프라인의 단일 데이터베이스 또는 풀링 데이터베이스로 마이그레이션](tutorial-sql-server-to-azure-sql.md)
