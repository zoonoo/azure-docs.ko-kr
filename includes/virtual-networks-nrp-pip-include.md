## 공용 IP 주소
공용 IP 주소 리소스는 예약된 인터넷 연결 IP 주소 또는 동적 인터넷 연결 IP 주소를 제공합니다. 독립 실행형 개체로 공용 IP 주소를 만들 수 있지만 실제로 주소를 사용하려면 다른 개체에 연결해야 합니다. 부하 분산 장치, 응용 프로그램 게이트웨이 또는 NIC에 공용 IP 주소를 연결하여 해당 리소스에 대한 인터넷 액세스를 제공할 수 있습니다.

|속성|설명|샘플 값|
|---|---|---|
|**publicIPAllocationMethod**|IP 주소가 *고정* 또는 *동적*인지를 정의합니다.|고정, 동적|
|**idleTimeoutInMinutes**|유휴 시간 초과를 정의합니다. 기본값은 4분입니다. 이 시간 안에 지정된 세션에 대한 추가 패킷이 수신되지 않을 경우 세션이 종료됩니다.|4와 30 사이의 임의 값|
|**ipAddress**|개체에 할당된 IP 주소입니다. 읽기 전용 속성입니다.|104\.42.233.77|

### DNS 설정
공용 IP 주소는 다음과 같은 속성이 포함된 **dnsSettings**라는 자식 개체를 포함합니다.

|속성|설명|샘플 값|
|---|---|---|
|**domainNameLabel**|이름 확인에 사용되는 호스트 이름입니다.|www, ftp, vm1|
|**fqdn**|공용 IP에 대한 정규화된 이름입니다.|www.westus.cloudapp.azure.com|
|**reverseFqdn**|IP 주소로 확인되고 DNS에 PTR 레코드로 등록되는 정규화된 도메인 이름입니다.|www.contoso.com.|

JSON 형식의 샘플 공용 IP 주소:

	{
	   "name": "PIP01",
	   "location": "North US",
	   "tags": { "key": "value" },
	   "properties": {
	      "publicIPAllocationMethod": "Static",
	      "idleTimeoutInMinutes": 4,
		  "ipAddress": "104.42.233.77",
	      "dnsSettings": {
	         "domainNameLabel": "mylabel",
			 "fqdn": "mylabel.westus.cloudapp.azure.com",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

### 추가 리소스

- [공용 IP 주소](../articles/virtual-network/virtual-networks-reserved-public-ip.md)에 대해 자세히 알아보세요.
- [인스턴스 수준 공용 IP](../articles/virtual-network/virtual-networks-instance-level-public-ip.md) 주소에 대해 알아봅니다.
- 공용 IP 주소에 대한 [REST API 참조 설명서](https://msdn.microsoft.com/library/azure/mt163638.aspx)를 읽으십시오.

<!---HONumber=AcomDC_0323_2016-->