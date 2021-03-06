---
title: "Külső gyártótól származó Hadoop-alkalmazások telepítése az Azure HDInsighton | Microsoft Docs"
description: "Megismerheti, hogyan telepíthet külső gyártótól származó Hadoop-alkalmazásokat az Azure HDInsighton."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 8e7911a3a8080ef8fa125779aa1f6778b9655cde
ms.openlocfilehash: 8780c193c6aa4b6b183723f88d67ac0990347d1e


---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Külső gyártótól származó Hadoop-alkalmazások telepítése az Azure HDInsighton

Ebből a cikkből megtudhatja, hogyan telepíthet már közzétett, külső gyártótól származó Hadoop-alkalmazásokat az Azure HDInsighton. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Jelenleg négy közzétett alkalmazás érhető el:

* **DATAIKU DDS on HDInsight**: A Dataiku DSS (Data Science Studio) egy olyan szoftver, amely lehetővé teszi az adatokat professzionálisan célra használók (adatszakértők, üzleti elemzők, fejlesztők…) számára a nyers adatokat hatékony üzleti előrejelzésekké alakító rendkívül specifikus szolgáltatások prototípusként való használatát, létrehozását és központi telepítését.
* **Datameer**: a [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) interaktív módot kínál az elemzőknek a Big Data-eredmények felfedezésére, elemzésére és vizuális megjelenítésére. Vonjon be további adatforrásokat, fedezzen fel új kapcsolatokat, és kapjon még gyorsabban választ kérdéseire.
* A **Streamsets Data Collector for HDInsight** olyan, átfogó funkciókat kínáló integrált fejlesztőkörnyezetet (IDE) biztosít, amelyben bármely irányú, stream- és batch-adatokat összefűző feldolgozási folyamatok tervezése, tesztelése, üzembe helyezése és felügyelete lehetséges, továbbá különféle streamen belüli transzformációkat is tartalmaz, és mindehhez nincs szükség egyedi kód írására. 
* A **Cask CDAP 3.5 for HDInsight** az első olyan egységes integrációs big data-platform, amely az adatalkalmazások és adattárak létrehozási idejét 80%-kal csökkenti. Ez az alkalmazás kizárólag a Standard HBase 3.4-fürtöket támogatja.


A cikkben szereplő utasítások az Azure Portalon alapulnak. Az Azure Resource Manager-sablont a portálról is exportálhatja, vagy beszerezheti a Resource Manager-sablon egy példányát a szállítóktól, majd az Azure PowerShell és az Azure parancssori felület segítségével üzembe helyezheti a sablont.  Lásd: [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Resource Manager-sablonok segítségével).

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-applications-to-existing-clusters"></a>Alkalmazások telepítése a meglévő fürtökre
Az alábbi eljárásból megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat egy meglévő HDInsight-fürtbe.

**HDInsight-alkalmazások telepítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre.  Ha nem jelenik meg, kattintson a **További szolgáltatások**, majd a **HDInsight-fürtök** elemre.
3. Kattintson a kívánt HDInsight-fürtre.  Ha még nincs ilyen fürtje, hozzon létre egyet most.  Lásd: [Fürtök létrehozása](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Kattintson az **Applications** (Alkalmazások) elemre a **Configurations** (Konfigurációk) kategóriában. Megjelenik a telepített alkalmazások listája, ha vannak ilyenek. Ha nem látja az Alkalmazások elemet, az azt jelenti, hogy a HDInsight-fürt jelen verziójához nem érhetők el alkalmazások.
   
    ![HDInsight-alkalmazások menü a portálon](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Kattintson a panel menüjének **Hozzáadás** elemére. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
   
    Megjelenik a meglévő HDInsight-alkalmazások listája.
   
    ![HDInsight-alkalmazások, elérhető alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Kattintson a kívánt alkalmazásra, fogadja el a jogi feltételeket, majd kattintson a **Kiválasztás** gombra.

A portál értesítései között megtekintheti a telepítési állapotot (ehhez kattintson a portál felső részén található harang ikonra). Az alkalmazás telepítését követően az alkalmazás bekerül a Telepített alkalmazások panelre.

## <a name="install-applications-during-cluster-creation"></a>Alkalmazások telepítése fürtlétrehozás során
HDInsight-alkalmazásokat fürt létrehozása közben is telepíthet. A HDInsight-alkalmazásokat azt követően telepíti a rendszer, hogy a fürt létrejött, és futó állapotba került. Az alábbi eljárás leírja, hogyan telepíthet HDInsight-alkalmazásokat a fürt létrehozása közben.

**HDInsight-alkalmazások telepítése**

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) webhelyre.
2. Kattintson az **ÚJ** elemre, majd az **Adatok + analitika**, végül a **HDInsight** elemre.
3. A **Fürt neve** mezőben adja meg a fürt nevét, amelynek globálisan egyedinek kell lennie.
4. Kattintson az **Előfizetés** elemre, és válassza ki a fürthöz használni kívánt Azure-előfizetést.
5. Kattintson a **Fürt típusának kiválasztása** elemre, majd válasszon:
   
   * **Fürt típusa**: ha nem tudja, hogy melyik a megfelelő, válassza a **Hadoop** lehetőséget. Ez a legnépszerűbb fürttípus.
   * **Operációs rendszer**: válassza a **Linux** lehetőséget.
   * **Verzió**: ha nem tudja, hogy melyik a megfelelő, válassza az alapértelmezett verziót. További tájékoztatás a [HDInsight cluster versions](hdinsight-component-versioning.md) (A HDInsight-fürtök verziói) című cikkben olvasható.
   * **Cluster Tier** (Fürt szintje): az Azure HDInsight a big data felhőajánlatokat kétféle – Standard és Prémium – szinten biztosítja. További tájékoztatás a [Cluster tiers](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers) (A fürtök szintjei) című cikkben olvasható.
6. Kattintson az **Alkalmazások** lehetőségre, kattintson a közzétett alkalmazások egyikére, majd a **Kiválasztás** elemre.
7. Kattintson a **Hitelesítő adatok** elemre, majd adja meg a rendszergazda felhasználó jelszavát. Ezenfelül ki kell töltenie az **SSH-felhasználónév** és a **JELSZÓ** vagy a **NYILVÁNOS KULCS** mezőt. A rendszer ezek alapján hitelesíti az SSH-felhasználót. A Microsoft a nyilvános kulcs használatát javasolja. A hitelesítő adatok konfigurációjának mentéséhez kattintson az oldal alján található **Kiválasztás** elemre.
8. Kattintson az **Adatforrás** elemre, válasszon egy meglévő tárfiókot vagy hozzon létre egy új tárfiókot. A rendszer ezt fogja a fürt alapértelmezett tárfiókjaként használni.
9. Kattintson az **Erőforráscsoport** elemre, és válasszon egy meglévő erőforráscsoportot, vagy kattintson az **Új** elemre egy új erőforráscsoport létrehozásához.
10. Az **Új HDInsight-fürt** panelen ellenőrizze, hogy be van-e jelölve a **Kezdőlapra rögzít** jelölőnégyzet, majd kattintson a **Létrehozás** gombra. 

## <a name="list-installed-hdinsight-apps-and-properties"></a>Telepített HDInsight-alkalmazások és az alkalmazástulajdonságok listázása
A portál megjeleníti a fürthöz tartozó telepített HDInsight-alkalmazásokat, valamint az egyes telepített alkalmazásokhoz tartozó tulajdonságokat.

**HDInsight-alkalmazások listázása és az alkalmazástulajdonságok megjelenítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre.  Ha nem látja, kattintson a **Browse** (Tallózás), majd a **HDInsight Clusters** (HDInsight-fürtök) elemre.
3. Kattintson a kívánt HDInsight-fürtre.
4. A **Settings** (Beállítások) panelen kattintson az **Applications** (Alkalmazások) elemre a **General** (Általános) kategóriában. A Telepített alkalmazások panelen az összes telepített alkalmazás fel van sorolva. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. A tulajdonságok megjelenítéséhez kattintson a kívánt telepített alkalmazásra. A tulajdonság panelen a következők láthatók:
   
   * Alkalmazásnév: az alkalmazás neve.
   * Állapot: az alkalmazás állapota. 
   * Weblap: az élcsomóponton üzembe helyezett webalkalmazás URL-címe, amennyiben van ilyen. A hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt.
   * HTTP-végpont: a hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt. 
   * SSH-végpont: az élcsomóponthoz [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) segítségével is csatlakozhat. Az SSH hitelesítő adatai ugyanazok, mint amelyeket a fürthöz beállított SSH-felhasználónál használt.
6. A kívánt alkalmazás törléséhez kattintson jobb gombbal az alkalmazásra, majd válassza a helyi menü **Törlés** pontját.

## <a name="connect-to-the-edge-node"></a>Csatlakozás az élcsomóponthoz
Az élcsomóponthoz HTTP és SSH segítségével csatlakozhat. A végpont adatait megtalálja a [portálon](#list-installed-hdinsight-apps-and-properties). Az SSH használatával kapcsolatos további információk [Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Linux, Unix vagy OS X rendszerben](hdinsight-hadoop-linux-use-ssh-unix.md) című cikkben olvashatók. 

A HTTP végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított HTTP-felhasználónál használt; az SSH-végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított SSH-felhasználónál használt.

## <a name="troubleshoot"></a>Hibaelhárítás
Lásd: [A telepítési folyamat hibaelhárítása](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Következő lépések
* [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Egyéni HDInsight-alkalmazások telepítése): megtudhatja, hogyan helyezhet üzembe a HDInsight szolgáltatásban egy olyan HDInsight-alkalmazást, amely nincs közzétéve.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Resource Manager-sablonok segítségével): Megtudhatja, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.




<!--HONumber=Feb17_HO3-->


