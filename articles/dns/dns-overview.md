<properties 
   pageTitle="Azure DNS 개요 | Microsoft Azure" 
   description="Microsoft Azure의 Azure DNS 호스팅 서비스 개요 Microsoft Azure에 도메인을 호스트하세요." 
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

# Azure DNS 개요


Domain Name System, 즉 DNS는 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. Azure DNS는 DNS 도메인에 대한 호스팅 서비스로, Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.


Azure DNS의 DNS 도메인은 DNS 이름 서버의 Azure 글로벌 네트워크에 호스트됩니다. 사용 가능한 가장 가까운 DNS 서버에서 각 DNS 쿼리에 응답하도록 애니캐스트 네트워킹이 사용됩니다. 이렇게 하면 도메인에 대해 빠른 성능과 고가용성이 제공됩니다.

Azure DNS 서비스는 ARM(Azure Resource Manager)을 기반으로 합니다. 이 경우 역할 기반 액세스 제어, 감사 로그 및 리소스 잠금과 같은 ARM 기능의 이점을 누릴 수 있습니다. 도메인과 레코드는 Azure 포털, Azure PowerShell cmdlet 및 플랫폼 간 Azure CLI를 통해 관리할 수 있습니다. 자동 DNS 관리가 필요한 응용 프로그램은 REST API 및 SDK를 통해 서비스와 통합할 수 있습니다.

Azure DNS는 현재 도메인 이름 구입을 지원하지 않습니다. 도메인을 구입하려면 타사 도메인 이름 등록 기관을 사용해야 합니다. 등록 기관은 일반적으로 소액의 연간 요금을 부과합니다. 그런 다음, DNS 레코드의 관리를 위해 Azure DNS에 해당 도메인을 호스트할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.


## 다음 단계

[DNS 영역 만들기](dns-getstarted-create-dnszone-portal.md)




 

<!---HONumber=AcomDC_0817_2016-->