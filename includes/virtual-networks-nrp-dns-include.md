## <a name="azure-dns"></a>Azure DNS
Azure DNS는 DNS 도메인에 대한 호스팅 서비스로, Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다.

| 속성 | 설명 | 샘플 값 |
| --- | --- | --- |
| **DNSzones** |특정 도메인의 DNS 레코드를 호스트할 도메인 영역 정보입니다. |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com" |

### <a name="dns-record-sets"></a>DNS 레코드 집합
DNS 영역에는 레코드 집합이라고 하는 자식 개체가 있습니다. 레코드 집합은 DNS 영역에 대한 유형별 호스트 레코드 모음입니다. 레코드 유형은 A, AAAA, CNAME, MX, NS, SOA, SRV 및 TXT입니다.

| 속성 | 설명 | 샘플 값 |
| --- | --- | --- |
| 문자열(UTF-8 형식) 또는 |IPv4 레코드 유형 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA |IPv6 레코드 유형 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME |Canonical 이름 레코드 유형 <sup>1</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX |메일 레코드 유형 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS |이름 서버 레코드 유형 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA |권한 시작 레코드 종류 <sup>2</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV |서비스 레코드 유형  |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> 레코드 집합당 하나의 값만 허용합니다.

<sup>2</sup> DNS 영역당 하나의 레코드 종류 SOA만 허용합니다. 

Json 형식의 DNS 영역 샘플:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "newZoneName": {
          "type": "String",
          "metadata": {
              "description": "The name of the DNS zone to be created."
          }
        },
        "newRecordName": {
          "type": "String",
          "defaultValue": "www",
          "metadata": {
              "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
          }
        }
      },
      "resources": 
      [
        {
          "type": "microsoft.network/dnszones",
          "name": "[parameters('newZoneName')]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": {
          }
        },
        {
          "type": "microsoft.network/dnszones/a",
          "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": 
          {
            "TTL": 3600,
            "ARecords": 
            [
                {
                    "ipv4Address": "1.2.3.4"
                },
                {
                    "ipv4Address": "1.2.3.5"
                }
            ]
          },
          "dependsOn": [
            "[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
          ]
        }
          ]
    }

## <a name="additional-resources"></a>추가 리소스
자세한 내용은 [DNS 영역에 대한 REST API 설명서 ](https://msdn.microsoft.com/library/azure/mt130626.aspx) 를 참조하세요.

자세한 내용은 [DNS 레코드 집합에 대한 REST API 설명서](https://msdn.microsoft.com/library/azure/mt130627.aspx) 를 참조하세요.

