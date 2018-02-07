

## <a name="what-is-happening"></a>설명

업계 차원의 하드웨어 기반 보안 취약성이 [1월 3일에 공개](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)되었습니다. 고객의 보안을 유지하는 것은 항상 최우선 순위이며 이러한 취약성에 노출되는 Azure 고객이 없도록 적극적인 조치를 취하고 있습니다.

보안 취약성의 공개와 함께 [계획된 유지 관리 타이밍을 가속화했으며](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) 여전히 업데이트가 필요한 VM의 재부팅을 자동으로 시작했습니다.


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>내 VM 중에서 이미 업데이트된 항목을 어떻게 확인할 수 있나요? 

[Azure Portal의 VM 목록](https://aka.ms/T08tdc)에서 VM의 상태를 확인할 수 있습니다(재부팅이 완료된 경우). 업데이트가 적용된 경우 “이미 업데이트됨”으로 표시되고, 업데이트가 필요한 경우 “예약됨”으로 표시됩니다. “예약됨” 상태의 VM만 보려면 [Azure Service Health](https://portal.azure.com/)를 참조하세요.

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>내 VM이 재부팅될 경우를 정확하게 확인할 수 있나요?

재부팅에 대한 경고를 받을 수 있는 가장 좋은 방법은 [예약된 이벤트](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events)를 구성하는 것입니다. 그러면 유지 관리로 인해 VM이 다운되기 15분 전에 알림이 표시됩니다.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>필요한 유지 관리를 수행하도록 지금 수동으로 재배포할 수 있나요? 

재배포된 VM이 업데이트된 호스트에 할당되는 것을 보장할 수 없습니다. 가능할 때마다 Azure 패브릭은 이미 업데이트된 호스트에 VM을 할당하려고 합니다. VM 재배포로 업데이트되지 않은 호스트에 놓일 수 있으며 이 경우 예약된 유지 관리의 일환으로 강제로 두 번째 재부팅이 될 수 있습니다. 결과적으로, 수동 재배포는 해결 방법으로 권장되지 않습니다.

## <a name="how-long-will-the-reboot-take"></a>재부팅은 얼마나 걸리나요? 

대부분의 재부팅은 약 **30분**이 걸립니다.

## <a name="does-the-guest-os-need-to-be-updated"></a>게스트 OS를 업데이트해야 하나요? 

이 Azure 인프라 업데이트는 하이퍼바이저 수준에서 공개된 취약성을 해결하며 Windows 또는 Linux VM 이미지에 대한 업데이트는 필요하지 않습니다. 그러나 항상 VM 이미지에 보안 모범 사례를 계속해서 적용해야 합니다. 필요에 따라 업데이트 및 지침은 운영 체제의 공급업체에 문의하세요. 현재 Windows Server VM 고객을 위한 지침이 게시되었으며 [여기](../articles/virtual-machines/windows/mitigate-se.md)에서 볼 수 있습니다.

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>이 업데이트 문제를 해결하면 성능에 영향이 있을까요?

대부분의 Azure 고객은 이 업데이트로 성능이 크게 달라졌다고 느끼지 못할 것입니다. Microsoft는 CPU 및 디스크 I/O 경로를 최적화하기 위해 노력했으며, 수정 사항이 적용된 후 성능이 눈에 띄게 달라졌다고 느끼지 못하고 있습니다. 소수의 고객은 네트워킹 성능이 약간 달라졌다고 느낄 수도 있습니다. 모든 Azure 고객에게 제공되는 무료 기능인 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 또는 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)용 Azure Accelerated Networking을 사용하여 이 문제를 해결할 수 있습니다.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>고가용성을 위해 권장 사항을 따릅니다. 내 환경은 재부팅 동안 고가용성을 유지하나요?

예, 가용성 집합 또는 가상 머신 확장 집합에 배포된 가상 머신에는 UD(업데이트 도메인) 구문이 있습니다. 유지 관리를 수행할 때 Azure는 UD 제약 조건을 적용하고 다른 UD(동일한 가용성 집합 내)의 가상 머신을 다시 부팅하지 않습니다. 고가용성에 대한 자세한 내용은 [Azure에서 Windows 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) 또는 [Azure에서 Linux 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)를 참조하세요.

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>지역 쌍을 사용하여 비즈니스 연속성/재해 복구 계획을 설계했습니다. 내 VM에 대한 재부팅은 지역 쌍에서 동시에 발생할까요?

일반적으로 Azure의 계획된 유지 관리 이벤트는 두 지역의 중단 위험을 최소화하기 위해 지역 쌍에 한 번에 하나씩 배포됩니다. 그러나 이 보안 업데이트의 긴급한 특성상 Microsoft는 이 업데이트를 모든 지역에 동시에 배포하고 있습니다.

## <a name="what-about-paas-services-on-azure"></a>Azure에서 PaaS 서비스 어떻습니까?  

웹 및 모바일, 데이터 서비스, IoT, 서버리스 등을 포함하는 Azure 플랫폼 서비스는 취약성을 해결했습니다. 이러한 서비스를 사용하는 고객에게 필요한 작업은 없습니다.

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>Intel은 보안 취약성과 관련하여 2018년 1월 22일에 추가 지침을 릴리스했습니다.  이 지침은 Azure에서 추가 유지 관리 작업을 발생시키나요?  

2018년 1월 3일에 발표된 Azure 완화는 Intel의 [업데이트된 지침](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/)에 영향을 받지 않습니다. 이러한 새 정보의 결과로 고객 VM에 대한 추가 유지 관리 작업이 없습니다.
 

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [CPU 취약성으로부터 Azure 고객 보호](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)를 참조하세요.
