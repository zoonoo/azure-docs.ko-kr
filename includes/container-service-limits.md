| 리소스 | 기본 제한 |
| --- | :--- |
| 클러스터당 최대 노드 | 100 |
| 노드당 최대 포드([Kubenet과 기본 네트워킹][basic-networking]) | 110 |
| 노드당 최대 포드([Azure CNI과 고급 네트워킹][advanced-networking]) | 30<sup>1</sup> |
| 구독당 최대 클러스터 | 20<sup>2</sup> |

<sup>1</sup> ARM 템플릿 배포를 통해 이 값을 사용자 지정할 수 있습니다. [여기][arm-deployment-example]에서 예제를 참조합니다.<br />
<sup>2</sup> 제한 증가를 요청하려면 [Azure 지원 요청][azure-support]을 만듭니다.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
