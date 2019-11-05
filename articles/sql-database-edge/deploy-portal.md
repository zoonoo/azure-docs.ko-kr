---
title: Azure Portal를 사용 하 여 SQL Database Edge 미리 보기 배포 Microsoft Docs
description: Azure Portal를 사용 하 여 Azure SQL Database에 지를 배포 하는 방법을 알아봅니다.
keywords: sql database edge 배포
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da756b702c994d69aae42ecef0e2da4d44eed39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514096"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Azure SQL Database Edge 미리 보기 배포

Azure SQL Database Edge 미리 보기는 IoT 및 Azure IoT Edge 배포에 최적화 된 관계형 데이터베이스 엔진입니다. IoT 응용 프로그램 및 솔루션에 대 한 고성능 데이터 저장소 및 처리 계층을 만드는 기능을 제공 합니다. 이 빠른 시작에서는 Azure Portal를 사용 하 여 Azure IoT Edge를 통해 Azure SQL Database Edge 모듈 만들기를 시작 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure 포털](https://portal.azure.com/)에 로그인합니다.
* [여기](https://azure.microsoft.com/services/sql-database-edge/#contact)에서 요청을 제출 하 여 SQL Database Edge 배포를 위해 구독을 사용 하도록 설정 합니다.
* [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md)를 만듭니다.
* [Azure Portal에서 IoT Edge 장치](../iot-edge/how-to-register-device-portal.md)를 등록 합니다.
* [Azure Portal에서 IoT Edge 모듈을 배포할](../iot-edge/how-to-deploy-modules-portal.md)IoT Edge 장치를 준비 합니다.

> [!NOTE]
> Azure Linux VM을 IoT Edge 장치로 배포 하려면이 [빠른 시작 가이드](../iot-edge/quickstart-linux.md)를 참조 하세요.

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Azure Marketplace에서 SQL Database Edge 모듈 배포

Azure Marketplace는 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)을 포함하여 Azure에서 실행되도록 인증되고 최적화된 광범위한 엔터프라이즈 애플리케이션 및 솔루션을 검색할 수 있는 온라인 애플리케이션 및 서비스 마켓플레이스입니다. Azure SQL Database Edge는 marketplace를 통해에 지 장치에 배포할 수 있습니다.

1. Azure Marketplace에서 Azure SQL Database Edge 모듈을 찾습니다.<br><br>

   ![MarketPlace의 SQL Database Edge](media/deploy-portal/find-offer-marketplace.png)

2. 요구 사항에 가장 부합 하는 소프트웨어 계획을 선택 하 고 **만들기**를 클릭 합니다. <br><br>

   ![올바른 소프트웨어 요금제 선택](media/deploy-portal/pick-correct-plan.png)

3. IoT Edge 모듈의 대상 장치 페이지에서 다음 정보를 지정한 다음 **만들기** 를 클릭 합니다.

   |**필드**  |**설명**  |
   |---------|---------|
   |구독  |  IoT Hub를 만든 Azure 구독 |
   |IoT Hub   |  IoT Edge 장치가 등록 된 IoT Hub의 이름이 며 "장치에 배포" 옵션을 선택 합니다.|
   |IoT Edge 장치 이름  |  SQL Database Edge가 배포 되는 IoT Edge 장치의 이름 |

4. **모듈 설정** 페이지에서 배포 모듈의 섹션으로 이동 하 고 SQL Database Edge 모듈에 대해 **구성** 을 클릭 합니다. 

5. **사용자 지정 모듈 IoT Edge** 창에서 환경 변수에 대해 원하는 값을 지정 하 고 모듈에 대 한 만들기 옵션 및 desired 속성을 사용자 지정 합니다. 지원 되는 환경 변수의 전체 목록은 [SQL Server 컨테이너 환경 변수](/sql/linux/sql-server-linux-configure-environment-variables/)를 참조 하세요.

   |**매개 변수**  |**설명**|
   |---------|---------|
   | Name | 모듈의 이름입니다. |
   |SA_PASSWORD  | SQL Database Edge 관리자 계정에 대 한 강력한 암호를 지정 합니다. |
   |MSSQL_LCID   | SQL Server에 사용할 언어 ID를 설정 합니다. 예를 들어 1036는 프랑스어입니다. |
   |MSSQL_COLLATION | SQL Server에 대 한 기본 데이터 정렬을 설정 합니다. 이 설정은 언어 ID (LCID)의 기본 매핑을 데이터 정렬로 재정의 합니다. |

   > [!NOTE]
   > 모듈에서 **이미지 URI** 또는 **ACCEPT_EULA** 설정을 변경 하거나 업데이트 하지 마세요.

6. **사용자 지정 모듈 IoT Edge** 창에서 컨테이너 만들기 옵션 원하는 **호스트 포트** 및 **대상** 의 탑재 지점의 값을 업데이트 합니다. 탑재 지점 대상은 SQL database 파일이 호스트 IoT Edge 장치에 저장 되는 위치입니다.

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

7. **사용자 지정 모듈 IoT Edge** 창에서 SQL 패키지의 위치와 stream analytics 작업 정보를 포함 하도록 *모듈 쌍의 desired 속성* 을 업데이트 합니다. 이러한 두 필드는 선택 사항이 며 데이터베이스와 스트리밍 작업을 사용 하 여 SQL Database Edge 모듈을 배포 하려는 경우에 사용 해야 합니다.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. **사용자 지정 모듈 IoT Edge** 창에서 *다시 시작 정책* 을 항상으로 설정 하 고 *원하는 상태* 를 실행 중으로 설정 합니다.
9. **사용자 지정 모듈 IoT Edge** 창에서 **저장**을 클릭 합니다.
10. **모듈 설정** 페이지에서 **다음**을 클릭 합니다.
11. **모듈 설정** 페이지의 **경로 지정 (선택 사항)** 에서 모듈에 모듈 또는 모듈에 대 한 경로를 지정 하 여 IoT Edge 허브 통신을 참조 하세요. [IoT Edge에서 모듈 배포 및 경로 설정](../iot-edge/module-composition.md)을 참조 하세요.
12. **다음**을 누릅니다.
13. **Submit**를 클릭합니다.

이 빠른 시작에서는 IoT Edge 장치에 SQL Database Edge 모듈을 배포 했습니다.

## <a name="next-steps"></a>다음 단계

- [SQL Database Edge에서 ONNX를 사용 하 여 Machine Learning 및 인공 지능](onnx-overview.md)
- IoT Edge를 사용 하 여 SQL Database Edge를 사용 하는 종단 간 IoT 솔루션 빌드