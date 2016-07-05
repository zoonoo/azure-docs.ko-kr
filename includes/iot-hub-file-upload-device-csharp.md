## Azure 저장소 계정을 IoT Hub에 연결

시뮬레이트된 장치는 Azure 저장소 Blob에서 파일을 업로드하므로 IoT Hub에 Azure 저장소 계정이 연결되어야 합니다. 기존 저장소 계정을 사용하거나 [Azure 저장소 정보]의 지침에 따라 새 계정을 만들 수 있습니다. [Azure 포털을 통해 IoT Hub 관리]의 지침에 따라 Azure 저장소 계정을 IoT Hub에 연결할 수 있습니다.

## 시뮬레이트된 장치에서 파일 업로드

이 섹션에서는 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기]에서 만든 시뮬레이트된 장치 응용 프로그램을 수정하여 IoT Hub에서 클라우드-장치 메시지를 수신합니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **추가**를 클릭하고 **기존 항목**을 클릭합니다. 이미지 파일을 찾아 프로젝트에 포함합니다. 이 자습서에서는 이미지 이름을 `image.jpg`로 지정한다고 가정합니다.

2. 이미지를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. **출력 디렉터리로 복사**가 **항상 복사**로 설정되어 있는지 확인합니다.

    ![][1]

3. **Program.cs** 파일 맨 위에 다음 문을 추가합니다.

        using System.IO;

4. **Program** 클래스에 다음 메서드를 추가합니다.
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    `UploadToBlobAsync` 메서드는 업로드할 파일의 파일 이름 및 스트림 원본을 받은 후 업로드를 저장소로 전달합니다. 콘솔 응용 프로그램은 파일을 업로드하는 데 걸리는 시간을 표시합니다.

5. **Main** 메서드에서 `Console.ReadLine()` 줄 바로 앞에 다음 메서드를 추가합니다.

        SendToBlobAsync();

> [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 저장소 정보]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure 포털을 통해 IoT Hub 관리]: ../articles/iot-hub-manage-through-portal/#file-upload

<!-- Images -->
[1]: ../articles/iot-hub/media/iot-hub-csharp-csharp-file-upload/image-properties.png

<!---HONumber=AcomDC_0622_2016-->