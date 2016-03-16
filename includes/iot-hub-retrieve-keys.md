## IoT Hub 키 검색

새 IoT Hub에 대한 인증 키를 표시합니다.

1. Program.cs에 다음 메서드를 추가합니다.

    ```
    static void ShowIoTHubKeys(ResourceManagementClient client, string token)
    {
    
    }
    ```

2. 인증 키를 콘솔에 인쇄하려면 다음 코드를 **ShowIoTHubKeys** 메서드에 추가합니다.

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
        new AuthenticationHeaderValue("Bearer", token);
    var httpsRepsonse = client.HttpClient.PostAsync(
        string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}/listKeys?api-version=2015-08-15-preview", 
        subscriptionId, rgName, iotHubName),
        null).Result;
    
    Console.WriteLine("Keys: {0}, 
        httpsRepsonse.Content.ReadAsStringAsync().Result);
    ```

<!---HONumber=AcomDC_1203_2015-->