## <a name="what-are-service-bus-queues"></a>Mik azok a Service Bus-üzenetsorok?
A Service Bus-üzenetsorok **közvetítőalapú üzenettovábbítási** kommunikációs modellt biztosítanak az üzenettovábbításhoz. Üzenetsorok használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenetsoron keresztül. Az üzenet létrehozója (a küldő) átadja az üzenetet az üzenetsornak, majd folytatja annak feldolgozását. Aszinkron módon az üzenetfogyasztó (a fogadó) kiemeli az üzenetet az üzenetsorból, és feldolgozza azt. A küldőnek nem kell várnia a fogyasztó válaszára a további üzenetek feldolgozásához és kiküldéséhez. Az üzenetsorok **elsőnek be, elsőnek ki (First In, First Out, FIFO)** üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Ez azt jelenti, hogy az üzeneteket általában az érzékelők fogadják és dolgozzák fel a sorhoz történő hozzáadásuk sorrendjében, és minden üzenetet csak egy üzenetfogyasztó fogad és dolgoz fel.

![Az üzenetsorok alapfogalmai](./media/howto-service-bus-queues/sb-queues-08.png)

A Service Bus-üzenetsor egy olyan általános célú technológia, amely széles körben felhasználható:

* Egy többszintes Azure-alkalmazásban, a webes és feldolgozói szerepkörök közötti kommunikáció során.
* Egy hibrid megoldásban, a helyszíni és az Azure által kezelt alkalmazások közötti kommunikáció során.
* A különböző szervezetekben, vagy egy szervezet különböző részlegein helyileg futó elosztott alkalmazások összetevői közötti kommunikáció során.

Az üzenetsorok használata lehetővé teszi az alkalmazások könnyebb méretezését, valamint nagyobb mértékű rugalmasságot biztosít az architektúrák számára.




<!--HONumber=Jan17_HO3-->


