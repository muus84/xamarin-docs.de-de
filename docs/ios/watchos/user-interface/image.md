---
title: watchos-Image-Steuerelemente in xamarin
description: In diesem Dokument wird die Verwendung von Image-Steuerelementen in einer watchos-Anwendung beschrieben, die mit xamarin erstellt wurde. Es erläutert das wkinterfaceimage-Steuerelement, die Methode "-timage", das Hinzufügen von Bildern zu einer Watch-Erweiterung, Animationen und mehr.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 7d24286b5d428a571afc7498afafa1171c075110
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032706"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchos-Image-Steuerelemente in xamarin

watchos bietet ein [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) Steuerelement, um Bilder und einfache Animationen anzuzeigen. Einige Steuerelemente können auch über ein Hintergrundbild verfügen (z. b. Schaltflächen, Gruppen und Schnittstellen Controller).

![](image-images/image-walkway.png "Bild Apple Watch angezeigt") ![](image-images/image-animation.png "Apple Watch mit einfacher Animation")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Verwenden Sie Asset catalog-images, um Images zu Watch Kit-apps hinzuzufügen.
Nur **@2x** Versionen sind erforderlich, da alle Überwachungsgeräte Retina-anzeigen aufweisen.

![](image-images/asset-universal-sml.png "Only 2x versions are required, since all watch devices have Retina displays")

Es wird empfohlen, sicherzustellen, dass die Bilder selbst die richtige Größe für die Anzeige Anzeige darstellen. *Vermeiden* Sie die Verwendung von Images mit falscher Größe (besonders große) und die Skalierung, um Sie auf dem Bildschirm anzuzeigen.

Sie können die Watch Kit-Größen (38mm und 42mm) in einem Asset Catalog-Image verwenden, um unterschiedliche Images für jede Anzeige Größe anzugeben.

![](image-images/asset-watch-sml.png "You can use the Watch Kit sizes 38mm and 42mm in an asset catalog image to specify different images for each display size")

## <a name="images-on-the-watch"></a>Bilder auf der Uhr

Die effizienteste Möglichkeit zum Anzeigen von Bildern besteht darin, *Sie in das Überwachungs-App-Projekt einzuschließen* und mithilfe der `SetImage(string imageName)`-Methode anzuzeigen.

Beispielsweise verfügt das [watchkitcatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog/) -Beispiel über eine Reihe von Bildern, die einem Asset-Katalog im Überwachungs-App-Projekt hinzugefügt werden:

![](image-images/asset-whale-sml.png "The WatchKitCatalog sample has a number of images added to an asset catalog in the watch app project")

Diese können mithilfe `SetImage` mit dem Parameter "String Name" effizient geladen und auf der Überwachung angezeigt werden:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Hintergrundbilder

Die gleiche Logik gilt für die `SetBackgroundImage (string imageName)` für die Klassen `Button`, `Group`und `InterfaceController`. Die beste Leistung wird erzielt, indem die Images in der Watch-APP selbst gespeichert werden.

## <a name="images-in-the-watch-extension"></a>Bilder in der Watch-Erweiterung

Zusätzlich zum Laden von Bildern, die in der Watch-APP selbst gespeichert sind, können Sie Bilder aus dem Erweiterungspaket zur Anzeige an die Watch-App senden (oder Sie können Images von einem Remote Speicherort herunterladen und anzeigen).

Wenn Sie Bilder aus der Watch-Erweiterung laden möchten, erstellen Sie `UIImage` Instanzen, und rufen Sie dann `SetImage` mit dem `UIImage`-Objekt auf.

Beispielsweise hat das [watchkitcatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) -Beispiel ein Image mit dem Namen **Bumblebee** im Überwachungs Erweiterungsprojekt:

![](image-images/asset-bumblebee-sml.png "The WatchKitCatalog sample has an image named Bumblebee in the watch extension project")

Der folgende Code führt zu folgendem:

- das Bild, das in den Arbeitsspeicher geladen wird, und
- wird auf der Überwachung angezeigt.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```

## <a name="animations"></a>Animationen

Um einen Satz von Bildern zu animieren, sollten Sie alle mit dem gleichen Präfix beginnen und über ein numerisches Suffix verfügen.

Das [watchkitcatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) -Beispiel enthält eine Reihe nummerierter Images im Watch-App-Projekt mit dem **Bus** -Präfix:

![](image-images/asset-bus-animation-sml.png "The WatchKitCatalog sample has a series of numbered images in the watch app project with the Bus prefix")

Wenn Sie diese Bilder als Animation anzeigen möchten, laden Sie das Bild zuerst mithilfe `SetImage` mit dem Präfix Namen, und wenden Sie dann `StartAnimating`an:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

`StopAnimating` für das Image-Steuerelement aufgerufen, um die Animations Schleife zu verhindern:

```csharp
animatedImage.StopAnimating ();
```

<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Anhang: Zwischenspeichern von Images (watchos 1)

> [!IMPORTANT]
> watchos 3-apps werden vollständig auf dem Gerät ausgeführt. Die folgenden Informationen sind nur für watchos 1-apps vorgesehen.

Wenn die Anwendung wiederholt ein Bild verwendet, das in der Erweiterung gespeichert ist (oder heruntergeladen wurde), ist es möglich, das Image im Speicher der Überwachung zwischenzuspeichern, um die Leistung für nachfolgende anzeigen zu erhöhen.

Verwenden Sie die `WKInterfaceDevice`s `AddCachedImage`-Methode, um das Bild auf die Überwachung zu übertragen, und verwenden Sie dann `SetImage` mit dem Parameter "Image Name" als Zeichenfolge, um es anzuzeigen:

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

Sie können den Inhalt des Image Caches im Code mithilfe `WKInterfaceDevice.CurrentDevice.WeakCachedImages`Abfragen.

### <a name="managing-the-cache"></a>Verwalten des Caches

Der Cache ist ungefähr 20 MB groß. Sie wird über APP-Neustarts hinweg beibehalten, und wenn Sie ausgefüllt wird, liegt es in ihrer Verantwortung, Dateien mithilfe `RemoveCachedImage` oder `RemoveAllCachedImages` Methoden für das `WKInterfaceDevice.CurrentDevice`-Objekt zu löschen.

## <a name="related-links"></a>Verwandte Links

- [Watchkitcatalog (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Image-Dokument von Apple](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)
