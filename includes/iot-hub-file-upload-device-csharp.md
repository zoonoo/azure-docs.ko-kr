## 시뮬레이트된 장치에서 파일 업로드

이 섹션에서는 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기]에서 만든 시뮬레이트된 장치 응용 프로그램을 수정하여 IoT Hub에서 클라우드-장치 메시지를 수신합니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리...**를 클릭합니다. 

    그러면 NuGet 패키지 관리 창이 표시됩니다.

2. `WindowsAzure.Storage`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.

    그러면 [Microsoft Azure 저장소 SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)가 다운로드 및 설치되고 해당 참조가 추가됩니다.

3. **Program.cs** 파일 맨 위에 다음 문을 추가합니다.

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. **Program** 클래스에서 **ReceiveC2dAsync** 메서드를 다음과 같은 방식으로 변경합니다.
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    이렇게 하면 **ReceiveC2dAsync**가 `FileUpload`로 설정된 `command` 속성을 가진 메시지를 식별하며 이는 **UploadFileToBlobAsync** 메서드로 처리됩니다.

    파일 업로드 명령을 처리하려면 아래 메서드를 추가합니다.
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    이 메서드는 Azure 저장소 SDK를 사용하여 임의로 생성된 10Mb BLOB을 지정된 URI에 업로드합니다. BLOB을 업로드하는 방법에 대한 자세한 내용은 [Azure 저장소 - BLOB을 사용하는 방법]을 참조하세요.

> [AZURE.NOTE] 이렇게 구현된 시뮬레이트된 장치는 BLOB이 업로드된 후에만 클라우드-장치 메시지를 완료합니다. 이 접근 방법은 배달 승인이 처리할 업로드된 파일의 가용성을 나타내기 때문에 백 엔드에서 업로드된 파일의 처리를 단순화합니다. 그러나 [IoT Hub 개발자 가이드][IoT Hub Developer Guide - C2D]에서 설명한 대로 *가시성 타임아웃*(일반적으로 1분) 전에 완료되지 않은 메시지가 장치 큐에 다시 넣어지고 **ReceiveAsync()** 메서드가 메시지를 다시 수신합니다. 파일 업로드에 시간이 오래 걸릴 수 있는 경우에는 시뮬레이트된 장치로 현재 업로드 작업을 지속적으로 저장하는 것이 좋습니다. 이렇게 하면 파일 업로드가 완료되기 전에 시뮬레이트된 장치가 클라우드-장치 메시지를 완료하고 백 엔드 완료를 알리는 장치-클라우드 메시지를 보낼 수 있습니다.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[Azure 저장소 - BLOB을 사용하는 방법]: ../articles/storage/storage-dotnet-how-to-use-blobs.md#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=AcomDC_0413_2016-->