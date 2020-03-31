---
title: Azure 포털을 사용하여 SQL 데이터베이스 에지 미리 보기 배포 | 마이크로 소프트 문서
description: Azure 포털을 사용하여 Azure SQL Database Edge를 배포하는 방법 알아보기
keywords: SQL 데이터베이스 에지 배포
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246724"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Azure SQL 데이터베이스 에지 미리 보기 배포

Azure SQL Database 가장자리 미리 보기는 IoT 및 Azure IoT 에지 배포에 최적화된 관계형 데이터베이스 엔진입니다. IoT 애플리케이션 및 솔루션을 위한 고성능 데이터 스토리지 및 처리 계층을 만들 수 있는 기능을 제공합니다. 이 빠른 시작은 Azure 포털을 사용하여 Azure IoT Edge를 통해 Azure SQL Database Edge 모듈을 만드는 방법을 보여 주며, 이를 통해 Azure SQL Database Edge 모듈을 만드는 방법을 보여 주며, 이를 통해 Azure SQL Database Edge 모듈을 만드는 방법을 보여 주며, 이를 통해 Azure SQL Database Edge 모듈을 만드는 방법을 보여 주며, 이를 통해 Azure SQL Database Edge모듈을 만드는 방법을 보여 주시면 합니다

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 없는 경우 [무료 계정을](https://azure.microsoft.com/free/)만듭니다.
* [Azure 포털에](https://portal.azure.com/)로그인합니다.
* [여기에](https://azure.microsoft.com/services/sql-database-edge/#contact)요청을 제출하여 SQL Database Edge 배포에 대한 구독을 사용하도록 설정합니다.
* Azure [IoT 허브를](../iot-hub/iot-hub-create-through-portal.md)만듭니다.
* Azure [포털에서 IoT 에지 장치를](../iot-edge/how-to-register-device-portal.md)등록합니다.
* [Azure 포털에서 IoT Edge 모듈을 배포할 수 있도록 IoT](../iot-edge/how-to-deploy-modules-portal.md)Edge 장치를 준비합니다.

> [!NOTE]
> Azure Linux VM을 IoT Edge 장치로 배포하려면 이 [빠른 시작 가이드를](../iot-edge/quickstart-linux.md)참조하십시오.

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Azure 마켓플레이스에서 SQL 데이터베이스 에지 모듈 배포

Azure Marketplace는 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)을 포함하여 Azure에서 실행되도록 인증되고 최적화된 광범위한 엔터프라이즈 애플리케이션 및 솔루션을 검색할 수 있는 온라인 애플리케이션 및 서비스 마켓플레이스입니다. Azure SQL Database Edge는 마켓플레이스를 통해 에지 장치에 배포할 수 있습니다.

1. Azure 마켓플레이스에서 Azure SQL 데이터베이스 에지 모듈을 찾습니다.<br><br>

   ![마켓플레이스의 SQL 데이터베이스 에지](media/deploy-portal/find-offer-marketplace.png)

2. 요구 사항에 가장 적합한 소프트웨어 계획을 선택하고 **만들기를**클릭합니다. <br><br>

   ![올바른 소프트웨어 계획 선택](media/deploy-portal/pick-correct-plan.png)

3. IoT Edge 모듈의 대상 장치 페이지에서 다음 세부 정보를 지정한 다음 **만들기를** 클릭합니다.

   |**필드**  |**설명**  |
   |---------|---------|
   |Subscription  |  IoT Hub가 만들어진 Azure 구독 |
   |IoT Hub   |  IoT Edge 장치가 등록된 IoT Hub 의 이름을 선택한 다음 "장치에 배포" 옵션을 선택합니다.|
   |IoT 에지 장치 이름  |  SQL 데이터베이스 에지가 배포될 IoT Edge 장치의 이름 |

4. 모듈 **설정** 페이지에서 배포 모듈의 섹션으로 이동하여 SQL Database Edge 모듈에 대해 **구성을** 클릭합니다. 

5. **IoT Edge 사용자 지정 모듈** 창에서 환경 변수에 대해 원하는 값을 지정하거나 모듈에 대한 만들기 옵션 및 원하는 속성을 사용자 지정합니다. 지원되는 환경 변수의 전체 목록은 [SQL Server 컨테이너 환경 변수를](/sql/linux/sql-server-linux-configure-environment-variables/)참조하십시오.

   |**매개 변수**  |**설명**|
   |---------|---------|
   | 이름 | 모듈의 이름입니다. |
   |SA_PASSWORD  | SQL Database Edge 관리자 계정에 대한 강력한 암호를 지정합니다. |
   |MSSQL_LCID   | SQL Server에 사용할 언어 ID를 설정합니다. 예를 들어 1036은 프랑스어입니다. |
   |MSSQL_COLLATION | SQL Server에 대한 기본 데이터 정렬을 설정합니다. 이 설정은 언어 ID(LCID)의 기본 매핑을 데이터 정렬에 재정의합니다. |

   > [!NOTE]
   > **이미지 URI** 또는 모듈의 **ACCEPT_EULA** 설정을 변경하거나 업데이트하지 마십시오.

6. **IoT Edge 사용자 지정 모듈** 창에서 컨테이너를 업데이트하여 **호스트 포트에**대해 원하는 값을 만듭니다. 둘 이상의 SQL DB Edge 모듈을 배포해야 하는 경우 마운트 옵션을 업데이트하여 영구 볼륨에 대한 새 소스 & 대상 쌍을 만들어야 합니다. 마운트 및 볼륨에 대한 자세한 내용은 docker 설명서의 [볼륨 사용을](https://docs.docker.com/storage/volumes/) 참조하십시오. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. **IoT Edge 사용자 지정 모듈** 창에서 SQL 패키지의 위치와 스트림 분석 작업 정보를 포함하도록 설정 모듈 *트윈의 원하는 속성을* 업데이트합니다. 이 두 필드는 선택 사항이며 데이터베이스 및 스트리밍 작업을 사용하여 SQL Database Edge 모듈을 배포하려는 경우 사용해야 합니다.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. IoT Edge 사용자 지정 모듈 창에서 *다시 시작 정책을* 항상 설정하고 원하는 *상태를* 실행하도록 **설정합니다.**
9. **IoT Edge 사용자 지정 모듈** 창에서 **저장을**클릭합니다.
10. 모듈 설정 페이지에서 **다음**을 **클릭합니다.**
11. **모듈 설정** 페이지의 **경로 지정(선택 사항)에서** 모듈에서 모듈 또는 모듈에 대한 모듈 또는 모듈 경로를 IoT Edge Hub 통신으로 지정하려면 모듈 배포 및 [IoT Edge에서 경로 설정](../iot-edge/module-composition.md)참조.
12. **다음**을 클릭합니다.
13. **제출**을 클릭합니다.

이 빠른 시작에서는 IoT Edge 장치에 SQL 데이터베이스 에지 모듈을 배포했습니다.

## <a name="next-steps"></a>다음 단계

- [SQL 데이터베이스 에지에서 ONNX를 사용하여 기계 학습 및 인공 지능](onnx-overview.md).
- IoT 에지를 사용하여 SQL 데이터베이스 에지로 엔드 투 엔드 IoT 솔루션을 구축합니다.
