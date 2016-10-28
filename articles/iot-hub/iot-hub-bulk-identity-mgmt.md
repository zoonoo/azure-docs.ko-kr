<properties
 pageTitle="IoT Hub 장치 id의 내보내기 가져오기 | Microsoft Azure"
 description="IoT Hub 장치 id의 대량 관리를 위한 개념 및.NET 코드 조각"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="07/19/2016"
 ms.author="dobett"/>

# IoT Hub 장치 ID의 대량 관리

각 IoT Hub에는 진행 중인 클라우드-장치 메시지를 포함 하는 큐와 같은 서비스에 장치 단위 리소스를 만드는 데 사용하는 장치 ID 레지스트리가 있습니다. 또한 장치 지향 끝점에 대한 액세스를 허용합니다. 이 문서에서는 장치 ID 레지스트리에 대해 장치 ID를 대량으로 가져오고 내보내는 방법을 설명합니다.

가져오기 및 내보내기 작업은 사용자가 IoT Hub에 대해 대량 서비스 작업을 실행할 수 있는 *작업* 상황에서 이루어집니다.

**RegistryManager** 클래스는 **Job** 프레임워크를 사용하는 **ExportDevicesAsync** 및 **ImportDevicesAsync** 메서드를 포함합니다. 이러한 메서드를 사용하여 전체 IoT Hub 장치 레지스트리를 내보내고, 가져오고, 동기화할 수 있습니다.

## 작업은 무엇입니까?

장치 ID 레지스트리 작업은 다음 작업을 할 때 **작업** 시스템을 사용합니다.

*  표준 런타임 작업에 비해 실행 시간이 깁니다. 또는
*  사용자에게 많은 양의 데이터를 반환합니다.

이러한 경우 단일 API 호출을 기다리거나 작업 결과가 있을 때 차단하는 대신에 작업은 해당 IoT Hub에 대한 **작업**을 만들고 나서 즉시 **JobProperties** 개체를 반환합니다.

다음 C# 코드 조각은 작업을 만드는 방법을 보여 줍니다.

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

이때 **RegistryManager** 클래스를 사용하면 반환된 **JobProperties** 메타데이터를 사용하는 **작업**의 상태를 쿼리할 수 있습니다.

다음 C# 코드 조각은 매 5초마다 폴링하여 작업이 실행을 마쳤는지 여부를 확인하는 방법을 보여 줍니다.

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## 내보내기 장치

**ExportDevicesAsync** 메서드를 사용하여 [공유 액세스 서명](https://msdn.microsoft.com/library/ee395415.aspx)을 사용하는 [Azure 저장소](https://azure.microsoft.com/documentation/services/storage/) blob 컨테이너에 전체 IoT hub 장치 레지스트리를 내보냅니다.

이 메서드를 사용하면 사용자가 제어하는 blob에 장치 정보의 신뢰할 수 있는 백업을 만들 수 있습니다.

**ExportDevicesAsync** 메서드에 매개변수 두 개를 지정해야 합니다.

*  blob 컨테이너의 URI가 포함된 *문자열*. 이 URI는 컨테이너에 대한 쓰기 액세스 권한을 부여하는 SAS 토큰을 포함해야 합니다. 작업은 직렬화된 내보내기 장치 데이터를 저장하기 위해 이 컨테이너에 블록 blob를 만듭니다. SAS 토큰은 이러한 사용 권한을 포함해야 합니다.
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  내보내기 데이터에서 인증 키를 제외하려는지 여부를 나타내는 *부울* 값입니다. **false**인 경우 인증 키가 내보내기 출력에 포함되지 않고 그렇지 않으면 키는 **null**로 내보내집니다.

다음 C# 코드 조각은 내보내기 데이터에 장치 인증 키를 포함하고 있는 내보내기 작업을 시작한 다음 완료를 폴링하는 방법을 보여 줍니다.

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

작업은 출력을 제공된 blob 컨테이너에 이름 **devices.txt**의 블록 blob로 저장합니다. 출력 데이터는 JSON 직렬화된 장치 데이터로 구성되며 한 장치가 한 줄에 표시됩니다.

다음은 출력 데이터의 예제입니다.

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

코드에서 이 데이터에 액세스해야 하는 경우 **ExportImportDevice** 클래스를 사용하여 이 데이터를 쉽게 역직렬화할 수 있습니다. 다음 C# 코드 조각은 이전에 블록 blob에 내보낸 장치 정보를 읽는 방법을 보여 줍니다.

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  **RegistryManager** 클래스의 **GetDevicesAsync** 메서드를 사용하여 장치의 목록을 가져올 수도 있습니다. 그러나 이 방법은 반환되는 장치 개체의 수에 1000의 하드 캡이 적용됩니다. **GetDevicesAsync** 메서드에 대해 예상되는 사용 사례는 디버깅을 돕는 개발 시나리오에 대한 것이며 생산 워크로드에는 권장되지 않습니다.

## 장치 가져오기

**RegistryManager** 클래스의 **ImportDevicesAsync** 메서드를 사용하여 IoT Hub 장치 레지스터에서 대량 가져오기 및 동기화 작업을 수행할 수 있습니다. **ExportDevicesAsync** 메서드와 마찬가지로 **ImportDevicesAsync** 메서드도 **작업** 프레임워크를 사용합니다.

장치 ID 레지스트리에 새 장치를 프로비전할 뿐만 아니라 기존 장치를 업데이트 및 삭제할 수도 있으므로 **ImportDevicesAsync** 메서드를 사용할 때 주의해야 합니다.

> [AZURE.WARNING]  가져오기 작업은 실행 취소할 수 없습니다. 장치 ID 레지스트리를 대량 변경하기 전에 언제나 **ExportDevicesAsync** 메서드를 사용하여 기존 데이터를 다른 blob 컨테이너에 백업해야 합니다.

**ImportDevicesAsync** 메서드에 매개 변수 두 개를 선택합니다.

*  작업에 대한 *입력*으로 [Azure 저장소](https://azure.microsoft.com/documentation/services/storage/) blob 컨테이너의 URI가 포함된 *문자열*. 이 URI는 컨테이너에 대한 읽기 액세스 권한을 부여하는 SAS 토큰을 포함해야 합니다. 이 컨테이너는 장치 ID 레지스트리에 가져오기 위해 직렬화된 장치 데이터가 포함된 **devices.txt** 이름의 blob를 포함하고 있어야 합니다. 가져오기 데이터는 **ExportImportDevice** 작업이 **devices.txt** Blob을 생성할 때 사용하는 것과 같은 JSON 형식의 장치 정보를 포함해야 합니다. SAS 토큰은 이러한 사용 권한을 포함해야 합니다.

    ```
    SharedAccessBlobPermissions.Read
    ```

*  작업에서 *출력*으로 [Azure 저장소](https://azure.microsoft.com/documentation/services/storage/) blob 컨테이너의 URI가 포함된 *문자열*. 작업은 이 컨테이너에 완료된 가져오기 **작업**에서 나온 오류 정보를 저장하기 위한 블록 blob를 생성합니다. SAS 토큰은 이러한 사용 권한을 포함해야 합니다.
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  두 매개 변수가 같은 blob 컨테이너를 가리킬 수 있습니다. 단순히 별도 매개 변수를 사용하여 출력 컨테이너에 추가 사용 권한이 필요할 때 데이터를 더 강력하게 제어할 수 있습니다.

다음 C# 코드 조각은 가져오기 작업을 시작하는 방법을 보여 줍니다.

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## 가져오기 동작

**ImportDevicesAsync** 메서드를 사용하여 장치 ID 레지스트리에서 다음 대량 작업을 수행할 수 있습니다.

-   새 장치 대량 등록
-   기존 장치의 대량 삭제
-   대량으로 상태 변경(장치를 사용 또는 사용하지 않도록 설정)
-   새 장치 인증 키의 대량 할당
-   장치 인증 키의 대량 자동 다시 생성

단일 **ImportDevicesAsync** 호출 내에서 위의 작업의 조합을 수행할 수 있습니다. 예를 들어 새 장치를 등록하는 동시에 기존 장치를 삭제 또는 업데이트할 수 있습니다. **ExportDevicesAsync** 메서드와 함께 사용하는 경우 모든 장치를 한 IoT Hub에서 다른 IoT Hub로 완전히 마이그레이션할 수 있습니다.

각 장치에 대한 가져오기 직렬화 데이터에 선택적 **importMode** 속성을 사용하여 가져오기 프로세스를 장치별로 제어할 수 있습니다. **importMode** 속성에 다음과 같은 옵션이 있습니다.

| importMode | 설명 |
| -------- | ----------- |
| **createOrUpdate** | 지정된 **ID**를 가진 장치가 존재하지 않는 경우, 새로 등록됩니다. <br/>장치가 이미 존재하는 경우 **ETag** 값과 관계 없이 제공된 입력 데이터가 기존 정보를 덮어씁니다. |
| **create** | 지정된 **ID**를 가진 장치가 존재하지 않는 경우, 새로 등록됩니다. <br/>장치에 이미 존재하는 경우 오류가 로그 파일에 기록됩니다. |
| **update** | 지정된 **ID**를 가진 장치가 이미 존재하는 경우 **ETag** 값과 관계 없이 제공된 입력 데이터가 기존 정보를 덮어씁니다. <br/>장치가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **updateIfMatchETag** | 지정된 **ID**를 가진 장치가 이미 존재하는 경우 **ETag**가 일치하는 경우에만 제공된 입력 데이터가 기존 정보를 덮어씁니다. <br/>장치가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. <br/>**ETag**가 일치하지 않는 경우 불일치 오류가 로그 파일에 기록됩니다. |
| **createOrUpdateIfMatchETag** | 지정된 **ID**를 가진 장치가 존재하지 않는 경우, 새로 등록됩니다. <br/>장치가 이미 존재하는 경우 **ETag**가 일치하는 경우에만 제공된 입력 데이터가 기존 정보를 덮어씁니다. <br/>**ETag**가 일치하지 않는 경우 불일치 오류가 로그 파일에 기록됩니다. |
| **delete** | 지정된 **ID**를 가진 장치가 이미 존재하는 경우, **ETag** 값과 관계 없이 삭제됩니다. <br/>장치가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **deleteIfMatchETag** | 지정된 **ID**를 가진 장치가 이미 존재하는 경우, **ETag**가 일치하는 경우에만 삭제됩니다. 장치가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. <br/>ETag가 일치하지 않는 경우 불일치 오류가 로그 파일에 기록됩니다. |

> [AZURE.NOTE] 직렬화 데이터가 장치에 대한 **importMode** 플래그를 명시적으로 정의하는 경우 가져오기 작업 중에 기본적으로 **createOrUpdate**를 가정합니다.

## 장치 가져오기 예제 – 대량 장치 프로비저닝 

다음 C# 코드 샘플은 인증 키를 포함한 여러 장치 ID를 생성하고 해당 장치 정보를 Azure 저장소 블록 blob에 쓴 다음 장치를 장치 ID 레지스트리에 가져오는 방법을 보여 줍니다.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the Azure storage blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same storage blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## 장치 가져오기 예제 – 대량 삭제

다음 코드 샘플은 이전 코드 샘플을 사용하여 추가한 장치를 삭제하는 방법을 보여 줍니다.

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same storage blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## 컨테이너 SAS URI 가져오기


다음 코드 샘플은 blob 컨테이너에 대한 읽기, 쓰기 및 삭제 사용 권한을 가진 [SAS URI](../storage/storage-dotnet-shared-access-signature-part-2.md)를 생성하는 방법을 보여 줍니다.

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## 다음 단계

이 문서에서는 IoT hub의 장치 identity 레지스트리에 대한 대량 작업을 수행하는 방법을 알아보았습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

- [사용 현황 메트릭][lnk-metrics]
- [작업 모니터링][lnk-monitor]
- [IoT Hub에 대한 액세스 관리][lnk-itpro]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [개발자 가이드][lnk-devguide]
- [샘플 UI를 사용하여 장치 관리 탐색][lnk-dmui]
- [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0720_2016-->