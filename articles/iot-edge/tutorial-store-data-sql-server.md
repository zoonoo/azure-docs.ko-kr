---
title: Azure IoT Edge SQL 모듈을 사용하여 데이터 저장 | Microsoft Docs
description: SQL Server 모듈을 사용하여 IoT Edge 장치에서 데이터를 로컬로 저장하는 방법 알아보기
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: e10c80f658a7297a6642e34d8f5d0abe13b0881d
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748190"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>자습서: SQL Server 데이터베이스로 에지에 데이터 저장

Azure IoT Edge 및 SQL Server를 사용하여 에지에 데이터를 저장하고 쿼리합니다. Azure IoT Edge는 장치가 오프라인 상태인 경우 메시지를 캐시하는 기본 저장소 기능으로 기본 제공된 다음, 연결이 다시 설정되면 해당 메시지를 전달합니다. 그러나 데이터를 로컬로 쿼리하는 등 고급 저장소 기능을 추가할 수 있습니다. IoT Edge 장치는 로컬 데이터베이스를 통합하여 IoT Hub에 대한 연결을 유지 관리하지 않고 더 복잡한 계산을 수행할 수 있습니다. 예를 들어 기계 학습 모델을 개선할 수 있도록 데이터가 클라우드에 한 달에 한 번만 업로드되었더라도 현장 기술자는 컴퓨터에서 로컬로 최근 몇 일 간의 센서 데이터를 시각화할 수 있습니다.

이 문서에서는 SQL Server 데이터베이스를 IoT Edge 장치에 배포하는 지침을 제공합니다. Azure Functions는 IoT Edge 장치에서 실행되며, 들어오는 데이터를 구조화한 다음, 데이터베이스로 보냅니다. 이 문서의 단계는 컨테이너에서 작동하는 다른 데이터베이스(예: MySQL 또는 PostgreSQL)에도 적용할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 Azure 함수 만들기
> * IoT Edge 장치에 SQL 데이터베이스 배포
> * Visual Studio Code를 사용하여 모듈 빌드 및 IoT Edge 장치에 배포
> * 생성된 데이터 보기

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 장치:

* [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)의 빠른 시작에 설명된 단계에 따라 개발 머신 또는 가상 머신을 Edge 장치로 사용할 수 있습니다.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 

개발 리소스:

* [Visual Studio Code](https://code.visualstudio.com/) 
* Visual Studio Code에 대한 [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* Visual Studio Code에 대한 [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기
이 자습서에서는 VS Code용 Azure IoT Edge 확장을 사용하여 모듈을 빌드하고 파일에서 **컨테이너 이미지**를 만듭니다. 그런 후 이미지를 저장하고 관리하는 **레지스트리**에 이 이미지를 푸시합니다. 마지막으로 IoT Edge 장치에서 실행되도록 레지스트리의 이미지를 배포합니다.  

이 자습서에서는 Docker 호환 레지스트리를 사용할 수 있습니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 자습서에서는 Azure Container Registry를 사용합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **컨테이너** > **Container Registry**를 선택합니다.

    ![컨테이너 레지스트리 만들기](./media/tutorial-deploy-function/create-container-registry.png)

2. 레지스트리에 이름을 지정하고 구독을 선택합니다.
3. 리소스 그룹의 경우 IoT Hub를 포함하는 동일한 리소스 그룹 이름을 사용하는 것이 좋습니다. 동일한 그룹에 모든 리소스를 배치하여 함께 관리할 수 있습니다. 예를 들어 테스트에 사용된 리소스 그룹을 삭제하면 해당 그룹에 포함된 모든 리소스가 삭제됩니다. 
4. SKU를 **기본**으로 설정하고 **관리 사용자**를 **사용**으로 설정/해제합니다. 
5. **만들기**를 클릭합니다.
6. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리로 이동하고 **액세스 키**를 선택합니다. 
7. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사합니다. 자습서의 뒷부분에서 이러한 값을 사용합니다. 

## <a name="create-a-function-project"></a>함수 프로젝트 만들기

데이터를 데이터베이스에 보내려면 데이터를 제대로 구성한 다음, 테이블에 저장할 수 있는 모듈이 필요합니다. 

다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 IoT Edge 함수를 만드는 방법을 보여 줍니다.

1. Visual Studio Code를 엽니다.
2. **보기** > **터미널**을 차례로 선택하여 VS Code 통합 터미널을 엽니다.
3. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다.
4. 명령 팔레트에서 **Azure: 로그인** 명령을 입력 및 실행하고, 지침에 따라 Azure 계정에 로그인합니다. 이미 로그인한 경우 이 단계는 건너뛸 수 있습니다.
3. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트에서 다음 정보를 제공하여 솔루션을 만듭니다. 
   1. 솔루션을 만들 폴더를 선택합니다. 
   2. 솔루션에 대한 이름을 제공하거나 기본 **EdgeSolution**을 그대로 적용합니다.
   3. **Azure Functions - C#** 을 모듈 템플릿으로 선택합니다. 
   4. 모듈의 이름을 **sqlFunction**으로 지정합니다. 
   5. 이전 섹션에서 만든 Azure Container Registry를 첫 번째 모듈에 대한 이미지 리포지토리로 지정합니다. **localhost:5000**을 복사한 로그인 서버 값으로 바꿉니다. 마지막 문자열은 **\<레지스트리 이름\>.azurecr.io/sqlFunction**과 같습니다.

4. VS Code 창에서 IoT Edge 솔루션 작업 영역을 로드합니다. 여기에는 **modules** 폴더, **.vscode** 폴더 및 배포 매니페스트 템플릿 파일이 있습니다. **모듈** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx**를 엽니다.

5. 파일 내용을 다음 코드로 바꿉니다.

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
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
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
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
   ```

6. 줄 24에서 문자열 **\<sql 연결 문자열\>** 을 다음 문자열로 바꿉니다. **데이터 원본** 속성은 SQL Server 컨테이너 이름을 참조합니다. 이 항목은 다음 섹션에서 **SQL** 이름을 사용하여 만듭니다. 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. **run.csx** 파일을 저장합니다. 

## <a name="add-a-sql-server-container"></a>SQL Server 컨테이너 추가

[배포 매니페스트](module-composition.md)는 IoT Edge 런타임이 IoT Edge 장치에 설치할 모듈을 선언합니다. 이전 섹션에서 사용자 지정된 함수 모듈을 만드는 코드를 추가했지만 SQL Server 모듈이 이미 구성되어 있습니다. IoT Edge 런타임에서 해당 모듈을 포함한 다음에 다음 장치에서 구성하도록 알리기만 하면 됩니다. 

1. Visual Studio Code 탐색기에서 **deployment.template.json** 파일을 엽니다. 
2. **moduleContent.$edgeAgent.properties.desired.modules** 섹션을 찾습니다. 시뮬레이션된 데이터를 생성하는 **tempSensor** 및 **sqlFunction** 모듈 등 두 개의 모듈이 나열되어야 합니다.
3. Windows 컨테이너를 사용하는 경우 **sqlFunction.settings.image** 섹션을 수정합니다.
    ```json
    "image": "${MODULES.sqlFunction.windows-amd64}"
    ```
4. 다음 코드를 추가하여 세 번째 모듈을 선언합니다. sqlFunction 섹션 뒤에 쉼표를 추가하고 다음을 삽입합니다.

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "settings": {
           "image": "",
           "createOptions": ""
       }
   }
   ```

   다음은 JSON 요소를 추가하는 데 혼란이 있는 경우의 예제입니다. ![SQL 서버 컨테이너 추가](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. IoT Edge 장치의 Docker 컨테이너 유형에 따라 **sql.settings** 매개 변수를 다음 코드로 업데이트합니다.

   * Windows 컨테이너:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux 컨테이너:

      ```json
      "image": "mcr.microsoft.com/mssql/server:latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >프로덕션 환경에서 SQL Server 컨테이너를 만들 때마다 [기본 시스템 관리자 암호를 변경](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password)해야 합니다.

6. **deployment.template.json** 파일을 저장합니다.

## <a name="build-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드

이전 섹션에서는 하나의 모듈을 사용하여 솔루션을 만든 다음, 배포 매니페스트 템플릿에 다른 솔루션을 추가했습니다. 이제 솔루션을 빌드하고, 모듈에 대한 컨테이너 이미지를 만들고, 컨테이너 레지스트리에 이미지를 푸시해야 합니다. 

1. .env 파일에서 모듈 이미지에 액세스할 수 있도록 IoT Edge 런타임에 레지스트리 자격 증명을 제공합니다. **CONTAINER_REGISTRY_USERNAME** 및 **CONTAINER_REGISTRY_PASSWORD** 섹션을 찾고 등호 기호 뒤에 자격 증명을 삽입합니다. 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourContainerReg=<username>
   CONTAINER_REGISTRY_PASSWORD_yourContainerReg=<password>
   ```

2. .env 파일을 저장합니다.
3. 이미지를 레지스트리에 푸시할 수 있도록 Visual Studio Code에서 컨테이너 레지스트리에 로그인합니다. .env 파일에 추가한 것과 동일한 자격 증명을 사용합니다. 통합 터미널에서 다음 명령을 입력합니다.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    암호를 입력하라는 메시지가 표시됩니다. 프롬프트에 암호를 붙여넣고 **Enter** 키를 누릅니다.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

4. VS Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다. 

## <a name="deploy-the-solution-to-a-device"></a>장치에 솔루션 배포

IoT Hub를 통해 장치에서 모듈을 설정할 수 있지만 Visual Studio Code를 통해 IoT Hub 및 장치에 액세스할 수도 있습니다. 이 섹션에서는 IoT Hub에 대한 액세스 권한을 설정한 다음, VS Code를 사용하여 IoT Edge 장치에 솔루션을 배포합니다. 

1. VS Code 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택**을 선택합니다.
2. 표시되는 메시지에 따라 Azure 계정에 로그인합니다. 
3. 명령 팔레트에서 Azure 구독을 선택한 다음, IoT Hub를 선택합니다. 
4. VS Code 탐색기에서 **Azure IoT Hub 장치** 섹션을 펼칩니다. 
5. 배포에서 대상으로 지정하려는 장치를 마우스 오른쪽 단추로 클릭하고, **단일 장치 배포 만들기**를 선택합니다. 
6. 파일 탐색기에서는 솔루션 내의 **config** 폴더로 이동하고, **deployment.json**을 선택합니다. **에지 배포 매니페스트 선택**을 클릭합니다. 

배포가 성공하는 경우 VS Code 출력에 확인 메시지가 출력됩니다. 모든 모듈이 장치에서 실행되는지를 확인할 수도 있습니다. 

IoT Edge 장치에서 다음 명령을 실행하여 모듈의 상태를 확인합니다. 몇 분이 걸릴 수 있습니다.

   ```PowerShell
   iotedge list
   ```

## <a name="create-the-sql-database"></a>SQL 데이터베이스 만들기

장치에 배포 매니페스트를 적용할 때 세 개의 모듈이 실행됩니다. tempSensor 모듈은 시뮬레이션된 환경 데이터를 생성합니다. sqlFunction 모듈은 데이터를 선택하고 데이터베이스에 대한 형식을 지정합니다. 

이 섹션에서는 온도 데이터를 저장하도록 SQL 데이터베이스를 설정하는 방법을 설명합니다. 

1. 명령줄 도구에서도 데이터베이스에 연결합니다. 
   * Windows 컨테이너:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Linux 컨테이너: 

      ```bash
      docker exec -it sql bash
      ```

2. SQL 명령 도구를 엽니다.
   * Windows 컨테이너:

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Linux 컨테이너: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. 데이터베이스를 만듭니다. 

   * Windows 컨테이너
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Linux 컨테이너
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

   ![로컬 데이터 보기](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>리소스 정리

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 장치와 동일한 IoT Edge 장치를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치에서 생성된 원시 데이터를 필터링하는 코드가 포함된 Azure Functions 모듈을 만들었습니다. 고유한 모듈을 빌드할 준비가 되면 [Visual Studio Code에 대한 Azure IoT Edge를 사용하여 Azure Functions를 개발](how-to-develop-csharp-function.md)하는 방법에 대해 자세히 알아볼 수 있습니다. 

Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [C# 코드를 사용하여 센서 데이터 필터링](tutorial-csharp-module.md)