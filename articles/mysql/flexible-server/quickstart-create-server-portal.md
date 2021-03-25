---
title: '빠른 시작: Azure Database for MySQL 유연한 서버 만들기 - Azure Portal'
description: 이 문서에서는 Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버를 몇 분 내에 만드는 과정을 안내합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 074b799a4f0e83c47aac0b2b3fca5386bd45429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521971"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>빠른 시작: Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 만들기

Azure Database for MySQL 유연한 서버는 클라우드에서 고가용성 MySQL 서버를 실행, 관리 및 크기 조정하는 데 사용할 수 있는 관리되는 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 유연한 서버를 만드는 방법을 보여 줍니다.

> [!IMPORTANT] 
> Azure Database for MySQL 유연한 서버는 현재 공개 미리 보기에 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure 포털](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버 만들기

정의된 [컴퓨팅 및 스토리지 리소스](./concepts-compute-storage.md) 세트를 사용하여 유연한 서버를 만듭니다. 서버는 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md) 내에 만듭니다.

다음 단계를 완료하여 유연한 서버를 만듭니다.

1. Azure Portal에서 **Azure Database for MySQL 서버** 를 검색하여 선택합니다.
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Azure Database for MySQL 서버에 대한 검색을 보여 주는 스크린샷":::

2. **추가** 를 선택합니다. 

3. **Azure Database for MySQL 배포 옵션 선택** 페이지에서 배포 옵션으로 **유연한 서버** 를 선택합니다.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="유연한 서버 옵션을 보여 주는 스크린샷":::    

4. **기본** 탭에서 다음 정보를 입력합니다. 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="유연한 서버 페이지의 기본 탭을 보여 주는 스크린샷"::: 
                                    
    |**설정**|**제안 값**|**설명**|
    |---|---|---|
    Subscription|구독 이름|서버에 사용할 Azure 구독입니다. 여러 구독이 있는 경우 리소스에 대한 요금이 청구되도록 하려는 구독을 선택합니다.|
    Resource group|**myresourcegroup**| 새 리소스 그룹 이름 또는 구독의 기존 이름입니다.|
    서버 이름 |**mydemoserver**|유연한 서버를 식별하는 고유한 이름입니다. 사용자가 제공한 서버 이름에 `mysql.database.azure.com` 도메인 이름이 추가됩니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3~63자여야 합니다.|
    관리자 사용자 이름 |**mydemouser**| 서버에 연결할 때 사용할 사용자 고유의 로그인 계정입니다. 관리자 사용자 이름은 **azure_superuser**, **admin**, **administrator**, **root**, **guest** 또는 **public** 일 수 없습니다.|
    암호 |사용자 암호| 서버 관리자 계정의 새 암호입니다. 8-128자여야 합니다. 또한 다음 범주의 세 가지 문자를 포함해야 합니다. 영어 대문자, 영어 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).|
    지역|사용자와 가장 가까운 지역| 사용자에게 가장 가까운 위치입니다.|
    버전|**5.7**| MySQL 주 버전입니다.|
    컴퓨팅 + 스토리지 | **버스트 가능**, **Standard_B1ms**, **10GiB**, **7일** | 새 서버에 대한 컴퓨팅, 스토리지 및 백업 구성입니다. **서버 구성** 을 선택합니다. **버스트 가능**, **Standard_B1ms**, **10GiB** 및 **7일** 은 **컴퓨팅 계층**, **컴퓨팅 크기**, **스토리지 크기** 및 백업 **보존 기간** 에 대한 기본값입니다. 이러한 값은 그대로 두거나 조정할 수 있습니다. 컴퓨팅 및 스토리지 선택 항목을 저장하려면 **저장** 을 선택하여 구성을 계속합니다. 다음 스크린샷에서는 컴퓨팅 및 스토리지 옵션을 보여 줍니다.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="컴퓨팅 및 스토리지 옵션을 보여 주는 스크린샷":::

5. 네트워킹 옵션을 구성합니다.

    **네트워킹** 탭에서 서버에 연결할 수 있는 방법을 선택할 수 있습니다. 서버에 연결하기 위해 Azure Database for MySQL 유연한 서버에서 제공하는 두 가지 방법은 다음과 같습니다. 
   - 퍼블릭 액세스(허용된 IP 주소)
   - 프라이빗 액세스(VNet 통합) 
   
   퍼블릭 액세스를 사용하는 경우 서버에 대한 액세스는 방화벽 규칙에 추가하는 허용된 IP 주소로 제한됩니다. 특정 IP 주소 또는 범위에 대한 방화벽을 여는 규칙을 만들지 않는 한 이 방법은 외부 애플리케이션과 도구에서 서버 및 서버의 모든 데이터베이스에 연결하지 못하도록 방지합니다. 프라이빗 액세스(VNet 통합)를 사용하는 경우 서버에 대한 액세스는 가상 네트워크로 제한됩니다. 이 빠른 시작에서는 서버에 연결하기 위해 퍼블릭 액세스를 사용하도록 설정하는 방법을 알아봅니다. [개념 문서에서 연결 방법에 대해 자세히 알아보세요.](./concepts-networking.md)

    > [!NOTE]
    > 서버가 만들어지면 연결 방법을 변경할 수 없습니다. 예를 들어 서버를 만들 때 **퍼블릭 액세스(허용된 IP 주소)** 를 선택하면 서버를 만든 후에 **프라이빗 액세스(VNet 통합)** 로 변경할 수 없습니다. VNet 통합을 통해 서버에 대한 액세스를 안전하게 보호하려면 프라이빗 액세스 권한이 있는 서버를 만드는 것이 좋습니다. [개념 문서에서 프라이빗 액세스에 대해 자세히 알아보세요.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="네트워킹 탭을 보여 주는 스크린샷":::  

6. **검토 + 만들기** 를 선택하여 유연한 서버 구성을 검토합니다.

7. **만들기** 를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

8. 도구 모음에서 **알림**(종 모양 단추)을 클릭하여 배포 프로세스를 모니터링합니다. 배포가 완료되면 **대시보드에 고정** 을 선택하여 유연한 서버에 대한 타일을 Azure Portal 대시보드에 만들 수 있습니다. 이 타일은 서버의 **개요** 페이지에 대한 바로 가기입니다. **리소스로 이동** 옵션을 선택하면 서버의 **개요** 페이지가 열립니다.

기본적으로 이러한 데이터베이스는 information_schema, mysql, performance_schema 및 sys 서버 아래에 만들어집니다.

> [!NOTE]
> 연결 문제를 방지하려면 네트워크에서 3306 포트(Azure Database for MySQL 유연한 서버에서 사용하는 포트)를 통해 아웃바운드 트래픽을 허용하는지 확인합니다.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>mysql.exe를 사용하여 서버에 연결

프라이빗 액세스(VNet 통합)를 사용하여 유연한 서버를 만든 경우 서버와 동일한 가상 네트워크 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고, 유연한 서버를 사용하여 만든 가상 네트워크에 이를 추가할 수 있습니다. 자세한 정보는 [프라이빗 액세스 설명서](how-to-manage-virtual-network-portal.md) 구성을 참조하세요.

퍼블릭 액세스(허용된 IP 주소)를 사용하여 유연한 서버를 만든 경우 로컬 IP 주소를 서버의 방화벽 규칙 목록에 추가할 수 있습니다. 단계별 지침은 [방화벽 규칙 만들기 또는 관리 설명서](how-to-manage-firewall-portal.md)를 참조하세요.

[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 또는 [MySQL 워크벤치](./connect-workbench.md)를 사용하여 로컬 환경에서 서버에 연결할 수 있습니다. 

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

**퍼블릭 액세스** 를 사용하여 유연한 서버를 프로비저닝한 경우 아래와 같이 미리 설치된 mysql 클라이언트를 사용하여 유연한 서버에 연결할 때 [Azure Cloud Shell](https://shell.azure.com/bash)을 사용할 수도 있습니다.

Azure Cloud Shell을 사용하여 유연한 서버에 연결하려면 Azure Cloud Shell에서 유연한 서버로의 네트워킹 액세스를 허용해야 합니다. 이를 위해 Azure Portal의 MySQL 유연한 서버 **네트워킹** 블레이드로 이동하고 **방화벽** 섹션 아래에 있는 "Allow public access from any Azure service within Azure to this server(Azure 내 모든 Azure 서비스에서 이 서버에 대한 퍼블릭 액세스 허용)" 확인란을 선택하고 저장을 클릭하여 설정을 유지합니다.

> [!NOTE]
> 개발 또는 테스트하는 경우에만 **Allow public access from any Azure service within Azure to this server(Azure 내 모든 Azure 서비스에서 이 서버에 대한 퍼블릭 액세스 허용)** 를 선택해야 합니다. 다른 고객의 구독에서 들어오는 연결을 포함하여 Azure 서비스 또는 자산에 할당된 IP 주소에서 들어오는 연결을 허용하도록 방화벽을 구성합니다.

**사용해 보기** 를 클릭하여 Azure Cloud Shell을 시작하고 다음 명령을 사용하여 유연한 서버에 연결합니다. 명령에서는 서버 이름, 사용자 이름 및 암호를 사용합니다. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

이전 명령을 실행한 후에 유연한 서버에 연결하는 동안 다음과 같은 오류 메시지가 표시되는 경우 앞에서 언급한 "Allow public access from any Azure service within Azure to this server(Azure 내 모든 Azure 서비스에서 이 서버에 대한 퍼블릭 액세스 허용)"를 사용하여 방화벽 규칙을 설정하지 않았거나 옵션이 저장되지 않은 것입니다. 방화벽을 다시 설정하고 다시 시도해 보세요.

오류 2002(HY000): <servername>의 MySQL 서버에 연결할 수 없음(115)

## <a name="clean-up-resources"></a>리소스 정리
이제 Azure Database for MySQL 유연한 서버를 리소스 그룹에 만들었습니다. 이러한 리소스가 나중에 필요하지 않을 경우에는 리소스 그룹을 삭제하거나 MySQL 서버만 삭제하여 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **리소스 그룹** 을 검색하고 선택합니다.
1. 리소스 그룹 목록에서 삭제하려는 리소스 그룹의 이름을 선택합니다.
1. 리소스 그룹에 대한 **개요** 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
1. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

서버를 삭제하려면 다음과 같이 서버에 대한 **개요** 페이지에서 **삭제** 를 선택할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="서버를 삭제하는 방법을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [MySQL로 PHP(Laravel) 웹앱 빌드](tutorial-php-database-app.md)
