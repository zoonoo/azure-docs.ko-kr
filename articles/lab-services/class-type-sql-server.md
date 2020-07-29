---
title: Azure SQL Database를 사용 하 여 관리 및 개발 하기 위한 랩 설정 Azure Lab Services
description: Azure SQL Database를 사용 하 여 관리 및 개발 하는 랩을 설정 하는 방법을 알아봅니다.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: a3601010eae614049b4af5aa2ddbcdc6acda21cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444882"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>SQL Server를 사용 하 여 관리 및 개발 하기 위한 랩 설정

이 문서에서는 Azure Lab Services에서 기본 SQL Server 관리 및 개발 클래스에 대 한 랩을 설정 하는 방법을 설명 합니다.  데이터베이스 개념은 대학에서 대부분의 컴퓨터 과학 부서에서 배울 수 있는 소개 과정 중 하나입니다. 구조적 쿼리 언어 (SQL)은 국제 표준입니다.  SQL은 데이터베이스의 콘텐츠 추가, 액세스 및 관리를 포함 하 여 데이터베이스 관리와 관련 된 표준 언어입니다.  이는 빠른 처리, 검증 된 안정성, 편의성 및 사용의 유연성에 가장 많이 사용 됩니다.

이 문서에서는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)및 [Azure Data Studio](https://github.com/microsoft/azuredatastudio)를 사용 하 여 랩에서 가상 머신 템플릿을 설정 하는 방법을 보여 줍니다.  이 랩에서는 전체 랩에서 하나의 공유 [SQL Server 데이터베이스](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) 를 사용 합니다. [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) 는 Azure에서 제공 하는 Paas (Platform As a Service) 데이터베이스 엔진입니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정 하려면 시작 하려면 Azure 구독 및 랩 계정이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 방법에 대 한 자세한 내용은 자습서를 참조 [하 여 랩 계정 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)을 참조 하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명 된 설정을 사용 하도록 설정 합니다. Marketplace 이미지를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [랩 작성자에 게 제공 되는 marketplace 이미지 지정](specify-marketplace-images.md)을 참조 하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
| Marketplace 이미지 | 랩 계정 내에서 사용 하기 위해 Windows 10 Enterprise Kn (x64)에서 ' Visual Studio 2019 Community (최신 릴리스) ' 이미지를 사용 하도록 설정 합니다. |

### <a name="shared-resource-configuration"></a>공유 리소스 구성

Lab Services에서 공유 리소스를 사용 하려면 먼저 가상 네트워크와 리소스 자체를 만들어야 합니다.  가상 네트워크를 만들어 랩에 연결 하려면 [Azure Lab Services에서 공유 리소스를 사용 하 여 랩을 만드는 방법을](how-to-create-a-lab-with-shared-resource.md)따르세요.  Lab Services 외부의 모든 리소스는 별도로 청구 되며 랩 비용 예상치에 포함 되지 않습니다.

>[!WARNING]
>랩을 만들기 전에 랩에 대 한 공유 리소스를 설정 해야 합니다.  랩을 만들기 *전에* vnet이 [랩 계정으로 피어 링](how-to-connect-peer-virtual-network.md) 되지 않는 경우 랩은 공유 리소스에 액세스할 수 없습니다.

네트워킹 측면을 처리 했으므로 이제 SQL Server 데이터베이스를 만들 수 있습니다.  Azure SQL Database에 대 한 가장 빠른 배포 옵션인 [단일 데이터베이스](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal) 를 만들 예정입니다.  다른 배포 옵션의 경우 [탄력적 풀](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), [관리 되는 인스턴스](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)또는 [SQL 가상 컴퓨터](https://docs.microsoft.com/azure/virtual-machines/windows/sql/quickstart-sql-vm-create-portal)를 만듭니다.

1. Azure Portal 메뉴에서 **새 리소스 만들기**를 선택 합니다.
2. **SQL Database** 를 선택 하 고 **만들기** 단추를 클릭 합니다.
3. **SQL Database 만들기** 폼의 **기본 사항** 탭에서 데이터베이스에 대 한 리소스 그룹을 선택 합니다.  *Sqldb-rg*를 사용 합니다.
4. **데이터베이스 이름**에 *classlab db*를 입력 합니다.
5. **서버** 설정에서 **새로 만들기** 를 클릭 하 여 데이터베이스를 보관할 새 서버를 만듭니다.
6. **새 서버** 플라이 아웃에서 서버 이름을 입력 합니다.  *Classlabdbserver*를 사용 합니다.  서버 이름은 전역적으로 고유 해야 합니다.
7. **서버 관리자 로그인**에 대해 *azureuser* 를 입력 합니다.
8. 기억 하기 쉬운 암호를 입력 하세요.  암호는 길이가 8 자 이상 이어야 하며 특수 문자를 포함 해야 합니다.
9. **위치의**지역을 선택 합니다.  가능 하면 랩 계정 및 피어 링 vnet과 동일한 위치를 입력 하 여 대기 시간을 최소화 합니다.
10. **확인** 을 클릭 하 여 **만들기 SQL Database** 양식으로 돌아갑니다.
11. **Compute + storage** 설정에서 **데이터베이스 구성** 링크를 클릭 합니다.
12. 클래스에 필요한 경우 데이터베이스 설정을 수정 합니다.  프로 비전 된 옵션 및 서버를 사용 하지 않는 옵션 중에서 선택할 수 있습니다.  이 예제에서는 자동 조정 되 서버를 사용 하지 않는 옵션을 사용 하 여 최대 vCores 4, 최소 vCores 1 Autopause 설정은 최소 1 시간으로 유지 됩니다. **적용**을 클릭합니다.
13. **다음: 네트워킹** 단추를 클릭 합니다.
14. 네트워킹 탭에서 **연결 방법**에 대 한 개인 끝점을 선택 합니다.
15. **개인 끝점** 섹션에서 **개인 끝점 추가**를 클릭 합니다.
16. **개인 끝점 만들기** 플라이 아웃에서 랩 계정에 대 한 가상 네트워크 피어 링 동일한 리소스 그룹을 선택 합니다.
17. **위치**에서 가상 네트워크와 동일한 위치를 선택 합니다.
18. **이름**에 대해 *sql-endpt*를 입력 합니다.
19. 대상 하위 리소스를 SqlServer로 설정 된 상태로 둡니다.
20. **가상 네트워크**의 경우 랩 계정에 대해 동일한 가상 네트워크 피어 링를 선택 합니다.
21. **서브넷**에 대해 끝점을 호스트 하려는 서브넷을 선택 합니다.  끝점에 할당 되는 IP는 해당 서브넷에 할당 된 범위를 벗어납니다.
22. **개인 DNS와 통합** 을 **아니요**로 설정 합니다. 간단히 하기 위해 개인 DNS 영역 또는 자체 DNS 서버를 통해 Azure의 DNS를 사용 합니다.
23. **확인**을 클릭합니다.
24. **다음: 추가 설정**을 선택하여 사용자 지정 설정을 더 많이 구성합니다.
25. **기존 데이터 사용** 설정에서 **샘플**을 선택 합니다.  데이터베이스를 만들 때 AdventureWorksLT 데이터베이스의 데이터가 사용 됩니다.
26. **검토 + 만들기**를 클릭합니다.
27. **만들기**를 클릭합니다.

SQL Database 배포가 성공적으로 완료 되 면 랩을 만들고 랩 템플릿 컴퓨터에 소프트웨어를 설치할 수 있습니다.

### <a name="lab-settings"></a>랩 설정

클래스 룸 랩을 설정 하는 경우 아래 표의 설정을 사용 합니다. 교실 랩을 만드는 방법에 대 한 자세한 내용은 [교실 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조 하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
| Virtual Machine 크기 | 중간. 이 크기는 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다. |
| 가상 머신 이미지 | Windows 10 Enterprise Kn (x64)의 Visual Studio 2019 Community (최신 릴리스) |

이제 랩을 만들었으므로 필요한 소프트웨어로 템플릿 컴퓨터를 수정 하겠습니다.

## <a name="visual-studio"></a>Visual Studio

위에서 선택한 이미지에는 [Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/)가 포함 됩니다.  모든 워크 로드 및 도구 집합이 이미지에 이미 설치 되어 있습니다.  Visual Studio 설치 관리자 사용 하 여 원하는 [선택적 도구를 설치할](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) 수 있습니다.  [Visual Studio에 로그인](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio) 하 여 community edition의 잠금을 해제 합니다.

Visual Studio에는 SQL Server Data Tools (SSDT)를 포함 하는 **데이터 저장 및 처리** 도구 집합이 포함 되어 있습니다.  SSDT 기능에 대 한 자세한 내용은 [SQL Server Data Tools 개요](https://docs.microsoft.com/sql/ssdt/sql-server-data-tools?view=sql-server-ver15)를 참조 하세요.  클래스에 대 한 공유 SQL Server에 대 한 연결이 성공적인 지 확인 하려면 [데이터베이스에 연결 및 기존 개체 찾아보기](https://docs.microsoft.com/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15)를 참조 하세요. 메시지가 표시 되 면 SQL Server 인스턴스에 연결할 수 있는 [허용 된 컴퓨터 목록](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure) 에 템플릿 컴퓨터 IP를 추가 합니다.

Visual Studio는 **웹 & 클라우드** 및 **데스크톱 & 모바일** 워크 로드를 비롯 한 여러 작업을 지원 합니다.  이러한 작업은 모두 데이터 원본으로 SQL Server을 지원 합니다. SQL Server ASP.NET Core를 사용 하는 방법에 대 한 자세한 내용은 [Azure App Service 자습서에서 ASP.NET Core 및 SQL Database 앱 빌드](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb) 를 참조 하세요.  [System.object](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) 라이브러리를 사용 하 여 [Xamarin](https://docs.microsoft.com/xamarin) 앱에서 SQL Database에 연결 합니다.

## <a name="install-azure-data-studio"></a>Azure Data Studio 설치

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) 는 Windows, Macos 및 Linux에서 온-프레미스 및 클라우드 데이터 플랫폼 제품군을 사용 하는 데이터 전문가를 위한 다중 데이터베이스 플랫폼 간 데스크톱 환경입니다.

1. [Windows 용 Azure Data Studio *시스템* 설치 관리자](https://go.microsoft.com/fwlink/?linkid=2127432)를 다운로드 합니다. 지원 되는 다른 운영 체제에 대 한 설치 관리자를 찾으려면 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download) 다운로드 페이지로 이동 합니다.
2. **사용권 계약** 페이지 **에서 동의 함을 선택 합니다**. **다음**을 클릭합니다.
3. **대상 위치 선택** 페이지에서 **다음**을 클릭합니다.
4. **시작 메뉴 폴더 선택** 페이지에서 **다음**을 클릭합니다.
5. **추가 작업 선택** 페이지에서 바탕 화면 아이콘을 원하는 경우 **바탕 화면 아이콘 만들기** 를 선택 합니다.  **다음**을 클릭합니다.
6. **설치 준비**에서 **다음**을 클릭 합니다.
7. 설치 관리자가 실행 될 때까지 기다립니다.  **Finish**를 클릭합니다.

Azure Data Studio 설치 했으므로 Azure SQL Database에 대 한 연결을 설정 하겠습니다.

1. Azure Data Studio **시작** 페이지에서 **새 연결** 링크를 클릭 합니다.
2. **연결 정보** 상자에서 필요한 정보를 입력 합니다.
    - **서버** 를 *classlabdbserver.database.windows.net* 로 설정 합니다.
    - **사용자** 이름을 *azureuser* 로 설정 합니다.
    - **암호** 를 데이터베이스를 만드는 데 사용 되는 암호로 설정 합니다.
    - **암호 기억을**선택 합니다.
    - **데이터베이스**에 대해 *classlab db*를 선택 합니다.
3. **연결**을 클릭합니다.

## <a name="install-sql-server-management-studio"></a>SQL Server Management Studio를 설치합니다.

[SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) 는 모든 SQL 인프라를 관리 하기 위한 통합 환경입니다.  SSMS는 데이터베이스 관리자가 데이터 인프라를 배포, 모니터링 및 업그레이드 하는 데 사용 하는 도구입니다.

1. [Sql Server Management Studio를 다운로드](https://aka.ms/ssmsfullsetup)합니다. 다운로드 한 후 설치 관리자를 시작 합니다.
2. **시작** 페이지에서 **설치**를 클릭 합니다.
3. **설치 완료** 페이지에서 **닫기**를 클릭 합니다.
4. Sql Server Management Studio를 시작 합니다.  
5. **종속성 구성 프로세스** 페이지에서 **닫기**를 클릭 합니다.

SSMS가 설치 되지 않은 경우 [SQL Server 연결 하 고 쿼리할](https://docs.microsoft.com/sql/ssms/tutorials/connect-query-sql-server)수 있습니다. 연결을 설정할 때 다음 값을 사용 합니다.

- 서버 유형: 데이터베이스 엔진
- 서버 이름: *classlabdbserver.database.windows.net*
- 인증: SQL Server 인증
- 로그인: *azureuser*
- Password: 데이터베이스를 만드는 데 사용 하는 암호입니다.

## <a name="cost-estimate"></a>예상 비용

이 클래스에 대해 가능한 예상 비용을 살펴보겠습니다. 예측에는 SQL Server를 실행 하는 비용이 포함 되지 않습니다.  데이터베이스 가격 책정에 대 한 현재 세부 정보는 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database) 을 참조 하세요.

25 명의 학생 클래스를 사용 합니다. 예약 된 클래스 시간은 20 시간입니다. 또한 각 학생은 예약 된 클래스 시간 외에도 과제 또는 배정에 대해 10 시간 할당량을 얻습니다. 선택한 가상 컴퓨터 크기는 42 lab 단위인 medium입니다.

이 클래스에 대 한 예상 비용 예상 예는 다음과 같습니다.

25 개 학생 \* (20 개의 예약 된 시간 \+ 10 할당량 시간) * 시간당 0.42 USD = 315.00 usd

>[!IMPORTANT]
>비용 예측은 예를 들어 목적 으로만 사용 됩니다. 가격 책정에 대 한 최신 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [템플릿 만들기, 관리 및 게시](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
