---
title: 자습서 - SQL 모듈을 사용하여 데이터 저장 - Azure IoT Edge | Microsoft Docs
description: SQL Server 모듈을 사용하여 IoT Edge 디바이스에서 데이터를 로컬로 저장하는 방법 알아보기
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 86aab19eb0203e75fb8586adbdeb3f6fff9d14bd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575434"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>자습서: SQL Server 데이터베이스로 에지에 데이터 저장

Azure IoT Edge를 실행하는 Linux 디바이스에 데이터를 저장하는 SQL Server 모듈을 배포합니다.

Azure IoT Edge 및 SQL Server를 사용하여 에지에 데이터를 저장하고 쿼리합니다. Azure IoT Edge는 디바이스가 오프라인 상태인 경우 메시지를 캐시한 후, 연결이 다시 설정될 때 전달하는 기본 스토리지 기능을 제공합니다. 그러나 데이터를 로컬로 쿼리하는 등 고급 저장소 기능을 추가할 수 있습니다. IoT Edge 디바이스는 로컬 데이터베이스를 사용하여 IoT Hub에 대한 연결을 유지 관리하지 않고 더 복잡한 컴퓨팅을 수행할 수 있습니다. 

이 문서에서는 SQL Server 데이터베이스를 IoT Edge 디바이스에 배포하는 지침을 제공합니다. Azure Functions는 IoT Edge 디바이스에서 실행되며, 들어오는 데이터를 구조화한 다음, 데이터베이스로 보냅니다. 이 문서의 단계는 컨테이너에서 작동하는 다른 데이터베이스(예: MySQL 또는 PostgreSQL)에도 적용할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 Azure 함수 만들기
> * IoT Edge 장치에 SQL 데이터베이스 배포
> * Visual Studio Code를 사용하여 모듈 빌드 및 IoT Edge 장치에 배포
> * 생성된 데이터 보기

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하려면 이전 자습서를 진행하여 Linux 컨테이너 개발을 위한 개발 환경이 설정되어 있어야 합니다. [Linux 디바이스를 위한 IoT Edge 모듈을 개발합니다](tutorial-develop-for-linux.md). 이 자습서를 완료하여 다음과 같은 필수 구성 요소를 갖추어야 합니다. 

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge를 실행하는 Linux 디바이스](quickstart-linux.md)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)와 같은 컨테이너 레지스트리
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 사용하여 구성된 [Visual Studio Code](https://code.visualstudio.com/)
* Linux 컨테이너를 실행하도록 구성된 [Docker CE](https://docs.docker.com/install/)

이 자습서에서는 SQL Server에 데이터를 보내도록 Azure Functions 모듈을 사용합니다. Azure Functions를 사용하여 IoT Edge 모듈을 개발하려면 다음과 같은 추가 필수 구성 요소를 개발 머신에 설치합니다. 

* Visual Studio Code에 대한 [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>함수 프로젝트 만들기

데이터를 데이터베이스에 보내려면 데이터를 제대로 구성한 다음, 테이블에 저장할 수 있는 모듈이 필요합니다. 

### <a name="create-a-new-project"></a>새 프로젝트 만들기

다음 단계에서는 Visual Studio Code 및 Azure IoT Tools를 사용하여 IoT Edge Node.js 함수를 만드는 방법을 보여 줍니다.

1. Visual Studio Code를 엽니다.

2. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다.

3. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트에서 다음 정보를 제공하여 솔루션을 만듭니다. 

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | **SqlSolution**과 같은 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값을 적용합니다. |
   | 모듈 템플릿 선택 | **Azure Functions - C#** 을 선택합니다. |
   | 모듈 이름 제공 | 모듈의 이름을 **sqlFunction**으로 지정합니다. |
   | 모듈의 Docker 이미지 리포지토리 제공 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 마지막 단계에서 미리 채워져 있습니다. **localhost:5000**을 Azure 컨테이너 레지스트리의 로그인 서버 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. <br><br>마지막 문자열은 \<레지스트리 이름\>.azurecr.io/sqlFunction과 같습니다. |

   VS Code 창에서 IoT Edge 솔루션 작업 영역을 로드합니다. 
   
### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

환경 파일은 컨테이너 레지스트리의 자격 증명을 저장하고 IoT Edge 런타임과 공유합니다. 이러한 자격 증명은 런타임에서 프라이빗 이미지를 IoT Edge 디바이스로 가져오기 위해 필요합니다.

1. VS Code 탐색기에서 .env 파일을 엽니다.
2. 필드를 Azure 컨테이너 레지스트리에서 복사한 **사용자 이름** 및 **암호** 값으로 업데이트합니다.
3. 이 파일을 저장합니다.

### <a name="select-your-target-architecture"></a>대상 아키텍처 선택

현재, Visual Studio Code에서는 Linux AMD64 및 Linux ARM32v7 디바이스용 C 모듈을 개발할 수 있습니다. 컨테이너는 아키텍처 유형별로 다르게 빌드되고 실행되므로 각 솔루션에서 대상으로 지정할 대상 아키텍처를 선택해야 합니다. 기본값은 Linux AMD64입니다. 

1. 명령 팔레트를 열고 **Azure IoT Edge: 에지 솔루션용 기본 대상 플랫폼 설정**을 검색하거나 창의 맨 아래에 있는 사이드바에서 바로 가기 아이콘을 선택합니다. 

2. 명령 팔레트의 옵션 목록에서 대상 아키텍처를 선택합니다. 이 자습서에서는 Ubuntu 가상 머신을 IoT Edge 디바이스로 사용할 예정이므로 기본값인 **amd64**를 그대로 둡니다. 

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

1. VS Code 탐색기에서 **모듈** > **sqlFunction** > **sqlFunction.cs**를 엽니다.

2. 파일의 전체 내용을 다음 코드로 바꿉니다.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}         
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}         
       }
   }
   ```

3. 줄 35에서 문자열 **\<sql 연결 문자열\>** 을 다음 문자열로 바꿉니다. **데이터 원본** 속성은 SQL Server 컨테이너를 참조하며, 이 항목은 아직 존재하지 않지만 다음 섹션에서 **SQL**이라는 이름으로 만들게 됩니다. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. **sqlFunction.cs** 파일을 저장합니다. 

5. **sqlFunction.csproj** 파일을 엽니다.

6. 패키지 참조 그룹을 찾고, 새 항목을 추가하여 SqlClient를 포함시킵니다. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. **sqlFunction.csproj** 파일을 저장합니다.

## <a name="add-the-sql-server-container"></a>SQL Server 컨테이너 추가

[배포 매니페스트](module-composition.md)는 IoT Edge 런타임이 IoT Edge 디바이스에 설치할 모듈을 선언합니다. 이전 섹션에서 사용자 지정된 함수 모듈을 만드는 코드를 제공했지만 SQL Server 모듈이 이미 구성되어 Azure Marketplace에서 사용할 수 있습니다. IoT Edge 런타임에서 해당 모듈을 포함한 다음에 다음 디바이스에서 구성하도록 알리기만 하면 됩니다. 

1. Visual Studio Code에서 **보기** > **명령 팔레트**를 차례로 선택하여 명령 팔레트를 엽니다.

2. 명령 팔레트에서 **Azure IoT Edge: IoT Edge 모듈 추가** 명령을 입력하고 실행합니다. 명령 팔레트에서 다음 정보를 제공하여 새 모듈을 추가합니다. 

   | 필드 | 값 | 
   | ----- | ----- |
   | 배포 템플릿 파일 선택 | 명령 팔레트에 현재 솔루션 폴더에 있는 deployment.template.json 파일이 강조 표시됩니다. 이 파일을 선택합니다.  |
   | 모듈 템플릿 선택 | **Azure Marketplace의 모듈**을 선택합니다. |

3. Azure IoT Edge 모듈 마켓플레이스에서 **SQL Server 모듈**을 검색하여 선택합니다. 

4. 모듈 이름을 **sql**(모두 소문자)로 변경합니다. 이 이름은 sqlFunction.cs 파일의 연결 문장열에 선언된 컨테이너 이름과 일치합니다. 

5. **가져오기**를 선택하여 모듈을 솔루션에 추가합니다. 

6. 솔루션 폴더에서 **deployment.template.json** 파일을 엽니다. 

7. **모듈** 섹션을 찾습니다. 세 가지 모듈이 보입니다. *tempSensor* 모듈은 새 솔루션에 기본적으로 포함되며 다른 모듈과 함께 사용할 테스트 데이터를 제공합니다. *sqlFunction* 모듈은 초기에 만들고 새 코드로 업데이트한 모듈입니다. 마지막으로 *sql* 모듈은 Azure Marketplace에서 가져왔습니다. 

   >[!Tip]
   >SQL Server 모듈에는 배포 매니페스트의 환경 변수에 설정된 기본 암호가 제공됩니다. 프로덕션 환경에서 SQL Server 컨테이너를 만들 때마다 [기본 시스템 관리자 암호를 변경](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)해야 합니다.

8. **deployment.template.json** 파일을 닫습니다.

## <a name="build-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드

이전 섹션에서는 하나의 모듈을 사용하여 솔루션을 만든 다음, 배포 매니페스트 템플릿에 다른 솔루션을 추가했습니다. SQL Server 모듈은 Microsoft에서 공개적으로 호스팅되지만 함수 모듈에서 코드를 컨테이너화해야 합니다. 이 섹션에서는, 솔루션을 빌드하고, sqlFunction 모듈에 대한 컨테이너 이미지를 만들고, 이 이미지를 컨테이너 레지스트리에 푸시합니다. 

1. Visual Studio Code에서 **보기** > **터미널**을 선택하여 통합 터미널을 엽니다.  

1. 이미지를 레지스트리에 푸시할 수 있도록 Visual Studio Code에서 컨테이너 레지스트리에 로그인합니다. .env 파일에 추가한 것과 동일한 ACR(Azure Container Registry) 자격 증명을 사용합니다. 통합 터미널에서 다음 명령을 입력합니다.

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    --password-stdin 매개 변수를 사용하도록 권장하는 보안 경고가 표시될 수 있습니다. 이 문서의 범위 외부에서 사용하는 경우 이 모범 사례를 따르는 것이 좋습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 명령 참조를 참조하세요. 

2. VS Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다. 

솔루션을 빌드하도록 Visual Studio Code에 지시하면 먼저 배포 템플릿의 정보를 가져와서 **config**라는 새 폴더에 deployment.json 파일을 생성합니다. 그런 다음, 통합 터미널에서 `docker build` 및 `docker push`, 두 개의 명령을 실행합니다. 이 두 명령은 코드를 빌드하고, 모듈을 컨테이너화한 다음, 솔루션을 초기화할 때 지정한 컨테이너 레지스트리로 코드를 푸시합니다. 

sqlFunction 모듈이 컨테이너 레지스트리에 성공적으로 푸시된 것을 확인할 수 있습니다. Azure Portal에서 컨테이너 레지스트리로 이동합니다. **리포지토리**를 선택하고 **sqlFunction**을 검색합니다. 다른 두 가지 모듈 즉, tempSensor와 sql은 컨테이너 레지스트리에 푸시되지 않습니다. 이미 Microsoft 레지스트리에서 해당 리포지토리를 가리키고 있기 때문입니다.

## <a name="deploy-the-solution-to-a-device"></a>디바이스에 솔루션 배포

IoT Hub를 통해 디바이스에서 모듈을 설정할 수 있지만 Visual Studio Code를 통해 IoT Hub 및 디바이스에 액세스할 수도 있습니다. 이 섹션에서는 IoT Hub에 대한 액세스 권한을 설정한 다음, VS Code를 사용하여 IoT Edge 디바이스에 솔루션을 배포합니다. 

1. VS Code 탐색기에서 **Azure IoT Hub 디바이스** 섹션을 펼칩니다. 

2. 배포에서 대상으로 지정하려는 디바이스를 마우스 오른쪽 단추로 클릭하고, **단일 디바이스 배포 만들기**를 선택합니다. 

3. 파일 탐색기에서는 솔루션 내의 **config** 폴더로 이동하고, **deployment.amd64**를 선택합니다. **에지 배포 매니페스트 선택**을 클릭합니다. 

   deployment.template.json 파일을 배포 매니페스트로 사용하지 마십시오.

배포가 성공하는 경우 VS Code 출력에 확인 메시지가 출력됩니다. 

VS Code의 Azure IoT Hub Devices 섹션에서 디바이스의 상태를 새로 고칩니다. 새 모듈이 나열되고, 컨테이너가 설치되고 시작됨에 따라 다음 몇 분 동안 '실행 중'이라고 보고되기 시작합니다. 모든 모듈이 디바이스에서 실행되는지를 확인할 수도 있습니다. IoT Edge 장치에서 다음 명령을 실행하여 모듈의 상태를 확인합니다. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>SQL 데이터베이스 만들기

장치에 배포 매니페스트를 적용할 때 세 개의 모듈이 실행됩니다. tempSensor 모듈은 시뮬레이션된 환경 데이터를 생성합니다. sqlFunction 모듈은 데이터를 선택하고 데이터베이스에 대한 형식을 지정합니다. 이 섹션에서는 온도 데이터를 저장하도록 SQL 데이터베이스를 설정하는 방법을 설명합니다. 

IoT Edge 디바이스에서 다음 명령을 실행합니다. 이러한 명령은 디바이스에서 실행 중인 **sql** 모듈에 연결하고, 해당 모듈에 전송되는 온도 데이터를 보관할 데이터베이스와 테이블을 만듭니다. 

1. IoT Edge 디바이스의 명령줄 도구에서 데이터베이스에 연결합니다. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. SQL 명령 도구를 엽니다.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. 데이터베이스를 만듭니다. 
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. 테이블을 정의합니다.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

SQL Server가 여러 IoT Edge 장치에 배포되도록 자동으로 설정하도록 SQL Server Docker 파일을 사용자 지정할 수 있습니다. 자세한 내용은 [Microsoft SQL Server 컨테이너 데모 프로젝트](https://github.com/twright-msft/mssql-node-docker-demo-app)를 참조하세요. 

## <a name="view-the-local-data"></a>로컬 데이터 보기

테이블을 만들면 sqlFunction 모듈이 IoT Edge 장치의 로컬 SQL Server 2017 데이터베이스에 데이터를 저장하기 시작합니다. 

SQL 명령 도구 내에서 다음 명령을 실행하여 형식이 지정된 테이블 데이터를 봅니다. 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![로컬 데이터베이스의 내용 보기](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>리소스 정리

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 장치와 동일한 IoT Edge 장치를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치에서 생성된 원시 데이터를 필터링하는 코드가 포함된 Azure Functions 모듈을 만들었습니다. 고유한 모듈을 빌드할 준비가 되면 [Visual Studio Code에 대한 Azure IoT Edge를 사용하여 Azure Functions를 개발](how-to-develop-csharp-function.md)하는 방법에 대해 자세히 알아볼 수 있습니다. 

에지에서 다른 스토리지 메서드를 시도하려는 경우 IoT Edge의 Azure Blob Storage를 사용하는 방법을 읽어보세요. 

> [!div class="nextstepaction"]
> [IoT Edge에서 Azure Blob Storage를 사용하여 에지에 데이터 저장](how-to-store-data-blob.md)
