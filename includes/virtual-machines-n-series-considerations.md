## <a name="general-considerations-for-n-series-vms"></a>N 시리즈 VM에 대한 일반적인 고려 사항

* N 시리즈 VM의 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/en-us/regions/services/)을 참조하세요.

* N 시리즈 VM은 Resource Manager 배포 모델에만 배포할 수 있습니다.

* Azure Portal을 사용하여 N 시리즈 VM을 만들 때에는 **기본** 블레이드에서 **HDD**의 **VM 디스크 유형**을 선택합니다. 사용 가능한 N 시리즈 크기를 선택하려면 **크기** 블레이드에서 **모두 보기**를 클릭합니다.

* N 시리즈 VM은 Azure Premium Storage에서 지원되는 VM 디스크를 지원하지 않습니다.

* 몇 개의 N 시리즈 VM을 배포하려는 경우 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 계산 코어만 사용할 수 있습니다.

* Azure 구독에서 코어 할당량(지역당) 및 NC 또는 NV 코어에 대한 별도의 할당량을 늘려야 할 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../articles/azure-supportability/how-to-create-azure-support-request.md) 합니다. 기본 제한은 구독 범주에 따라 달라질 수 있습니다.







