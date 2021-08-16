---
title: Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포
description: Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: a9208101777cd88f0237e661a414550759a069b0
ms.sourcegitcommit: aaba99b8b1c545ad5d19f400bcc2d30d59c63f39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2021
ms.locfileid: "108007384"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포

SAP 솔루션을 위한 Azure Monitor 리소스는 [Azure Portal](https://azure.microsoft.com/features/azure-portal)을 통해 만들 수 있습니다. 이 방법은 SAP 솔루션을 위한 Azure Monitor를 배포하고 구성 공급자를 구성하는 브라우저 기반 사용자 인터페이스를 제공합니다.

## <a name="sign-in-to-azure-portal"></a>Azure Portal에 로그인

https://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-monitoring-resource"></a>모니터링 리소스 만들기

1. **Azure Marketplace** 에서 **SAP 솔루션을 위한 Azure Monitor** 를 선택합니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Azure Marketplace에서 SAP 솔루션을 위한 Azure Monitor 제품 선택을 보여 주는 이미지" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. **기본 사항** 탭에서 필요한 값을 제공합니다. 해당하는 경우 기존 Log Analytics 작업 영역을 사용할 수 있습니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Azure Portal 구성 옵션을 표시합니다." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. 가상 네트워크를 선택하는 경우 해당 VNET 내에서 모니터링할 시스템에 연결할 수 있는지 확인합니다. 

   > [!IMPORTANT]
   > Microsoft와 데이터 공유를 위해 **공유** 를 선택하면 지원 팀이 추가 지원을 제공할 수 있습니다.

## <a name="configure-providers"></a>공급자 구성

### <a name="sap-netweaver-provider"></a>SAP NetWeaver 공급자

#### <a name="prerequisites-for-adding-netweaver-provider"></a>NetWeaver 공급자를 추가하기 위한 필수 조건

“SAP 시작 서비스”는 SAP 시스템 모니터링을 비롯한 서비스의 호스트를 제공합니다. 이 기능을 공개하는 SOAP 웹 서비스 인터페이스인 “SAPControl”을 이용하고 있습니다. 이 SAPControl 웹 서비스 인터페이스는 [보호된 및 보호되지 않는](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) 웹 서비스 메서드를 구별합니다. 특정 메트릭을 페치하려면 일부 메서드의 보호를 해제해야 합니다. 현재 릴리스에 필요한 메서드를 보호 해제하려면 각 SAP 시스템에 대해 다음 단계를 수행하세요.

1. SAP 서버에 대한 SAP GUI 연결 열기
2. 관리 계정을 사용하여 로그인
3. 트랜잭션 RZ10 실행
4. 적절한 프로필 선택(DEFAULT.PFL)
5. ‘확장된 유지 관리’를 선택하고 [변경] 클릭 
6. “SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList”에 대한 영향을 받는 매개 변수 “service/protectedwebmethods” 값을 권장 설정으로 수정하고 [복사] 클릭
7. 돌아가서 [프로필] -> [저장] 선택
8. 매개 변수를 적용하려면 시스템 다시 시작

>[!Tip]
> ACL(액세스 제어 목록)을 사용하여 서버 포트에 대한 액세스를 필터링합니다. 해당 [SAP Note](https://launchpad.support.sap.com/#/notes/1495075)를 참조하세요.

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>Azure Portal에 NetWeaver 공급자 설치
1.  필수 조건 단계가 완료되고 서버가 다시 시작되었는지 확인
2.  Azure Portal의 AMS 아래에서 [공급자 추가]를 선택하고 드롭다운에서 SAP NetWeaver 선택
3.  SAP 시스템 및 하위 도메인의 호스트 이름 입력(해당하는 경우)
4.  입력한 호스트 이름에 해당하는 인스턴스 번호 입력 
5.  SID(시스템 ID) 입력
6.  완료되면 [공급자 추가] 선택
7.  필요에 따라 다른 공급자를 계속 추가하거나 [검토 + 만들기]를 선택하여 배포 완료

![이미지](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

### <a name="sap-hana-provider"></a>SAP HANA 공급자 

1. **공급자** 탭을 선택하여 구성하려는 공급자를 추가합니다. 여러 공급자를 차례로 추가하거나 모니터링 리소스를 배포한 후에 추가할 수 있습니다. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="SAP 솔루션을 위한 Azure Monitor에 공급자를 추가하는 공급자 탭을 표시합니다." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. **공급자 추가** 를 선택하고 드롭다운에서 **SAP HANA** 를 선택합니다. 

   > [!IMPORTANT]
   > SAP HANA 공급자가 SAP HANA ‘master’ 노드에 대해 구성되어 있는지 확인합니다.

3. HANA 서버의 개인 IP를 입력합니다.

4. 만들려는 데이터베이스 테넌트의 이름을 입력합니다. 모든 테넌트를 선택할 수 있지만, 더 광범위한 모니터링 영역을 사용할 수 있도록 **SYSTEMDB** 를 사용하는 것이 좋습니다. 

5. HANA 데이터베이스와 연결된 SQL 포트 번호를 입력합니다. 포트 번호는 **[3]**  +  **[instance#]**  +  **[13]** 형식이어야 합니다. 예: 30013. 

6. 사용하려는 데이터베이스 사용자 이름을 입력합니다. 데이터베이스 사용자에게 **모니터링** 및 **카탈로그 읽기** 역할이 할당되어 있는지 확인합니다. 

7. 완료되면 **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나 **검토 + 만들기** 를 선택하여 배포를 완료합니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="공급자 정보를 추가할 때 구성 옵션의 이미지" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::
   
### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server 공급자

1. Microsoft SQL Server 공급자를 추가하기 전에 SQL Server Management Studio에서 다음 스크립트를 실행하여 공급자를 구성하는 데 필요한 적절한 권한을 가진 사용자를 만들어야 합니다.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. **공급자 추가** 를 선택하고 드롭다운에서 **Microsoft SQL Server** 를 선택합니다. 

3. Microsoft SQL Server와 연결된 정보를 사용하여 필드를 채웁니다. 

4. 완료되면 **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나 **검토 + 만들기** 를 선택하여 배포를 완료합니다.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Microsoft SQL Server 공급자 추가와 관련된 정보를 보여 주는 이미지" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>고가용성 클러스터(Pacemaker) 공급자

1. 드롭다운에서 **고가용성 클러스터(Pacemaker)** 를 선택합니다. 

   > [!IMPORTANT]
   > 고가용성 클러스터(Pacemaker) 공급자를 구성하려면 ha_cluster_provider가 각 노드에 설치되어 있는지 확인합니다. 자세한 내용은 [HA 클러스터 내보내기](https://github.com/ClusterLabs/ha_cluster_exporter#installation)를 참조하세요.

2. http://IP:9664/metrics 형식의 Prometheus 엔드포인트를 입력합니다. 
 
3. SID(시스템 ID), 호스트 이름 및 클러스터 이름을 입력합니다.

4. 완료되면 **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나 **검토 + 만들기** 를 선택하여 배포를 완료합니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="HA 클러스터 Pacemaker 공급자와 관련된 옵션을 보여 주는 이미지" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::

### <a name="os-linux-provider"></a>OS(Linux) 공급자 

1. 드롭다운에서 OS(Linux) 선택 

   >[!IMPORTANT]
   > OS(Linux) 공급자를 구성하려면 모니터링하려는 각 호스트(BareMetal 또는 VM)에 최신 버전의 Node_Exporter가 설치되어 있는지 확인합니다. [Node Exporter의 최신 버전](https://prometheus.io/download/#node_exporter)을 설치하세요. 자세한 내용은 [자세히 알아보기](https://github.com/prometheus/node_exporter)를 참조하세요.

2. BareMetal 인스턴스의 식별자가 될 이름을 입력합니다.
3. http://IP:9100/metrics 형식으로 노드 내보내기 엔드포인트를 입력합니다.

   >[!IMPORTANT]
   >Linux 호스트의 개인 IP 주소를 사용하세요. 호스트 및 AMS 리소스가 동일한 VNET에 있는지 확인하세요. 

   >[!Note]
   > 방화벽 포트 “9100”이 Linux 호스트에서 열려야 합니다.
   >firewall-cmd를 사용하는 경우: firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload. ufw를 사용하는 경우: ufw allow 9100/tcp ufw reload

    >[!Tip]
    > Linux 호스트가 Azure VM인 경우 해당하는 모든 NSG가 “VirtualNetwork”의 포트 9100에서 인바운드 트래픽을 원본으로 허용하는지 확인하세요.
 
5. 완료되면  **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나  **검토 + 만들기** 를 선택하여 배포를 완료합니다. 


## <a name="next-steps"></a>다음 단계

[SAP 솔루션을 위한 Azure Monitor](azure-monitor-overview.md)에 관해 자세히 알아봅니다.
