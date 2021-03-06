---
title: "Az ExpressRoute-kapcsolatcsoportok NAT-követelményei | Microsoft Docs"
description: "Ez az oldal ExpressRoute-kapcsolatcsoportok NAT-konfigurálásának és -kezelésének részletes követelményeit ismerteti."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 867bf936-c851-485f-84c8-d8d6e33fee9f
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 371c76ed36cd9d21026a5a49c6ef86a0cd3cc816
ms.openlocfilehash: 8fd8b4b9611adb15df7e436a00f8ec35ea1e9614


---
# <a name="expressroute-nat-requirements"></a>Az ExpressRoute NAT-követelményei
Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, NAT-okat kell beállítania és kezelnie. Egyes kapcsolatszolgáltatók felügyelt szolgáltatásként kínálják a NAT beállítását és kezelését. Ellenőrizze kapcsolatszolgáltatójánál, hogy kínál-e ilyen szolgáltatást. Ha nem, akkor meg kell felelnie az alább ismertetett követelményeknek. 

A különböző útválasztási tartományok áttekintését az [ExpressRoute-kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md) oldalon tekintheti meg. Az Azure nyilvános és a Microsoft társviszony-létesítés nyilvános IP-cím követelményeinek való megfeleléshez javasoljuk, hogy állítsa be a NAT-ot a hálózata és a Microsoft között. Ez a szakasz a beállítandó NAT-infrastruktúra részletes leírását tartalmazza.

## <a name="nat-requirements-for-azure-public-peering"></a>Az Azure nyilvános társviszony-létesítés NAT-követelményei
Az Azure nyilvános társviszony-létesítési útvonal használatával az Azure-ban üzemeltetett összes szolgáltatáshoz csatlakozhat a nyilvános IP-címeiken keresztül. Ezen szolgáltatások közé tartoznak az [ExpressRoute – Gyakori kérdések](expressroute-faqs.md) című dokumentumban felsorolt szolgáltatások, valamint az ISV-k által a Microsoft Azure-ban üzemeltetett szolgáltatások. A Microsoft Azure-szolgáltatásokhoz a nyilvános társviszony-létesítéssel létrehozott kapcsolatokat mindig az Ön hálózata kezdeményezi a Microsoft hálózata felé. A Microsoft Azure felé a nyilvános társviszony-létesítésen keresztül irányuló forgalmat forrásoldali hálózati címfordítással érvényes nyilvános IPv4-címekké kell alakítani, mielőtt belép a Microsoft hálózatába. Az alábbi ábrán annak magas szintű ábrázolása látható, hogy hogyan kell beállítani a NAT-ot a fenti követelmények teljesítéséhez.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT IP-készlet és útvonalhirdetések
Biztosítania kell, hogy a forgalom az Azure nyilvános társviszony-létesítési útvonalra érvényes nyilvános IPv4-címmel lépjen be. A Microsoftnak képesnek kell lennie ellenőriznie az IPv4 NAT-címkészlet tulajdonjogát egy regionális útválasztási internetes jegyzékben (RIR) vagy egy internetes útválasztási jegyzékben (RIR) A rendszer a NAT-tal társviszonyban lévő AS-szám és az ahhoz használt IP-címek alapján ellenőrzést hajt végre. Az útválasztási nyilvántartásokkal kapcsolatos információkért tekintse meg [az ExpressRoute útválasztási követelményeit](expressroute-routing.md) ismertető oldalt.

A társviszony-létesítésen keresztül hirdetett NAT IP-előtag hosszára nem vonatkoznak korlátozások. Meg kell figyelnie a NAT-készletet, és biztosítania kell, hogy nem fogy ki a NAT-munkamenetekből.

> [!IMPORTANT]
> A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.
> 
> 

## <a name="nat-requirements-for-microsoft-peering"></a>A Microsoft társviszony-létesítésre vonatkozó NAT-követelmények
A Microsoft társviszony-létesítési útvonal lehetővé teszi, hogy az Azure nyilvános társviszony-létesítési útvonalon keresztül nem támogatott Microsoft-felhőszolgáltatásokhoz csatlakozzon. A szolgáltatások listájába beletartoznak az Office 365 szolgáltatásai, például az Exchange Online, a SharePoint Online, a Skype Vállalati verzió és a CRM Online. A Microsoft tervezi a kétirányú kapcsolatok támogatását a Microsoft társviszony-létesítésen keresztül. A Microsoft-felhőszolgáltatások felé irányuló forgalmat forrásoldali hálózati címfordítással érvényes nyilvános IPv4-címekké kell alakítani, mielőtt belép a Microsoft hálózatába. A Microsoft felhőszolgáltatásai felől az Ön hálózata felé irányuló forgalmon forrásoldali hálózati címfordítást kell végrehajtani, mielőtt az adatok belépnének az Ön hálózatába. Az alábbi ábrán annak magas szintű ábrázolása látható, hogy hogyan kell beállítani a NAT-ot a Microsoft társviszony-létesítéshez.

![](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Az Ön hálózatából eredő, a Microsoft felé irányuló forgalom
* Biztosítania kell, hogy a forgalom a Microsoft társviszony-létesítési útvonalra érvényes nyilvános IPv4-címmel lépjen be. A Microsoftnak képesnek kell lennie ellenőriznie az IPv4 NAT-címkészlet tulajdonosát egy regionális útválasztási internetes jegyzékben (RIR) vagy egy internetes útválasztási jegyzékben (RIR) A rendszer a NAT-tal társviszonyban lévő AS-szám és az ahhoz használt IP-címek alapján ellenőrzést hajt végre. Az útválasztási nyilvántartásokkal kapcsolatos információkért tekintse meg [az ExpressRoute útválasztási követelményeit](expressroute-routing.md) ismertető oldalt.
* Az Azure nyilvános társviszony-létesítési beállításhoz és az egyéb ExpressRoute-kapcsolatcsoportokhoz használt IP-címek nem hirdethetők a Microsoft felé a BGP-munkameneten keresztül. A társviszony-létesítésen keresztül hirdetett NAT IP-előtag hosszára nem vonatkoznak korlátozások.
  
  > [!IMPORTANT]
  > A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>A Microsofttól eredő, az Ön hálózata felé irányuló forgalom
* Bizonyos forgatókönyvek megkövetelik, hogy a Microsoft kapcsolatot kezdeményezzen az Ön hálózatán üzemeltetett szolgáltatásvégpontokkal. A forgatókönyvre jellemző példa az Ön hálózatában üzemeltetett ADFS-kiszolgálókhoz való csatlakozás az Office 365-ből. Ilyen esetekben a megfelelő előtagokat ki kell szivárogtatnia a hálózatából a Microsoft-társviszonyba. 
* A Microsoft felől az Ön hálózatában lévő IP-címekhez irányított forgalmon forrásoldali hálózati címfordítást kell végrehajtani. 

## <a name="next-steps"></a>Következő lépések
* Tekintse meg az [Útválasztás](expressroute-routing.md) és a [QoS](expressroute-qos.md) követelményeit.
* További információkért lásd: [ExpressRoute-kapcsolatcsoportok kiépítési munkafolyamatai és kapcsolatcsoport-állapotok](expressroute-workflows.md).
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-classic.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Nov16_HO3-->


