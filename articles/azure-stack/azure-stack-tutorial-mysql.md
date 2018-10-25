---
title: Azure Stack에서 항상 사용 가능한 MySQL 데이터베이스를 제공 합니다. | Microsoft Docs
description: Azure Stack을 사용 하 여 호스트 컴퓨터 및 항상 사용 가능한 MySQL 데이터베이스는 MySQL Server 리소스 공급자를 만드는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 84aaa5534c629554074544b4bb56ae8da8825397
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986458"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>자습서: 항상 사용 가능한 MySQL 데이터베이스를 제공 합니다.

Azure Stack 운영자로 호스트 MySQL Server 데이터베이스 서버 Vm을 구성할 수 있습니다. MySQL 후 클러스터를 성공적으로 MySQL 서비스에 가입한 사용자 항상 사용 가능한 MySQL 데이터베이스를 쉽게 만들 수 생성 하 고 Azure Stack에서 관리 합니다.

이 자습서에서는 Azure Stack marketplace 항목 만들기를 사용 하는 방법을 보여 줍니다.는 [MySQL 복제 클러스터를 사용 하 여](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)입니다. 이 솔루션 구성 가능한 복제본 수에 마스터 노드에서 데이터베이스를 복제할 여러 Vm을 사용 합니다. 만들어지면 클러스터 다음으로 추가할 수는 Azure Stack MySQL 호스팅 서버를 하 고 사용자는 항상 사용 가능한 MySQL 데이터베이스를 만들 수 있습니다.

> [!IMPORTANT]
> 합니다 **복제를 사용 하 여 MySQL** Azure Stack의 마켓플레이스 항목을 모든 Azure 클라우드 구독 환경에 사용할 수 없습니다. 마켓플레이스 항목이 tutoral 나머지를 수행 하기 전에 구독에 사용할 수 있는지 확인 합니다.

학습할 내용:

> [!div class="checklist"]
> * Marketplace 항목에서 MySQL 서버 클러스터 만들기
> * 서버를 호스팅하는 Azure Stack MySQL 만들기
> * 항상 사용 가능한 MySQL 데이터베이스 만들기

이 자습서에서는 세 개의 VM MySQL Server 클러스터를 만들어지고 사용 가능한 Azure Stack의 마켓플레이스 항목을 사용 하 여 구성 합니다. 

확인 단계를이 자습서를 시작 하기 전에 합니다 [MySQL Server 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md) 설치 된 Azure Stack marketplace에서 다음 항목을 사용할 수 있는지 및:

> [!IMPORTANT]
> 다음의 모든 MySQL 클러스터를 만드는 데 필요한 합니다.

- [복제를 사용 하 여 MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)합니다. 이 MySQL 클러스터 배포에 사용할 Bitnami 솔루션 템플릿입니다.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/credativ.Debian8backports?tab=Overview)합니다. Debian 8 "Jessie" backports 커널을 사용 하 여 Microsoft Azure에 대 한 credativ에서 제공합니다. Debian GNU/Linux는 가장 많이 사용되는 Linux 배포 중 하나입니다.
- [Linux 2.0에 대 한 사용자 지정 스크립트](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview)합니다. 사용자 지정 스크립트 확장은에 VM 사용자 지정 작업 후 VM 프로 비전을 실행 하는 도구입니다. 이 확장은 가상 컴퓨터에 추가 되 면 Azure storage에서 스크립트를 다운로드 하 고 VM에서 실행 수 있습니다. Azure PowerShell cmdlet 및 Azure 플랫폼 간 명령줄 인터페이스 (xPlat CLI)를 사용 하 여 사용자 지정 스크립트 확장 태스크를 자동화할 수도 있습니다.
- Linux 확장 1.4.7에 대 한 VM 액세스 합니다. VM 액세스 확장을 사용 하면 VM에 대 한 액세스를 다시 사용할 수 있도록 암호, SSH 키 또는 SSH 구성을 다시 설정할 수 있습니다. 암호 또는 SSH 키를 가진 새 사용자를 추가 하거나이 확장을 사용 하 여 사용자를 삭제할 수도 있습니다. 이 확장 Linux Vm을 대상 으로합니다.

Azure Stack marketplace에 항목을 추가 하는 방법에 대 한 자세한 내용은 참조는 [Azure Stack Marketplace 개요](azure-stack-marketplace.md)합니다.

같은 SSH 클라이언트를 해야 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 배포 된 후 Linux Vm에 로그인 합니다.

## <a name="create-a-mysql-server-cluster"></a>MySQL 서버 클러스터 만들기 
사용 하 여 MySQL 서버를 배포 하려면이 섹션의 단계를 사용 하 여 클러스터를 [복제를 사용 하 여 MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) marketplace 항목. 이 템플릿은 항상 사용 가능한 MySQL 클러스터에 구성 하는 세 가지 MySQL Server 인스턴스를 배포 합니다. 기본적으로 다음 리소스를 만듭니다.

- 가상 네트워크
- 네트워크 보안 그룹
- 저장소 계정
- 가용성 집합
- 세 개의 네트워크 인터페이스 (각각의 기본 Vm에 대해 하나씩)
- (기본 MySQL 클러스터 VM)에 대 한 공용 IP 주소
- MySQL 클러스터를 호스트 하는 3 개의 Linux Vm

1. 
[!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. 선택 **\+** **리소스 만들기** > **계산**를 차례로 **복제를 사용 하 여 MySQL**합니다.

   ![사용자 지정 템플릿 배포](media/azure-stack-tutorial-mysqlrp/1.png)

3. 에 기본 배포 정보를 제공 합니다 **기본 사항** 페이지입니다. 및 기본값을 검토 하 고, 필요에 따라 변경 하 고, 클릭 **확인**합니다.<br><br>최소한 다음을 제공 합니다.
   - 배포 이름 (기본값인 mymysql)
   - 응용 프로그램 루트 암호입니다. 사용 하 여 12 자의 영숫자 암호를 제공 **특수 문자가**
   - 응용 프로그램 데이터베이스 이름 (기본값인 bitnami)
   - MySQL 데이터베이스 복제본 만들려는 Vm의 수 (기본값은 2)
   - 사용할 구독을 선택 합니다.
   - 사용 하거나 새로 만들 리소스 그룹 선택
   - 위치 선택 (기본값: ASDK에 대 한 로컬)

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "배포 기본 사항")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. 에 **환경의 구성을** 페이지에서 다음 정보를 제공 하 고 클릭 **확인**: 
   - SSH (보안 셸) 인증에 사용할 암호 또는 SSH 공개 키입니다. 문자, 숫자 있어야 암호를 사용 하는 경우 및 **수** 특수 문자를 포함 합니다.
   - VM 크기 (기본값인 표준 D1 v2 Vm)
   - 데이터 디스크 크기 GB 클릭에서 **확인**

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "환경 구성")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. 배포 상태를 검토할 **요약**합니다. 사용자 지정된 템플릿 및 매개 변수를 다운로드 하 고 클릭 수 필요에 따라 **확인**합니다.

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "요약")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. 클릭 **Create** 에 **구입** 페이지 배포를 시작 합니다.

   ![구입](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > 배포 시간 정도 소요 됩니다. 배포가 완료 된 MySQL 클러스터를 계속 하기 전에 완전히 구성 되었는지 확인 하십시오. 

7. 모든 배포를 성공적으로 완료 한 후 리소스 그룹 항목을 검토 하 고 선택 합니다 **mysqlip** 공용 IP 주소 항목입니다. 공용 IP 주소 및 클러스터에 대 한 공용 IP의 전체 FQDN을 기록 합니다.<br><br>이 MySQL 클러스터를 활용 하 여 MySQL 호스팅 서버를 만들 수 있도록 Azure Stack 운영자에 게 제공 해야 합니다.


### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기
기본적으로 호스트 VM에 MySQL에 대 한 공용 액세스 권한이 구성 됩니다. Azure Stack MySQL 리소스 공급자를 연결 하 여 MySQL 클러스터를 관리에 대 한 인바운드 네트워크 보안 그룹 (NSG) 규칙을 만들 수 해야 합니다.

1. 관리자 포털에서 MySQL 클러스터를 배포할 때 만든 리소스 그룹으로 이동 하 고 네트워크 보안 그룹을 선택 (**sg-서브넷-기본**):

   ![open](media/azure-stack-tutorial-mysqlrp/6.png)

2. 선택 **인바운드 보안 규칙** 을 클릭 한 다음 **추가**합니다.<br><br>입력 **3306** 에 **대상 포트 범위** 에 설명을 선택적으로 제공 합니다 **이름** 및 **설명** 필드. 인바운드 보안 규칙 대화 상자를 닫고 추가 클릭 합니다.

   ![open](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>MySQL 클러스터에 대 한 외부 액세스 구성
MySQL 클러스터는 Azure Stack MySQL Server 호스트로 추가할 수 있습니다, 전에 외부 액세스를 사용할 수 있어야 합니다.

1. 이 예제에서는 SSH 클라이언트를 사용 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), 공용 IP에 액세스할 수 있는 컴퓨터에서 기본 MySQL 컴퓨터에 로그인 합니다. 기본 MySQL VM 이름은 일반적으로 끝나는 **0** 공용 IP를 할당 합니다.<br><br>사용자 이름 사용 하 여 VM에 공용 IP 및 로그를 사용 하 여 **bitnami** 및 특수 문자가 없는 이전에 만든 응용 프로그램 암호입니다.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. SSH 클라이언트 창에서 다음 명령을 사용 하 여 bitnami 서비스가 활성화 되어 실행을 확인 합니다. 메시지가 표시 되 면 다시 bitnami 암호를 제공 합니다.

   `sudo service bitnami status`

   ![서비스 확인](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. MySQL에 연결 하 여 SSH 클라이언트를 종료 한 다음 Azure Stack MySQL 호스팅 서버에서 사용할 원격 액세스 사용자 계정을 만듭니다.<br><br>앞에서 만든 루트 암호를 사용 하 여 루트로 MySQL에 로그인 하려면 다음 명령을 실행 하 고 새 관리자 사용자를 만드는 대체 *\<사용자 이름\>* 하 고 *\<암호\>* 환경의 필요에 따라 합니다. 이 예제에서는 만들 사용자의 이름은 **sqlsa** 강력한 암호는 데 사용 됩니다.

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![관리자 사용자 만들기](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. 새 MySQL 사용자 정보를 기록 합니다.<br><br>이 MySQL 클러스터를 사용 하 여 MySQL 호스팅 서버를 만들 수 있도록 Azure Stack 운영자에 게이 사용자 이름 및 암호를 공용 IP 주소 또는 클러스터에 대 한 공용 IP의 전체 FQDN을 제공 해야 합니다.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>서버를 호스팅하는 Azure Stack MySQL 만들기
MySQL 서버 클러스터를 생성 하 고 올바르게 구성 된 후 Azure Stack 운영자 Azure Stack MySQL 호스팅 서버를 추가 용량 사용자가 데이터베이스를 만들 수 있게 만들어야 합니다. 

주 VM MySQL 클러스터의 리소스 그룹을 만들 때 이전에 기록 하는 MySQL 클러스터의 공용 IP에 대 한 공용 IP 또는 전체 FQDN을 사용 하도록 (**mysqlip**). 또한 연산자는 MySQL 서버의 MySQL 클러스터 데이터베이스에 원격으로 액세스 하기 위해 만든 인증 자격 증명 인지 알고 있어야 할 수 있습니다.

> [!NOTE]
> 이 단계는 Azure Stack 운영자가 Azure Stack 관리 포털에서 실행 되어야 합니다.

MySQL 클러스터의 공용 IP 및 MySQL 인증 로그인 정보를 사용 하는 Azure Stack 운영자 수 이제 [새 MySQL 클러스터를 사용 하 여 MySQL 호스팅 서버를 만드는](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server)합니다. 

또한 계획을 만든 사용자에 대 한 MySQL 데이터베이스 만들기를 사용할 수 있도록 하려면 제공를 확인 합니다. 운영자를 추가 해야 합니다 **Microsoft.MySqlAdapter** 계획에 서비스 및 항상 사용 가능한 데이터베이스에 맞게 새 할당량 만들기. 계획을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [계획, 제안, 할당량 및 구독 개요](azure-stack-plan-offer-quota-overview.md)합니다.

> [!TIP]
> 합니다 **Microsoft.MySqlAdapter** 서비스까지 계획에 추가할 수 없습니다는 [MySQL Server 리소스 공급자가 배포 된](azure-stack-mysql-resource-provider-deploy.md)합니다.



## <a name="create-a-highly-available-mysql-database"></a>항상 사용 가능한 MySQL 데이터베이스 만들기
MySQL Server 데이터베이스 기능을 포함 하 여 구독을 사용 하 여 테 넌 트 사용자가 항상 사용 가능한 MySQL 데이터베이스를 만들 수 있습니다 MySQL 클러스터 생성, 구성 및 Azure Stack MySQL 호스팅 서버와 Azure Stack 운영자가 추가 된, 후 이 섹션의 단계를 수행 합니다. 

> [!NOTE]
> MySQL 서버 (Microsoft.MySQLAdapter 서비스) 기능을 제공 하는 구독을 사용 하 여 테 넌 트 사용자로 Azure Stack 사용자 포털에서 이러한 단계를 실행 합니다.

1. 
[!INCLUDE [azs-user-portal](../../includes/azs-user-portal.md)]

2. 선택 **\+** **리소스 만들기** > **Data \+ Storage**를 차례로 **MySQL 데이터베이스** .<br><br>이름, 데이터 정렬를 사용 하려면 구독 및 배포에 사용할 위치를 포함 하 여 필요한 데이터베이스 속성 정보를 제공 합니다. 

   ![MySQL 데이터베이스 만들기](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. 선택 **SKU** 를 선택한 다음 적절 한 MySQL 호스팅 서버 SKU를 사용 합니다. 이 예제에서는 Azure Stack 운영자 만들었습니다 합니다 **MySQL-HA** MySQL 클러스터 데이터베이스에 대 한 고가용성을 지원 하려면 SKU입니다.

   ![SKU 선택](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. 선택 **로그인** > **새 로그인을 만듭니다** 새 데이터베이스에 사용 되는 MySQL 인증 자격 증명을 입력 합니다. 완료 되 면, 클릭 **확인** 차례로 **만들기** 데이터베이스 배포 프로세스를 시작 합니다.

   ![로그인 추가](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. MySQL 데이터베이스 배포를 성공적으로 완료 되 면 새 항상 사용 가능한 데이터베이스에 연결할 때 사용할 연결 문자열을 검색 하는 데이터베이스 속성을 검토 합니다. 

   ![연결 문자열 보기](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Marketplace 항목에서 MySQL 서버 클러스터 만들기
> * 서버를 호스팅하는 Azure Stack MySQL 만들기
> * 항상 사용 가능한 MySQL 데이터베이스 만들기

에 대해 알아보려면 다음 자습서로 이동 하는 방법.
> [!div class="nextstepaction"]
> [Web apps 제공](azure-stack-tutorial-app-service.md)
