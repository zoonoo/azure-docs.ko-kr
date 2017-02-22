### <a name="troubleshoot-azure-diagnostics"></a>Azure Diagnostics 문제 해결

다음과 같은 오류 메시지를 받으면 Microsoft.insights 리소스 공급자가 등록되지 않습니다.

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

리소스 공급자를 등록하려면 Azure Portal에서 다음 단계를 수행합니다.

1.    왼쪽의 탐색 창에서 *구독* 클릭
2.    오류 메시지에서 식별된 구독 선택
3.    *리소스 공급자* 클릭
4.    *Microsoft.insights* 공급자 찾기
5.    *등록* 링크 클릭

![Microsoft.insights 리소스 공급자 등록](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

*Microsoft.insights* 리소스 공급자가 등록되면 진단 구성을 다시 시도하세요.


<!--HONumber=Feb17_HO2-->


