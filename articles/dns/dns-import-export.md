<properties
   pageTitle="Azure DNS으로 도메인 영역 파일 가져오기 및 내보내기 | Microsoft Azure"
   description="Azure CLI를 사용하여 Azure DNS에 DNS 영역 파일을 가져오고 내보내는 방법을 알아봅니다"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="jonatul"/>

# DNS 영역 파일 가져오기 및 내보내기


이 가이드에서는 Azure DNS에서 DNS 영역 파일을 가져오고 내보내는 방법을 보여줍니다.

## DNS 영역 마이그레이션 소개

DNS 영역 파일은 영역의 모든 DNS 레코드의 세부 정보를 포함하는 텍스트 파일입니다. 표준 형식을 따르며 이는 다른 DNS 시스템 간에 DNS 레코드를 전송하는 데 적합하도록 만듭니다. 영역 파일을 사용하는 작업은 DNS 영역을 Azure DNS으로 전송할 수 있는 신뢰할 수 있는 빠르고 편리한 방법입니다.

Azure DNS는 Azure CLI를 통해 영역 파일 가져오기 및 내보내기를 지원합니다. 이 문서에서는 이 기능을 사용하는 방법을 설명합니다.

Azure CLI는 Azure 서비스를 관리하는 데 사용하는 플랫폼 간 명령줄 도구입니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)에서 다운로드하여 Windows, Mac 및 Linux 플랫폼에 사용할 수 있습니다. 가장 일반적인 이름 서버 소프트웨어인 [BIND](https://www.isc.org/downloads/bind/)는 일반적으로 Linux에서 실행하기 때문에 이 플랫폼 간 지원은 영역 파일 가져오기 및 내보내기에 특히 중요합니다.

## 기존 DNS 영역 파일 가져오기

Azure DNS에 DNS 영역 파일을 가져오기 전에 영역 파일의 복사본을 가져와야 합니다. 이 파일의 원본은 DNS 영역이 현재 호스팅되는 위치에 따라 달라집니다. 예:

- DNS 영역이 타사 서비스에서 호스팅되는 경우(예: 도메인 등록자, 전용 DNS 호스팅 서비스 공급자 또는 다른 클라우드 공급자) 해당 서비스는 DNS 영역 파일을 다운로드하는 기능을 제공해야 합니다.
-	Windows DNS 서버를 사용하여 DNS 영역이 호스팅된 경우 기본적으로 영역 파일은 **'%systemroot%\\system32\\dns'** 폴더에 위치할 수 있습니다. 또한 각 영역 파일의 전체 경로는 DNS 서비스 관리 콘솔의 ‘일반’ 탭에 표시됩니다.
-	DNS 영역이 BIND를 사용하여 호스팅되는 경우 각 영역에 대한 영역 파일의 위치는 바인딩 구성 파일 **'named.conf'**에 지정됩니다. 

>[AZURE.NOTE]'GoDaddy'에서 다운로드된 영역 파일은 약간 비표준 형식이며 이는 해당 영역 파일을 Azure DNS에 가져오기 전에 수정되어야 합니다. 특히 각 DNS 레코드의 RDATA에 있는 DNS 이름은 정규화된 이름으로 지정되지만 '.'를 종료하지 않습니다. 즉, 다른 DNS 시스템에서 상대 이름으로 해석된다는 것을 의미합니다. Azure DNS로 가져오기 전에 영역 파일을 편집하여 이러한 이름에 대한 '.'를 종료하도록 추가해야 합니다.

## Azure DNS에 DNS 영역 파일 가져오기

DNS 영역을 가져오는 Azure CLI 명령 형식은 다음과 같습니다. `azure network dns zone import [options] <resource group> <zone name> <zone file name>` 위치:

- **리소스 그룹** - Azure DNS의 영역에 대한 리소스 그룹의 이름입니다.
- **영역 이름** - 영역의 이름입니다.
- **영역 파일 이름** -가져올 영역 파일의 경로/이름입니다.

이 이름이 있는 영역이 리소스 그룹에 없는 경우 생성됩니다. 영역이 이미 있는 경우 가져온 레코드 집합은 기존 레코드 집합으로 병합됩니다. 대신 기존 레코드 집합을 덮어쓰려면 '--force' 옵션을 사용합니다. [레코드 집합을 병합하는 방법](#merging-with-existing-data)에 대한 자세한 내용은 아래와 같습니다.

영역 파일을 실제로 가져오지 않고 영역 파일의 형식을 확인하려면 ‘--parse-only’ 옵션을 사용합니다.

## Azure DNS로 영역 파일을 가져오기 위한 단계별 가이드

예를 살펴보겠습니다. 'contoso.com' 영역에 대한 영역 파일을 가져오려 한다고 가정합니다.

### 1단계
Azure CLI를 사용하여 Azure 구독에 로그인합니다.

	azure login

### 2단계
새 DNS 영역을 만들려는 구독을 선택합니다.

	azure account set <subscription name>

### 3단계
Azure DNS는 Azure 리소스 관리자(ARM) 전용 서비스입니다. Azure CLI는 ARM 모드로 전환되어야 합니다.

	azure config mode arm

### 4단계
Azure DNS 서비스를 사용하기 전에 구독을 등록하여 Microsoft.Network 리소스 공급자를 사용해야 합니다.(각 구독에 한 번만 작업합니다)

	azure provider register Microsoft.Network

### 5단계 
또한 ARM 리소스 그룹이 없는 경우 ARM 리소스 그룹을 만들어야 할 수도 있습니다.
	
	azure group create myresourcegroup westeurope

### 6단계	
리소스 그룹 'myresourcegroup'에서 'contoso.com.txt' 파일에서 새 DNS 영역으로 'contoso.com' 영역을 가져오려면 명령 `azure network dns zone import`을 실행합니다.

	azure network dns zone import myresourcegroup contoso.com contoso.com.txt

이 명령은 영역 파일을 로드하고 구문을 분석하며 Azure DNS 서비스에서 일련의 명령을 실행하여 영역 및 영역의 모든 레코드 집합을 만듭니다.

모든 오류 또는 경고 뿐만 아니라 콘솔 창의 진행 상태를 보고합니다. 레코드 집합이 계열에 만들어지기 때문에 큰 영역 파일을 가져오는 데 몇 분 정도 걸릴 수 있습니다.

## 가져오기 후에 DNS 영역 확인

다음 Azure CLI 명령을 사용하여 레코드를 나열할 수 있습니다.(또한 `Get-AzureRmDnsRecordSet`을 사용하고 PowerShell을 통해 수행할 수 있습니다)

	azure network dns record-set list myresourcegroup contoso.com

또한 'nlookup'를 사용하여 레코드에 대한 이름 솔루션을 확인할 수 있습니다. 영역이 아직 위임되지 않았기 대문에 올바른 Azure DNS 이름 서버를 명시적으로 지정해야 합니다. 아래 샘플은 영역에 할당된 이름 서버 이름을 검색하고 'nslookup'를 사용하여 'www' 레코드를 쿼리하는 방법을 보여줍니다.

	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
	info:    Executing command network dns record-set show
	+ Looking up the DNS Record Set "@" of type "NS"
	data:    Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-01.azure-dns.com
	data:        Name server domain name     : ns2-01.azure-dns.net
	data:        Name server domain name     : ns3-01.azure-dns.org
	data:        Name server domain name     : ns4-01.azure-dns.info
	data:
	info:    network dns record-set show command OK

	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  40.90.4.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
            134.170.188.221

영역을 올바르게 가져왔는지를 확인하면 [DNS 위임을 업데이트](dns-domain-delegation.md)하여 Azure DNS 이름 서버를 가리키도록 해야 합니다.

## 기존 데이터와 병합

영역이 아직 없는 경우 영역 파일을 가져오면 Azure DNS에서 새 영역을 만듭니다. 영역이 이미 있는 경우 영역 파일의 레코드 집합은 기존 레코드 집합으로 병합되어야 합니다. 병합 동작은 다음과 같습니다.

1.	기존 및 새 레코드 집합은 기본적으로 병합됩니다. 병합된 레코드 집합 내의 동일한 레코드는 중복을 제거합니다.
2.	또는 '--force' 옵션을 지정하여 가져오기 프로세스가 기존 레코드 집합을 새 레코드 집합으로 바꿉니다. 가져온 영역 파일에 해당 레코드 집합이 없는 기존 레코드 집합은 제거되지 않습니다.
3.	레코드 집합을 병합하는 경우 기존 레코드 집합의 TTL이 사용됩니다. '--force'가 사용되는 경우 새 레코드 집합의 TTL가 사용됩니다.
4.	SOA 매개 변수('호스트' 제외)는 '--force'의 사용에 관계 없이 항상 가져온 영역 파일에서 가져옵니다. 마찬가지로 영역 광선의 NS 레코드 집합의 경우 TTL은 항상 가져온 영역 파일에서 가져옵니다.
5.	'--force' 매개 변수를 지정하지 않는 한 가져온 CNAME 레코드는 기존 CNAME 레코드를 동일한 이름으로 바꾸지 않습니다.
6.	CNAME 레코드와 이름은 같지만 형식이 다른 레코드 간에 충돌이 발생할 경우(기존 vs 새로 만든 것에 관계 없이) 기존 레코드가 보존됩니다. '--force'의 사용과 별개입니다.

## 추가 기술 세부 정보
다음 정보는 영역 가져오기 프로세스에 대한 추가 기술 세부 정보를 제공합니다.

1.	$TTL 지시문은 선택적이며 지원됩니다. $TTL 지시어를 지정하지 않는 경우 기본 TTL 3600초를 사용하여 명시적 TTL이 없는 레코드를 가져올 수 있습니다. 동일한 레코드 집합의 두 레코드가 다른 TTL을 지정하는 경우 낮은 값이 사용됩니다.
2.	$ORIGIN 지시문은 선택적이며 지원됩니다. $ORIGIN을 설정하지 않는 경우 사용된 기본 값은 명령줄에 지정된 영역 이름입니다.(그리고 '.'를 종료함)
3.	$INCLUDE 및 $GENERATE 지시문은 지원되지 않습니다.
4.	A, AAAA, CNAME, MX, NS, SOA, SRV, TXT 등의 레코드 형식을 지원합니다.  
5.	SOA 레코드는 영역이 만들어질 때 Azure DNS에서 자동으로 생성됩니다. 영역 파일을 가져올 때 '호스트' 매개 변수를 제외한 모든 SOA 매개 변수는 영역 파일에서 가져오며 이는 Azure DNS에서 제공되는 값을 사용합니다. 이 매개 변수가 Azure DNS에서 제공하는 기본 이름 서버를 참조해야 하기 때문입니다.
6.	또한 영역을 만들 때 역영 광선의 NS 레코드 집합은 Azure DNS에서 자동으로 만들어집니다. 이 레코드 집합의 TTL만을 가져옵니다. 이러한 레코드는 Azure DNS에서 제공하는 이름 서버 이름을 포함하며 따라서 레코드 데이터는 가져온 영역 파일에 포함된 값으로 덮어쓰지 않습니다.
7.	공개 미리 보기 동안 Azure DNS는 단일 문자열 TXT 레코드만을 지원하므로 다중 문자열 TXT 레코드는 연결되며 255자로 제한됩니다.

## Azure DNS에서 DNS 영역 파일 내보내기

DNS 영역을 가져오는 Azure CLI 명령 형식은 다음과 같습니다. `azure network dns zone export [options] <resource group> <zone name> <zone file name>` 위치:

- **리소스 그룹** - Azure DNS의 영역에 대한 리소스 그룹의 이름입니다.
- **영역 이름** - 영역의 이름입니다.
- **영역 파일 이름** - 내보낼 영역 파일의 경로/이름입니다.

## Azure DNS 파일을 내보내는 단계
 
영역 가져오기를 사용하여 먼저 로그인하고 구독을 선택하며 Azure CLI를 구성하여 'ARM' 모드를 사용해야 합니다.

### 1단계
Azure CLI를 사용하여 Azure 구독에 로그인합니다.

	azure login

### 2단계
새 DNS 영역을 만들려는 구독을 선택합니다.

	azure account set <subscription name>

### 3단계
Azure DNS는 Azure 리소스 관리자(ARM) 전용 서비스입니다. Azure CLI는 ARM 모드로 전환되어야 합니다.

	azure config mode arm
### 4단계
리소스 그룹 'myresourcegroup'의 기존 Azure DNS 영역 'contoso.com'을 (현재 폴더의)'contoso.com.txt' 파일로 내보내려면 `azure network dns zone export`을 실행합니다.

	azure network dns zone export myresourcegroup contoso.com contoso.com.txt

이 명령은 Azure DNS 서비스를 호출하여 영역에서 레코드 집합을 열거하고 BIND와 호환 가능한 영역 파일에 결과를 내보냅니다.

<!---HONumber=AcomDC_0114_2016-->