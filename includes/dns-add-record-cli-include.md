#### <a name="create-an-a-record-set-with-single-record"></a>단일 레코드가 포함된 A 레코드 집합 만들기

레코드 집합을 만들려면 `azure network dns record-set create`을 사용합니다. 리소스 그룹, 영역 이름, 레코드 집합의 상대적 이름, 레코드 형식, TTL(Time to Live)을 지정합니다.

```azurecli
    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300
```

A 레코드 집합을 만든 후에 `azure network dns record-set add-record`를 사용하여 레코드 집합에 IPv4 주소를 추가합니다.

```azurecli
    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1
```

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>단일 레코드가 포함된 AAAA 레코드 집합 만들기

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"
```

#### <a name="create-a-cname-record-set-with-a-single-record"></a>단일 레코드가 포함된 CNAME 레코드 집합 만들기

CNAME 레코드에는 하나의 단일 문자열 값만 허용됩니다.

```azurecli
    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"
```

#### <a name="create-an-mx-record-set-with-a-single-record"></a>단일 레코드가 포함된 MX 레코드 집합 만들기

이 예제에서는 레코드 집합 이름을 "@"(으)로 사용하여 영역 구로에 MX 레코드를 만듭니다(이 경우 "contoso.com"). MX 레코드에 공통됩니다.

```azurecli
    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5
```

#### <a name="create-an-ns-record-set-with-a-single-record"></a>단일 레코드가 포함된 NS 레코드 집합 만들기

```azurecli
    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"
```

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>단일 레코드가 포함된 PTR 레코드 집합 만들기

이 경우 'my-arpa-zone.com'은 IP 범위를 나타내는 ARPA 영역을 나타냅니다.  이 영역의 각 PTR 레코드 집합은 IP 범위 내의 IP 주소에 해당합니다.

```azurecli
    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"
```

#### <a name="create-an-srv-record-set-with-a-single-record"></a>단일 레코드가 포함된 SRV 레코드 집합 만들기

영역 루트에 SRV 레코드를 만드는 경우 레코드 이름에 "_ㄴservice" 및 "_protocol"을 지정할 수 있습니다. 레코드 이름에 "@"을(를) 포함하지 않아도 됩니다.

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"
```

#### <a name="create-a-txt-record-set-with-single-record"></a>단일 레코드가 포함된 TXT 레코드 집합 만들기

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"
```


<!--HONumber=Nov16_HO2-->


