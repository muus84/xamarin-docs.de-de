---
title: Anzeigen eines Bilds in xamarin. IOS
description: In diesem Artikel wird beschrieben, wie Sie ein Image-Asset in eine xamarin. IOS-App einschließen C# und dieses Bild entweder mithilfe von Code oder durch Zuweisen zu einem Steuerelement im IOS-Designer anzeigen.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/24/2018
ms.openlocfilehash: cda45f01dae2dc17c2517a7f013acacde7906a4b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004480"
---
# <a name="displaying-an-image-in-xamarinios"></a>Anzeigen eines Bilds in xamarin. IOS

_In diesem Artikel wird beschrieben, wie Sie ein Image-Asset in eine xamarin. IOS-App einschließen C# und dieses Bild entweder mithilfe von Code oder durch Zuweisen zu einem Steuerelement im IOS-Designer anzeigen._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Hinzufügen und organisieren von Bildern in einer xamarin. IOS-App

Wenn Sie ein Image für die Verwendung in einer xamarin. IOS-app hinzufügen, verwendet der Entwickler einen Ressourcen _Katalog_ , um alle IOS-Geräte und-Lösungen zu unterstützen, die von einer APP benötigt werden.

In ios 7 hinzugefügt wurden, enthalten **Image Sätze für Asset-Kataloge** alle Versionen oder Darstellungen eines Images, die zur Unterstützung verschiedener Geräte und Skalierungsfaktoren für eine APP erforderlich sind. Anstatt sich auf den Image Ressourcen Dateinamen zu verlassen, verwenden **Image-Sets** eine JSON-Datei, um anzugeben, welches Image zu welchem Gerät und/oder zur Auflösung gehört. Dies ist die bevorzugte Methode zum Verwalten und unterstützen von Images in ios (ab IOS 9).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Hinzufügen von Bildern zu einem Asset Catalog-Image Satz

Wie bereits erwähnt, enthalten die **Image Sätze der Asset-Kataloge** alle Versionen oder Darstellungen eines Bilds, die zur Unterstützung verschiedener Geräte und Skalierungsfaktoren für eine APP erforderlich sind. Anstatt sich auf den Image Ressourcen Dateinamen zu verlassen, verwenden **Image-Sets** eine JSON-Datei, um anzugeben, welches Image zu welchem Gerät und/oder zur Auflösung gehört.

Gehen Sie folgendermaßen vor, um eine neue Abbild Gruppe zu erstellen und Ihr Bilder hinzuzufügen:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio für Mac](#tab/macos)

1. Doppelklicken Sie im **Projektmappen-Explorer**auf die `Assets.xcassets` Datei, um Sie zur Bearbeitung zu öffnen:

    ![](displaying-an-image-images/imageset01.png "The Assets.xcassets in the Solution Explorer")
2. Klicken Sie mit der rechten Maustaste auf die **Liste Assets** , und wählen Sie **neue Bildmenge**aus:

    ![](displaying-an-image-images/imageset02.png "Adding a New Image Set")
3. Wählen Sie den neuen Bildsatz aus, und der Editor wird angezeigt:

    ![](displaying-an-image-images/imageset03.png "The Image Set editor")
4. Ziehen Sie von hier aus Bilder für jedes der verschiedenen Geräte und Auflösungen, die erforderlich sind.
5. Doppelklicken Sie auf den **Namen** des neuen Image Satzes in der **Liste Assets** , um ihn zu bearbeiten:![](displaying-an-image-images/imageset04.png "Der Name des neuen Image Satzes wird bearbeitet.")

Wenn Sie ein **Bild** im IOS-Designer verwenden, wählen Sie einfach den Namen der Gruppe in der Dropdown Liste im Eigenschaften-Editor aus:

![](displaying-an-image-images/imageset06.png "Select an image set's name from the dropdown list")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Öffnen Sie den Ressourcen Katalog über die **Projektmappen-Explorer**, und klicken Sie in der linken oberen Ecke auf die Schaltfläche **plus** :

    ![](displaying-an-image-images/asset5.png "Click the Plus button")

2. Wählen Sie **Bild Satz hinzufügen** aus, und der Bild Satz-Editor wird für die neue Bildmenge angezeigt. Ziehen Sie von hier aus Bilder für jedes der verschiedenen Geräte und Auflösungen, die erforderlich sind.

    ![](displaying-an-image-images/asset7.png "The image set editor")

### <a name="renaming-an-image-set"></a>Umbenennen eines Bild Satzes

Gehen Sie folgendermaßen vor, um einen Bildsatz umzubenennen:

1. Doppelklicken Sie im **Projektmappen-Explorer**auf die **Asset Catalog** -Datei, um Sie zur Bearbeitung zu öffnen:

    ![](displaying-an-image-images/rename01.png "The Asset Catalog in the Solution Explorer")
2. Wählen Sie das umzubenennende **Bild** aus:

    ![](displaying-an-image-images/rename02.png "Select the Image Set to rename")
3. Scrollen Sie im **Eigenschaften-Explorer**nach unten, und wählen Sie " **Name**" ( **im Abschnitt "** Sonstige") aus:

    ![](displaying-an-image-images/rename03.png "Select Name under the Misc section")
4. Geben Sie einen neuen **Namen** für die **Abbild Gruppe** ein, und speichern Sie die Änderungen.

-----

Wenn Sie einen **Bildsatz** im Code verwenden, verweisen Sie ihn anhand des Namens, indem Sie die `FromBundle`-Methode der `UIImage`-Klasse aufrufen. Beispiel:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Wenn die einem Bild Satz zugewiesenen Bilder nicht ordnungsgemäß angezeigt werden, stellen Sie sicher, dass der richtige Dateiname mit der `FromBundle`-Methode verwendet wird (der **Bildsatz** und nicht der Name des übergeordneten **Objekt Katalogs** ). Bei PNG-Bildern kann die `.png` Erweiterung weggelassen werden. Bei anderen Bildformaten ist die Erweiterung erforderlich (z. b. `PurpleMonkey.jpg`) angezeigt wird.

### <a name="using-vector-images-in-asset-catalogs"></a>Verwenden von Vektorbildern in Asset-Katalogen

Ab IOS 8 wurde eine spezielle **Vektor** Klasse zu **Bild Sätzen** hinzugefügt, die es dem Entwickler ermöglichen, ein **PDF-** formatiertes Vektorbild in die Kassette einzubeziehen, anstatt einzelne Bitmapdateien in den verschiedenen Auflösungen einzubeziehen. Stellen Sie mit dieser Methode eine einzelne Vektor Datei für die `@1x` Auflösung (formatiert als Vektor-PDF-Datei) bereit, und die `@2x`-und `@3x` Versionen der Datei werden zur Kompilierzeit generiert und in das Paket der Anwendung eingeschlossen.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio für Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "Vector Images in the Asset Catalogs editor")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "Vector Images in the Asset Catalogs editor")

-----

Wenn der Entwickler z. b. eine `MonkeyIcon.pdf`-Datei als Vektor eines Ressourcen Katalogs mit einer Auflösung von 150 px x 150px enthält, sind die folgenden bitmapassets in der endgültigen App Bundle enthalten, als Sie kompiliert wurde:

- Auflösung von `MonkeyIcon@1x.png`-150px x 150px.
- Auflösung von `MonkeyIcon@2x.png`-300 px x 300 px.
- `MonkeyIcon@3x.png`-450px x 450 px Auflösung.

Bei der Verwendung von PDF-Vektorbildern in Asset-Katalogen sollten folgende Aspekte berücksichtigt werden:

- Dabei handelt es sich nicht um vollständige Vektor Unterstützung, da die PDF-Datei zur Kompilierzeit in eine Bitmap gerengt wird und die in der abschließenden Anwendung enthaltenen Bitmaps.
- Die Größe des Abbilds kann nicht angepasst werden, sobald es im Asset-Katalog festgelegt wurde. Wenn der Entwickler versucht, die Größe des Bilds zu ändern (entweder im Code oder mithilfe der Klassen für automatisches Layout und Größe), wird das Bild wie jede andere Bitmap verzerrt.
- Assetkataloge sind nur mit IOS 7 und höher kompatibel. Wenn eine APP IOS 6 oder niedriger unterstützen muss, können Ressourcen Kataloge nicht verwendet werden.

## <a name="working-with-template-images"></a>Arbeiten mit Vorlagen Images

Basierend auf dem Entwurf einer IOS-App kann es vorkommen, dass der Entwickler ein Symbol oder Bild innerhalb der Benutzeroberfläche anpassen muss, damit er einer Änderung des Farbschemas entspricht (z. b. basierend auf den Benutzereinstellungen).

Um diesen Effekt zu erzielen, ändern Sie den _Rendermodus_ des Image Assets in das **Vorlagen Image**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio für Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "The Render Mode set to Template Image")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "The Render Mode set to Template")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

Weisen Sie im IOS-Designer das Image-Asset einem UI-Steuerelement zu, und legen Sie dann das **tint** fest, um das Bild zu färben:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio für Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "Set the Tint to colorize the image")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "Set the Tint to colorize the image")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Optional können Image Asset und tint direkt im Code festgelegt werden:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Gehen Sie folgendermaßen vor, um ein Vorlagen Image vollständig aus dem Code zu verwenden:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Da die `RenderMode`-Eigenschaft einer `UIImage` schreibgeschützt ist, verwenden Sie die `ImageWithRenderingMode`-Methode, um eine neue Instanz des Bilds mit der gewünschten rendermoduseinstellung zu erstellen.

Es gibt drei mögliche Einstellungen für `UIImage.RenderMode` über die `UIImageRenderingMode`-Aufzählung:

- `AlwaysOriginal` erzwingt, dass das Bild ohne Änderungen als ursprüngliche Quell Abbild Datei gerendert wird.
- `AlwaysTemplate` erzwingt, dass das Bild als Vorlagen Bild gerendert wird, indem die Pixel mit der angegebenen `Tint` Farbe hervorgehoben werden.
- `Automatic`: rendert das Bild entweder als Vorlage oder als ursprüngliches auf der Grundlage der Umgebung, in der es verwendet wird. Wenn das Image z. b. in einem `UIToolBar`, `UINavigationBar``UITabBar` oder `UISegmentControl` verwendet wird, wird es als Vorlage behandelt.

## <a name="adding-new-assets-collections"></a>Neue Ressourcen Sammlungen werden hinzugefügt

Beim Arbeiten mit Bildern in Ressourcen Katalogen kann es vorkommen, dass eine neue Sammlung erforderlich ist, anstatt alle Images der APP der `Assets.xcassets` Auflistung hinzuzufügen. Beispielsweise beim Entwerfen von bedarfsgesteuerten Ressourcen.

So fügen Sie dem Projekt einen neuen Ressourcen Katalog hinzu:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio für Mac](#tab/macos)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den **Projektnamen** , und wählen Sie > **neue Datei** **Hinzufügen** ... aus.
2. Wählen Sie **IOS** > **Asset Catalog**aus, geben Sie einen **Namen** für die Sammlung ein, und klicken Sie auf die Schaltfläche **neu**

    ![](displaying-an-image-images/asset01.png "Creating a new Asset Catalog")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Asset** Katalogs, und wählen Sie **> neuen Ressourcen Katalog hinzufügen**aus.
2. Geben Sie einen Namen, und klicken Sie auf **Hinzufügen**:

    ![](displaying-an-image-images/asset1.png "Creating a new Asset Catalog")

-----

Von hier aus kann die Auflistung genauso wie die standardmäßige `Assets.xcassets` Auflistung bearbeitet werden, die automatisch im Projekt enthalten ist.

## <a name="using-images-with-controls"></a>Verwenden von Bildern mit Steuerelementen

Zusätzlich zur Verwendung von Images zur Unterstützung einer App verwendet IOS auch Bilder mit App-Steuerelement Typen wie z. b. Registerkarten, Symbolleisten, Navigationsleisten, Tabellen und Schaltflächen. Eine einfache Möglichkeit, ein Bild auf einem Steuerelement zu erstellen, besteht darin, der `Image`-Eigenschaft des Steuer Elements eine `UIImage` Instanz zuzuweisen.

### <a name="frombundle"></a>Frombundle

Der `FromBundle`-Methodenaufrufe ist ein synchroner (blockierender) Befehl, der über eine Reihe von integrierten Funktionen zum Laden und Verwalten von Bildern verfügt, wie z. b. Caching-Unterstützung und automatische Behandlung von Bilddateien für verschiedene Auflösungen.

Im folgenden Beispiel wird gezeigt, wie das Image eines `UITabBarItem` in einem `UITabBar`festgelegt wird:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Angenommen, `MyImage` ist der Name eines imageassets, das einem oben genannten Ressourcen Katalog hinzugefügt wurde. Geben Sie beim Arbeiten mit dem Asset-Katalog Image einfach den Namen des Bilds an, das in der `FromBundle`-Methode für **PNG** -formatierte Images festgelegt

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Fügen Sie die Erweiterung für ein beliebiges anderes Bildformat in den Namen ein. Beispiel:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Weitere Informationen zu Symbolen und Bildern finden Sie in der Apple-Dokumentation zu [Richtlinien für benutzerdefiniertes Symbol und Bild Erstellung](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

## <a name="displaying-an-image-in-a-storyboards"></a>Anzeigen eines Bilds in einem Storyboard

Nachdem ein Image einem xamarin. IOS-Projekt mit einem Asset-Katalog hinzugefügt wurde, kann es problemlos in einem Storyboard mithilfe eines `UIImageView` im IOS-Designer angezeigt werden. Wenn z. b. das folgende bildasset hinzugefügt wurde:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio für Mac](#tab/macos)

![](displaying-an-image-images/display01.png "A sample Image Asset has been added")

Gehen Sie folgendermaßen vor, um die Datei auf einem Storyboard anzuzeigen:

1. Doppelklicken Sie auf die `Main.storyboard` Datei im **Projektmappen-Explorer** , um Sie im IOS-Designer zur Bearbeitung zu öffnen.
2. Wählen Sie eine **Bildansicht** aus der **Toolbox**aus:

     ![](displaying-an-image-images/display02.png "Select an Image View from the Toolbox")
3. Ziehen Sie die Bildansicht auf die Entwurfs Oberfläche, und positionieren Sie Sie nach Bedarf:

    ![](displaying-an-image-images/display03.png "A new Image View on the Design Surface")
4. Wählen Sie im Abschnitt **Widget** des **Eigenschaften-Explorers** das gewünschte **Bild** Medienobjekt aus, das angezeigt werden soll:

    ![](displaying-an-image-images/display04.png "Select the desired Image asset to be displayed")
5. Verwenden Sie im Abschnitt **Ansicht** den- **Modus** , um zu steuern, wie die Größe des Bilds geändert wird, wenn die Größe der **Bildansicht** geändert wird.
6. Wenn die **Bildansicht** ausgewählt ist, klicken Sie erneut, um **Einschränkungen**hinzuzufügen:

    ![](displaying-an-image-images/display05.png "Adding Constraints")
7. Ziehen Sie den "T"-förmigen Zieh Punkt an jedem Rand der **Bildansicht** an die entsprechende Seite des Bildschirms, um das Bild an die Seiten anzuheften. Auf diese Weise wird die **Bildansicht** verkleinert und vergrößert, wenn die Größe des Bildschirms geändert wird.
8. Speichern Sie die Änderungen am Storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "A sample Image Asset has been added")

Gehen Sie folgendermaßen vor, um die Datei auf einem Storyboard anzuzeigen:

1. Doppelklicken Sie auf die `Main.storyboard` Datei im **Projektmappen-Explorer** , um Sie im IOS-Designer zur Bearbeitung zu öffnen.
2. Wählen Sie eine **Bildansicht** aus der **Toolbox**aus:

     ![](displaying-an-image-images/display02vs.png "Select an Image View from the Toolbox")
3. Ziehen Sie die Bildansicht auf die Entwurfs Oberfläche, und positionieren Sie Sie nach Bedarf:

    ![](displaying-an-image-images/display03vs.png "A new Image View on the Design Surface")
4. Wählen Sie im Abschnitt **Widget** des **Eigenschaften-Explorers** das gewünschte **Bild** Medienobjekt aus, das angezeigt werden soll:

    ![](displaying-an-image-images/display04vs.png "Select the desired Image asset to be displayed")
5. Verwenden Sie im Abschnitt **Ansicht** den- **Modus** , um zu steuern, wie die Größe des Bilds geändert wird, wenn die Größe der **Bildansicht** geändert wird.
6. Wenn die **Bildansicht** ausgewählt ist, klicken Sie erneut, um **Einschränkungen**hinzuzufügen:

    ![](displaying-an-image-images/display05vs.png "Adding Constraints")
7. Ziehen Sie den "T"-förmigen Zieh Punkt an jedem Rand der **Bildansicht** an die entsprechende Seite des Bildschirms, um das Bild an die Seiten anzuheften. Auf diese Weise wird die **Bildansicht** verkleinert und vergrößert, wenn die Größe des Bildschirms geändert wird.
8. Speichern Sie die Änderungen am Storyboard.

-----

## <a name="displaying-an-image-in-code"></a>Anzeigen eines Bilds im Code

Wenn ein Bild einem xamarin. IOS-Projekt mit einem Asset-Katalog hinzugefügt wurde, kann es sich wie bei der Anzeige eines Bilds in einem Storyboard problemlos C# mithilfe von Code anzeigen lassen.

Betrachten Sie das folgende Beispiel:

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

Dieser Code erstellt einen neuen `UIImageView` und gibt ihm eine anfängliche Größe und Position. Anschließend wird das Bild aus einem Image-Asset geladen, das dem Projekt hinzugefügt wurde, und das `UIImageView` dem übergeordneten `UIView` hinzugefügt, um es anzuzeigen.

## <a name="related-links"></a>Verwandte Links

- [Arbeiten mit Bildern (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hallo iPhone](~/ios/get-started/hello-ios/index.md)
- [Bildgröße und-Auflösung (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
