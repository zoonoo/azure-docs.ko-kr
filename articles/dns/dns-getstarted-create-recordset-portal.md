<properties
   pageTitle="Azure 포털을 사용하여 DNS 영역에 대한 레코드 집합 및 레코드 만들기 | Microsoft Azure"
   description="Azure DNS에 대한 호스트 레코드를 만들고 Azure 포털을 사용하여 레코드 집합 및 레코드를 만드는 방법"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>



# Azure 포털을 사용하여 DNS 레코드 집합 및 레코드 만들기


> [AZURE.SELECTOR]
- [Azure 포털](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


이 문서는 Azure 포털을 사용하여 레코드 및 레코드 집합을 만드는 과정을 안내합니다. DNS 영역을 만든 후 도메인에 대한 DNS 레코드를 추가합니다. 이 작업을 수행하려면 먼저 DNS 레코드 및 레코드 집합을 이해해야 합니다.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## 레코드 집합 및 레코드 만들기

다음 예제에서는 Azure 포털을 사용하여 레코드 집합 및 레코드를 만드는 과정을 안내합니다. 여기에서는 DNS “A” 레코드 유형을 사용합니다.

1. 포털에 로그인합니다.

2. 레코드 집합을 만들려는 **DNS 영역** 블레이드로 이동합니다.

3. **DNS 영역** 블레이드의 위쪽에서 **레코드 집합**을 클릭하여 **레코드 집합 추가** 블레이드를 엽니다.

	![새 레코드 집합](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. **레코드 집합 추가** 블레이드에서 레코드 집합의 이름을 지정합니다. 예를 들어 레코드 집합의 이름을 "**www**"로 지정할 수 있습니다.

	![레코드 집합 추가](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 만들려는 레코드 유형을 선택합니다. 예를 들어 **A**를 선택합니다.

6. **TTL**을 설정합니다. 포털에서 라이브되는 기본 시간은 1시간입니다.

7. 한 줄당 하나씩 IP 주소를 추가합니다. 앞서 제안된 레코드 집합 이름 및 레코드 유형을 사용할 경우 www 레코드 집합에 대해 **A** 레코드에 IPv4 IP 주소가 추가됩니다.

8. IP 주소 추가가 끝나면 블레이드 맨 아래에서 **확인**을 선택합니다. DNS 레코드 집합이 생성됩니다.


## 다음 단계

레코드 집합과 레코드를 관리하려면 [Azure 포털을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-portal.md)를 참조하세요.

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0817_2016-->