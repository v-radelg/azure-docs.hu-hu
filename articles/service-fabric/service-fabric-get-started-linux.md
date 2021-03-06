---
title: "A fejlesztési környezet beállítása Linuxon | Microsoft Docs"
description: "Telepítse a futtatókörnyezetet és az SDK-t, majd hozzon létre egy helyi fejlesztési fürtöt Linuxon. A beállítás befejezése után készen áll az alkalmazások létrehozására."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 1e961eccbc4fb8af90c7da831429c942f92bdf79
ms.lasthandoff: 03/11/2017


---
# <a name="prepare-your-development-environment-on-linux"></a>A fejlesztőkörnyezet előkészítése Linuxon
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Az [Azure Service Fabric-alkalmazásoknak](service-fabric-application-model.md) a linuxos fejlesztői gépen való üzembe helyezéséhez és futtatásához telepítse a futtatókörnyezetet és az általános SDK-t. A Javához és a .NET Core-hoz készült opcionális SDK-kat is telepítheti.

## <a name="prerequisites"></a>Előfeltételek

### <a name="supported-operating-system-versions"></a>Támogatott operációsrendszer-verziók
A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Ubuntu 16.04 („Xenial Xerus”)

## <a name="update-your-apt-sources"></a>Frissítse az apt-forrásait
Az SDK és a kapcsolódó futtatókörnyezet-csomag apt-get használatával történő telepítéséhez először frissítenie kell az apt-forrásait.

1. Nyisson meg egy terminált.
2. Adja hozzá a Service Fabric-adattárat a források listájához.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Adja hozzá a dotnet-adattárat a források listájához.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```
4. Adja hozzá az új GPG-kulcsot az apt-kulcstárhoz.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Frissítse a csomaglistákat az újonnan hozzáadott adattárak szerint.

    ```bash
    sudo apt-get update
    ```
## <a name="install-and-set-up-the-sdk"></a>Az SDK telepítése és beállítása
A források frissítése után telepítheti az SDK-t.

1. Telepítse a Service Fabric SDK-csomagot. A rendszer felkéri, hogy erősítse meg a telepítést, és fogadjon el egy licencszerződést.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. Futtassa az SDK telepítőszkriptjét.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```


## <a name="set-up-the-azure-cross-platform-cli"></a>A platformfüggetlen Azure parancssori felület beállítása
A [platformfüggetlen Azure parancssori felület][azure-xplat-cli-github] a Service Fabric-entitásokkal, többek között fürtökkel és alkalmazásokkal folytatott interakcióra szolgáló parancsokat is tartalmaz. Node.js-alapú, ezért az alábbi utasítások végrehajtása előtt [ellenőrizze, hogy telepítve van-e a Node][install-node]:

1. Klónozza a github-adattárat a fejlesztői gépére.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. Váltson át a klónozott adattárra, és telepítse a parancssori felület függőségeit a Node Package Managerrel (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. Hozzon létre szimbolikus csatolást a klónozott adattár bin/azure mappájából az /usr/bin/azure mappába, hogy bekerüljön az elérési útjába, és a parancsok bármelyik könyvtárból elérhetők legyenek.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. Végezetül engedélyezze a Service Fabric-parancsok automatikus kiegészítését.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> A Service Fabric parancsok még nem érhetők el az Azure CLI 2.0-ás verziójában.

## <a name="set-up-a-local-cluster"></a>Helyi fürt beállítása
Ha mindent sikeresen telepített, elindíthatja a helyi fürtöt.

1. Futtassa a fürttelepítési szkriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Nyisson meg egy webböngészőt, majd navigáljon a következő helyre: http://localhost:19080/Explorer. Ha a fürt elindult, megjelenik a Service Fabric Explorer irányítópultja.

    ![Service Fabric Explorer Linuxon][sfx-linux]

Ezen a ponton előzetesen összeállított Service Fabric-alkalmazáscsomagokat, vagy vendégtárolókon és vendég futtatható fájlokon alapuló új alkalmazáscsomagokat helyezhet üzembe. Ha új szolgáltatásokat szeretne létrehozni a Java vagy a .NET Core SDK-k használatával, kövesse az ezt követő szakaszokban megadott opcionális beállítási lépéseket.


> [!NOTE]
> Az önálló fürtök Linux rendszeren nem támogatottak – az előzetes verzióban kizárólag beépített és Azure Linux többgépes fürtök támogatottak.
>
>

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>A Java SDK és az Eclipse Neon beépülő modul telepítése (nem kötelező)
A Java SDK biztosítja a Java-t használó Service Fabric-szolgáltatások létrehozásához szükséges kódtárakat és sablonokat.

1. Telepítse a Java SDK-csomagot.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Futtassa az SDK telepítőszkriptjét.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

A Service Fabric Eclipse beépülő modulját a **Java-fejlesztőknek készült Eclipse IDE-ből** is telepítheti.

1. Az Eclipse-ben győződjön meg arról, hogy telepítve van a legújabb Eclipse **Neon** és a Buildship&1;.0.17-es vagy újabb verziója. A telepített összetevők verzióját a **Help > Installation Details** (Súgó > Telepítés részletei) lehetőség kiválasztásával ellenőrizheti. Az [itt][buildship-update] található utasítások alapján frissítheti a Buildshipet.
2. A Service Fabric beépülő modul telepítéséhez válassza a **Help > Install New Software...** (Súgó > Új szoftver telepítése) lehetőséget.
3. A Work with (Használat) szövegbeviteli mezőbe írja be a következőt: http://dl.windowsazure.com/eclipse/servicefabric
4. Kattintson az Add (Hozzáadás) parancsra.
    ![Eclipse beépülő modul][sf-eclipse-plugin]
5. Válassza ki a Service Fabric beépülő modult, majd kattintson a Next (Tovább) gombra.
6. Folytassa a telepítést, és fogadja el a végfelhasználói licencszerződést.

Ha a Service Fabric Eclipse beépülő modul már telepítve van, győződjön meg arról, hogy a legújabb verziót használja. Ellenőrizze, hogy a program tovább frissíthető-e: lépjen a ``Help => Installation Details`` hivatkozásra, keresse meg a Service Fabric elemet a telepített beépülő modulok listájában, és kattintson a frissítés parancsra. Ha van függőben lévő frissítés, a rendszer lekéri és telepíti.

További információ a Service Fabric Java-alkalmazások Service Fabric Eclipse beépülő modullal való létrehozásáról, üzembe helyezéséről és frissítéséről: [Service Fabric – első lépések az Eclipse-szel](service-fabric-get-started-eclipse.md).

## <a name="install-the-net-core-sdk-optional"></a>A .NET Core SDK telepítése (opcionális)
A .NET Core SDK biztosítja a platformfüggetlen .NET Core-t használó Service Fabric-szolgáltatások létrehozásához szükséges kódtárakat és sablonokat.

1. Telepítse a .NET Core SDK-csomagot.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Futtassa az SDK telepítőszkriptjét.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>Az SDK és a futtatókörnyezet frissítése

Az SDK és a futtatókörnyezet legújabb verziójára történő frissítéséhez hajtsa végre a következő lépéseket (távolítsa el az SDK-kat azokból a listákból, amelyeket nem szeretne frissíteni vagy telepíteni):

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric, servicefabricsdkcommon, servicefabricsdkcsharp, servicefabricsdkjava
   ```

A parancssori felület frissítéséhez lépjen abba a könyvtárba, amelybe klónozta a parancssori felületet, és futtassa a `git pull` parancsot a frissítéshez.

## <a name="next-steps"></a>Következő lépések
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Az első CSharp-alkalmazás létrehozása Linuxon](service-fabric-create-your-first-linux-application-with-csharp.md)
* [A fejlesztőkörnyezet előkészítése OSX-en](service-fabric-get-started-mac.md)
* [Az Azure CLI használata a Service Fabric-alkalmazások kezeléséhez](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

