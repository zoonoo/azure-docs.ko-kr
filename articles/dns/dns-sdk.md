<properties 
   pageTitle=".NET SDK를 사용하여 Azure DNS에 대한 DNS 영역 및 레코드 집합 만들기 | Microsoft Azure" 
   description=".NET SDK를 사용하여 Azure DNS에 대한 DNS 영역 및 레코드 집합을 만드는 방법." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/16/2016"
   ms.author="cherylmc"/>


# .NET SDK를 사용하여 DNS 영역 및 레코드 집합 만들기

.NET DNS 관리 라이브러리와 함께 DNS SDK를 사용하여 DNS 영역, 레코드 집합 및 레코드를 만들거나 삭제하거나 업데이트하는 작업을 자동화할 수 있습니다. 전체 Visual Studio 프로젝트는 [여기](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)서 사용할 수 있습니다.

## NuGet 패키지 및 네임스페이스 선언

DNS 클라이언트를 사용하려면 **Azure DNS 관리 라이브러리** NuGet 패키지를 설치하고 프로젝트에 DNS 관리 네임스페이스를 추가해야 합니다.
 
1. **Visual Studio**에서 프로젝트 또는 새 프로젝트를 엽니다.

2. **도구** **>** **NuGet 패키지 관리자** **>** **패키지 관리자 콘솔**로 이동합니다.

3. Azure DNS 관리 라이브러리를 다운로드합니다.

		using Microsoft.Azure;
		using Microsoft.Azure.Management.Dns;
		using Microsoft.Azure.Management.Dns.Models;

## DNS 관리 클라이언트 초기화

*DnsManagementClient*에는 DNS 영역 및 레코드 집합 관리에 필요한 메서드 및 속성이 들어 있습니다. 클라이언트가 구독에 액세스할 수 있으려면, 올바른 사용 권한을 설정하고 AWT 토큰을 생성해야 합니다. 자세한 내용은[Azure Resource Manager 요청 인증](https://msdn.microsoft.com/library/azure/dn790557.aspx)을 참조하세요.

	// get a token for the AAD application (see the article link above for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## DNS 영역 만들기 또는 업데이트

DNS 영역을 만들려면 "Zone" 개체를 만들어 *dnsClient.Zones.CreateOrUpdate*로 전달합니다. DNS 영역은 특정 지역에 연결되어 있지 않기 때문에 위치가 "전역"으로 설정됩니다.<BR> Azure DNS는 [Etag](dns-getstarted-create-dnszone.md)라는 낙관적 동시성을 지원합니다. "Etag"는 Zone의 속성입니다. "IfNoneMatch"는 ZoneCreateOrUpdateParameters에 포함된 속성입니다.

	// To create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);



## DNS 레코드 및 레코드 집합 만들기 또는 업데이트

DNS 레코드는 레코드 집합으로 관리됩니다. 레코드 집합은 영역 내에서 동일한 이름과 레코드 형식을 가진 레코드의 집합입니다. 레코드 집합을 만들거나 업데이트하려면 "RecordSet" 개체를 만들어서 *dnsClient.RecordSets.CreateOrUpdate*로 전달합니다. 레코드 집합 이름은 정규화된 DNS 이름과 반대로 영역 이름을 기준으로 합니다. 위치는 "전역"으로 설정됩니다.<BR> Azure DNS는 [Etag](dns-getstarted-create-dnszone.md)라는 낙관적 동시성을 지원합니다. "Etag"는 RecordSet의 속성입니다. "IfNoneMatch"는 RecordSetCreateOrUpdateParameters에 포함된 속성입니다.
    


	// To create record sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
## 영역 및 레코드 집합 가져오기

*Zones* 및 *RecordSets* 컬렉션은 각각 영역 및 레코드 집합을 가져오는 기능을 제공합니다. RecordSets은 해당 형식, 이름 및 속해 있는 영역(및 리소스 그룹)으로 식별됩니다. 영역은 해당 이름 및 속해 있는 리소스 그룹으로 식별됩니다.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

## 영역 및 레코드 집합 나열

영역을 나열하려면 영역 컬렉션의 *List* 메서드를 사용합니다. 레코드 집합을 나열하려면 RecordSets 컬렉션의 *List* 또는 *ListAll* 메서드를 사용합니다. *List* 메서드는 지정된 형식의 레코드 집합만 반환한다는 점에서 *ListAll* 메서드와 다릅니다.

다음 예제는 DNS 영역 및 레코드 집합 목록을 가져오는 방법을 보여줍니다.


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}


## 다음 단계

[Visual Studio SDK 샘플 프로젝트](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=AcomDC_0817_2016-->