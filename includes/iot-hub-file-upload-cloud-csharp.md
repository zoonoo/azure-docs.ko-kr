## Azure 저장소 계정 프로비전
시뮬레이트된 장치는 Azure 저장소 Blob에서 파일을 업로드하므로 Azure 저장소 계정이 있어야 합니다. 기존 계정을 사용하거나 [Azure 저장소 정보]의 지침에 따라 새 계정을 만들 수 있습니다. 저장소 계정 연결 문자열을 기록해 둡니다.

## 시뮬레이트된 장치에 Azure Blob URI 보내기

이 섹션에서는 공유 액세스 서명을 통해 Azure Blob URI를 포함시키도록 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기]에서 만든 **SendCloudtoDevice** 콘솔 앱을 수정합니다. 그러면 클라우드 백 엔드가 클라우드-장치 메시지 수신자에게만 Blob에 대한 쓰기 권한을 부여할 수 있습니다.

1. Visual Studio에서 **SendCloudtoDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...**를 클릭합니다. 

    그러면 NuGet 패키지 관리 창이 표시됩니다.

2. `WindowsAzure.Storage`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.

    그러면 [Microsoft Azure 저장소 SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)가 다운로드 및 설치되고 해당 참조가 추가됩니다.

3. **Program.cs** 파일 맨 위에 다음 문을 추가합니다.

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. **Program** 클래스에서 다음 클래스 필드를 추가하여 저장소 계정에 대한 연결 문자열을 대체합니다.

        static string storageConnectionString = "{storage connection string}";

    이제 다음 메서드(Blob 컨테이너 이름 대신 사용할 수 있으며 이 자습서는 **iothubfileuploadtutorial**을 사용함)를 추가합니다.
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    이 메서드는 [Blob 서비스를 통해 SAS 만들기 및 사용](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-2/)에 설명되어 있는 대로 새 Blob 참조를 만들고 공유 액세스 서명 URI를 생성합니다. 위의 메서드는 24시간 동안 유효한 서명 URI를 생성합니다. 대상 장치가 파일을 업로드하는 데 시간이 더 필요할 경우(예를 들어 자주 연결하지 않거나 큰 파일을 업로드하기에 연결이 안정적이지 않은 경우) 해당 서명에 더 기간이 긴 만료 시간을 고려할 수 있습니다.

5. 다음과 같은 방식으로 **SendCloudToDeviceMessageAsync**를 수정합니다.

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    이 메서드는 2개의 응용 프로그램 속성을 포함하는 클라우드-장치 메시지를 전송합니다. 하나는 이 메시지를 명령으로 식별하여 파일을 업로드하고 다른 하나는 Blob URI를 보유합니다. 또한 전체 배달 승인을 요청합니다. 메시지 본문에서 두 응용 프로그램 속성의 정보를 직렬화할 수 있지만 해당 정보를 직렬화 및 역직렬화하려면 추가 처리가 발생합니다.

<!-- Links -->

[Azure 저장소 정보]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/en-us/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!---HONumber=Oct15_HO3-->