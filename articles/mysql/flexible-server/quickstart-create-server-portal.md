---
title: '빠른 시작: Azure DB for MySQL 유연한 서버 만들기 - Azure Portal'
description: 이 문서에서는 Azure Portal을 사용하여 몇 분 안에 Azure Database for MySQL 유연한 서버를 만드는 과정을 안내합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 0c082c797c75ba912bafead15d24ea3941cfc25e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534195"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>빠른 시작: Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 만들기

Azure Database for MySQL 유연한 서버는 클라우드에서 고가용성 MySQL 서버를 실행, 관리 및 확장하는 데 사용하는 관리형 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 유연한 서버를 만드는 방법을 보여 줍니다.

> [!IMPORTANT] 
> Azure Database for MySQL 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
웹 브라우저를 연 다음 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버 만들기

정의된 [컴퓨팅 및 스토리지 리소스](./concepts-compute-storage.md) 세트를 사용하여 유연한 서버를 만듭니다. 서버는 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md) 내에 만듭니다.

다음 단계에 따라 유연한 서버를 만듭니다.

1. 검색 상자를 사용하여 포털에서 "Azure Database for MySQL"을 검색하여 서비스를 찾습니다. 
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Azure Database for MySQL 검색":::

2. **추가** 를 선택합니다. 

3. "배포 옵션 선택 페이지"에서 배포 옵션으로 **유연한 서버** 를 선택합니다.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="배포 옵션 선택":::    

4. 다음 정보로 **기본 사항** 양식을 입력합니다. 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="서버 양식 만들기"::: 
                                    
    |**설정**|**제안 값**|**설명**|
    |---|---|---|
    Subscription|구독 이름|서버에 사용할 Azure 구독입니다. 구독이 여러 개인 경우 해당 리소스에 대해 요금이 청구되는 구독을 선택합니다.|
    Resource group|*myresourcegroup*| 새 리소스 그룹 이름 또는 구독의 기존 이름입니다.|
    서버 이름 |*mydemoserver*|유연한 서버를 식별하는 고유한 이름입니다. 사용자가 제공한 서버 이름에 *mysql.database.azure.com* 도메인 이름이 추가됩니다. 서버는 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자 이상이어야 합니다.|
    관리자 사용자 이름 |*mydemouser*| 서버에 연결할 경우 사용할 사용자 고유의 로그인 계정입니다. 관리자 로그인 이름은 **azure_superuser** , **admin** , **administrator** , **root** , **guest** 또는 **public** 일 수 없습니다.|
    암호 |사용자 암호| 서버 관리자 계정의 새 암호입니다. 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).|
    지역|사용자와 가장 가까운 지역| 사용자에게 가장 가까운 위치입니다.|
    버전|5.7| MySQL 주 버전입니다.|
    컴퓨팅 + 스토리지 | **버스트 가능** , **Standard_B1ms** , **10GiB** , **7일** | 새 서버에 대한 컴퓨팅, 스토리지 및 백업 구성입니다. **서버 구성** 을 선택합니다. *버스트 가능* , *Standard_B1ms* , *10GiB* , 및 *7일* 은 **컴퓨팅 계층** , **컴퓨팅 크기** , **스토리지** 및 **백업 보존 기간** 에 대한 기본값입니다. 해당 슬라이더를 그대로 두거나 조정할 수 있습니다. 이 컴퓨팅 및 스토리지 선택 항목을 저장하려면 **저장** 을 선택하여 구성을 계속합니다. 아래 스크린샷은 컴퓨팅 및 스토리지 옵션을 보여 줍니다.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="컴퓨팅 + 스토리지":::

5. 네트워킹 구성 옵션

    네트워킹 탭에서 서버에 연결하는 방법을 선택할 수 있습니다. Azure Database for MySQL 유연한 서버는 *퍼블릭 액세스(허용된 IP 주소)* 및 *프라이빗 액세스(VNet 통합)* 를 통해 서버에 연결하는 두 가지 옵션을 제공합니다. *퍼블릭 액세스(허용되는 IP 주소)* 를 사용하면 서버에 대한 액세스는 방화벽 규칙에 추가된 허용된 IP 주소를 제한합니다. 특정 IP 주소 또는 범위에 대한 방화벽을 열기 위한 규칙을 만들지 않는 한, 이 방화벽은 외부 애플리케이션과 도구에서 서버 및 서버의 모든 데이터베이스에 연결하는 것을 방지합니다. *프라이빗 액세스(VNet 통합)* 를 사용하면 서버에 대한 액세스는 가상 네트워크로 제한됩니다. 이 빠른 시작에서는 서버에 연결하기 위해 퍼블릭 액세스를 사용하도록 설정하는 방법을 보여 줍니다. [개념 문서](./concepts-networking.md)의 연결 방법에 대해 자세히 알아보세요.

    > [!NOTE]
    > 서버를 만든 후에는 연결 방법을 변경할 수 없습니다. 예를 들어 만드는 동안 *퍼블릭 액세스(허용된 IP 주소)* 를 선택한 경우 만든 후에 *프라이빗 액세스(VNet 통합)* 로 변경할 수 없습니다. VNet 통합을 사용하여 서버에 안전하게 액세스하려면 프라이빗 액세스 권한이 있는 서버를 만드는 것이 좋습니다. [개념 문서](./concepts-networking.md)에서 프라이빗 액세스에 대해 자세히 알아보세요.

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="네트워킹 구성":::  

6. **검토 + 만들기** 를 선택하여 유연한 서버 구성을 검토합니다.

7. **만들기** 를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

8. 배포 프로세스를 모니터링하려면 도구 모음에서 **알림** (벨 아이콘)을 클릭합니다. 배포가 완료되면 **대시보드에 고정** 을 선택할 수 있습니다. 그러면 Azure Portal 대시보드에서 이 유연한 서버에 대한 타일이 서버의 **개요** 페이지에 대한 바로 가기로 생성됩니다. **리소스로 이동** 옵션을 선택하면 서버의 **개요** 페이지가 열립니다.

기본적으로 서버 아래에 **information_schema** , **mysql** , **performance_schema** 및 **sys** 데이터베이스가 만들어집니다.

> [!NOTE]
> 네트워크에서 연결 문제를 방지하기 위해 Azure Database for MySQL 유연한 서버에서 사용하는 포트 3306을 통한 아웃바운드 트래픽을 허용하는지 확인합니다.  

## <a name="connect-to-using-mysql-command-line-client"></a>mysql 명령줄 클라이언트를 사용하여 연결

*프라이빗 액세스(VNet 통합)* 를 사용하여 유연한 서버를 만든 경우 서버와 동일한 VNet 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고 유연한 서버를 사용하여 만든 VNet에 추가할 수 있습니다.

*퍼블릭 액세스(허용되는 IP 주소)* 를 사용하여 유연한 서버를 만든 경우 서버의 방화벽 규칙 목록에 로컬 IP 주소를 추가할 수 있습니다.

[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 또는 [MySQL 워크벤치](./connect-workbench.md)를 선택하여 로컬 환경에서 서버에 연결할 수 있습니다. 

mysql.exe를 사용하면 다음 명령으로 연결합니다. 값을 실제 서버 이름 및 암호로 바꿉니다. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>리소스 정리
리소스 그룹에서 Azure Database for MySQL 유연한 서버를 성공적으로 만들었습니다.  이러한 리소스가 나중에 필요하지 않을 경우에는 리소스 그룹을 삭제하거나 MySQL 서버만 삭제하여 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **리소스 그룹** 을 검색하고 선택합니다.
1. 리소스 그룹 목록에서 리소스 그룹의 이름을 선택합니다.
1. 리소스 그룹의 개요 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
1. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

서버를 삭제하려면 아래와 같이 서버의 **개요** 페이지에서 **삭제** 단추를 클릭하면 됩니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="리소스 삭제":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [MySQL로 PHP(Laravel) 웹앱 빌드](tutorial-php-database-app.md)