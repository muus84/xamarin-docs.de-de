---
title: Kern Grafiken in xamarin. IOS
description: In diesem Artikel werden die Kern Grafiken für IOS-Frameworks erläutert. Es zeigt, wie Sie Kern Grafiken zum Zeichnen von Geometrie, Bildern und PDF-Dateien verwenden können.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 76901a5c48caef666d18f5cc7e2bfd8b28096184
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032456"
---
# <a name="core-graphics-in-xamarinios"></a>Kern Grafiken in xamarin. IOS

_diesem Artikel werden die Kern Grafiken für IOS-Frameworks erläutert. Es wird gezeigt, wie Kern Grafiken zum Zeichnen von Geometrie, Bildern und PDF-Dateien verwendet werden._

IOS umfasst das [*Core-Grafik*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) Framework, um Unterstützung auf niedriger Ebene bereitzustellen. Diese Frameworks ermöglichen die umfangreichen grafischen Funktionen in UIKit.

Kern Grafiken ist ein 2D-Grafik Framework auf niedriger Ebene, das das Zeichnen von geräteunabhängigen Grafiken ermöglicht. Alle 2D-Zeichen in UIKit verwenden intern Kern Grafiken.

Kern Grafiken unterstützen das Zeichnen in einer Reihe von Szenarien, einschließlich:

- [Zeichnen auf den Bildschirm über eine `UIView`](#Drawing_in_a_UIView_Subclass) .
- [Zeichnen von Bildern im Arbeitsspeicher oder auf dem Bildschirm](#Drawing_Images_and_Text).
- Erstellen und zeichnen in eine PDF-Datei.
- Lesen und Zeichnen einer vorhandenen PDF-Datei.

## <a name="geometric-space"></a>Geometrischer Raum

Unabhängig vom Szenario werden alle Zeichnungen, die mit Kern Grafiken durchgeführt werden, im geometrischen Raum durchgeführt, was bedeutet, dass Sie in abstrakten Punkten statt in Pixel funktionieren. Sie beschreiben, was im Hinblick auf Geometrie und Zeichnungs Zustand gezeichnet werden soll, z. b. Farben, Linienstile usw., und die Kern Grafik behandelt alles in Pixel. Ein solcher Zustand wird einem Grafik Kontext hinzugefügt, den Sie sich wie der Canvas eines Malers vorstellen können.

Dieser Ansatz bietet einige Vorteile:

- Das Zeichnen von Code wird dynamisch und kann anschließend Grafiken zur Laufzeit ändern.
- Wenn Sie die Notwendigkeit statischer Images im Anwendungs Bündel verringern, kann die Anwendungs Größe verringert werden.
- Grafiken werden für Geräte übergreifende Lösungs Änderungen stabiler.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Zeichnen in einer UIView-Unterklasse

Jede `UIView` verfügt über eine `Draw` Methode, die vom System aufgerufen wird, wenn Sie gezeichnet werden muss. Zum Hinzufügen von Zeichnungs Code zu einer Ansicht `UIView` und überschreiben Sie `Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Draw sollte nie direkt aufgerufen werden. Sie wird vom System während der Verarbeitung der Lauf Schleifen Verarbeitung aufgerufen. Beim ersten Mal durch die Lauf Schleife, nachdem der Ansichts Hierarchie eine Ansicht hinzugefügt wurde, wird die `Draw`-Methode aufgerufen. Nachfolgende Aufrufe von `Draw` auftreten, wenn die Sicht als gezeichnet gekennzeichnet ist, indem entweder `SetNeedsDisplay` oder `SetNeedsDisplayInRect` in der Sicht aufgerufen wird.

### <a name="pattern-for-graphics-code"></a>Muster für Grafik Code

Der Code in der `Draw` Implementierung sollte beschreiben, was er gezeichnet will. Der Zeichnungs Code folgt einem Muster, in dem ein Zeichnungs Zustand festgelegt wird, und ruft eine Methode auf, um das Zeichnen zu fordern. Dieses Muster kann wie folgt verallgemeinert werden:

1. Einen Grafik Kontext erhalten.

2. Einrichten von Zeichnungs Attributen.

3. Erstellen Sie eine Geometrie aus Zeichnungs primitiven.

4. Ruft eine Draw-oder Stroke-Methode auf.

### <a name="basic-drawing-example"></a>Einfaches Zeichnungs Beispiel

Sehen Sie sich beispielsweise den folgenden Code Ausschnitt an:

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {

    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100),
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

Wir brechen diesen Code ab:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```

Mit dieser Zeile ruft Sie zuerst den aktuellen Grafik Kontext ab, der zum Zeichnen verwendet werden soll. Sie können sich einen Grafik Kontext als den Zeichenbereich vorstellen, in dem die Zeichnung auftritt, der den gesamten Zustand der Zeichnung enthält, wie z. b. Strich-und Füll Farben sowie die zu zeichnende Geometrie.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
```

Nachdem Sie einen Grafik Kontext erhalten haben, richtet der Code einige Attribute ein, die beim Zeichnen verwendet werden, wie oben gezeigt. In diesem Fall werden die Linienbreite, Strich-und Füllfarbe festgelegt. Bei jeder nachfolgenden Zeichnung werden diese Attribute verwendet, da Sie im Zustand des Grafik Kontexts beibehalten werden.

Zum Erstellen von Geometrie verwendet der Code eine `CGPath`, die das Beschreiben eines Grafik Pfads aus Linien und Kurven ermöglicht. In diesem Fall fügt der Pfad Zeilen hinzu, die ein Array von Punkten verbinden, um ein Dreieck zu bilden. Wie im folgenden dargestellt, verwendet das Koordinatensystem für das Ansichts zeichnen, wo sich der Ursprung in der oberen linken Ecke befindet, mit dem positiven x-direkt nach rechts und der positiv-y-Richtung nach unten:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100),
new CGPoint (220, 200)});

path.CloseSubpath ();
```

Nachdem der Pfad erstellt wurde, wird er dem Grafik Kontext hinzugefügt, sodass er durch Aufrufen von `AddPath` und `DrawPath` gezeichnet werden kann.

Die resultierende Ansicht wird unten dargestellt:

 ![](core-graphics-images/00-bluetriangle.png "The sample output triangle")

## <a name="creating-gradient-fills"></a>Erstellen von Verlaufs Füllungen

Außerdem sind umfangreichere Formen der Zeichnung verfügbar. Beispielsweise ermöglicht die Kern Grafik das Erstellen von Farbverlaufs Füllungen und das Anwenden von clippingpfaden. Zum Zeichnen einer Farbverlaufsfüllung innerhalb des Pfads aus dem vorherigen Beispiel muss zuerst der Pfad als clippingpfad festgelegt werden:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Wenn der aktuelle Pfad als clippingpfad festgelegt wird, werden alle nachfolgenden Zeichnungen innerhalb der Geometrie des Pfads eingeschränkt, wie z. b. der folgende Code, der einen linearen Farbverlauf zeichnet:

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient,
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top),
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom),
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

Diese Änderungen führen zu einer Farbverlaufsfüllung, wie unten dargestellt:

 ![](core-graphics-images/01-gradient-fill.png "The example with a gradient fill")

## <a name="modifying-line-patterns"></a>Ändern von Zeilen Mustern

Die Zeichnungs Attribute von Linien können auch mit Kern Grafiken geändert werden. Dies umfasst auch das Ändern der Linienstärke und der Strichfarbe sowie des Linien Musters selbst, wie im folgenden Code gezeigt:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Wenn Sie diesen Code vor allen Zeichnungs Vorgängen hinzufügen, führt dies zu einem gestrichelten Strich von 10 Einheiten, der vier Einheiten Abstand zwischen den Bindestrichen aufweist, wie unten dargestellt:

 ![](core-graphics-images/02-dashed-stroke.png "Adding this code before any drawing operations results in dashed strokes")

Beachten Sie, dass der Arraytyp bei der Verwendung der Unified API in xamarin. IOS ein `nfloat`sein muss und außerdem explizit in Math. PI umgewandelt werden muss.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Zeichnen von Bildern und Text

Neben dem Zeichnen von Pfaden im Grafik Kontext einer Ansicht unterstützen Kern Grafiken auch das Zeichnen von Bildern und Text. Um ein Bild zu zeichnen, erstellen Sie einfach eine `CGImage` und übergeben Sie an einen `DrawImage`-Befehl:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Dies führt jedoch zu einem Bild, das oben nach unten gezeichnet wird, wie unten dargestellt:

 ![](core-graphics-images/03-upside-down-monkey.png "An image drawn upside down")

Der Grund hierfür ist, dass der Ursprung der Grafik Quelle für die Bild Zeichnung unten links ist, während die Ansicht den Ursprung in der linken oberen Ecke hat. Damit das Bild ordnungsgemäß angezeigt wird, muss der Ursprung geändert werden. Dies kann durch Ändern der *aktuellen Transformations Matrix* *(CTM)* erreicht werden. CTM definiert, wo Punkte aktiv sind, auch als *Benutzerbereich*bezeichnet. Wenn Sie die CTM-Achse in der y-Richtung umkehren und Sie um die Höhe der Begrenzungen in der negativen y-Richtung verschieben, kann das Bild kippen.

Der Grafik Kontext verfügt über Hilfsmethoden zum Transformieren der CTM-Datei. In diesem Fall `ScaleCTM` die Zeichnung "Flips", und `TranslateCTM` verschiebt Sie in die obere linke Ecke, wie unten dargestellt:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}
```

Das resultierende Bild wird dann aufsteigenden angezeigt:

 ![](core-graphics-images/04-upright-monkey.png "The sample image displayed upright")

> [!IMPORTANT]
> Änderungen am Grafik Kontext gelten für alle nachfolgenden Zeichnungsvorgänge. Wenn die CTM-Datei transformiert wird, wirkt sich dies auf alle zusätzlichen Zeichnungen aus. Wenn Sie z. b. das Dreieck hinter der CTM-Transformation gezeichnet haben, wird es in der obigen Ecke angezeigt.

### <a name="adding-text-to-the-image"></a>Hinzufügen von Text zum Bild

Wie bei Pfaden und Bildern umfasst das Zeichnen von Text mit Kern Grafiken dasselbe grundlegende Muster wie das Festlegen eines Grafik Zustands und das Aufrufen einer zu zeichnenden Methode. Im Fall von Text ist die Methode zum Anzeigen von Text `ShowText`. Wenn der folgende Code dem Bild Zeichnungs Beispiel hinzugefügt wird, zeichnet er mithilfe von Kern Grafiken Text:

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);

    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

Wie Sie sehen können, ähnelt das Festlegen des Grafik Zustands für die Text Zeichnung dem Zeichnen von Geometrie. Bei der Text Zeichnung werden jedoch auch der Text Zeichnungsmodus und die Schriftart angewendet. In diesem Fall wird auch ein Schatten angewendet, obwohl das Anwenden von Shadowing für die Pfad Zeichnung identisch ist.

Der resultierende Text wird mit dem Bild angezeigt, wie unten dargestellt:

 ![](core-graphics-images/05-text-on-image.png "The resulting text is displayed with the image")

## <a name="memory-backed-images"></a>Speicher gestützte Images

Zusätzlich zum Zeichnen in den Grafik Kontext einer Ansicht unterstützt die Kern Grafik das Zeichnen von Speicher gestützten Bildern, auch als zeichnen außerhalb des Bildschirms bezeichnet. Hierfür ist Folgendes erforderlich:

- Erstellen eines Grafik Kontexts, der durch eine in-Memory-Bitmap unterstützt wird
- Festlegen des Zeichnungs Zustands und Ausgeben von Zeichnungs Befehlen
- Das Bild wird aus dem Kontext abgeraten.
- Der Kontext wird entfernt.

Anders als bei der `Draw`-Methode, in der der Kontext von der Sicht bereitgestellt wird, erstellen Sie in diesem Fall den Kontext auf eine von zwei Arten:

1. Durch Aufrufen von `UIGraphics.BeginImageContext` (oder `BeginImageContextWithOptions`)

2. Durch Erstellen eines neuen `CGBitmapContextInstance`

 `CGBitmapContextInstance` ist nützlich, wenn Sie direkt mit den Bildbits arbeiten, z. b. in Fällen, in denen Sie einen benutzerdefinierten Bild Bearbeitungs Algorithmus verwenden. In allen anderen Fällen sollten Sie `BeginImageContext` oder `BeginImageContextWithOptions`verwenden.

Wenn Sie einen Bildkontext haben, ist das Hinzufügen von Zeichnungs Code genau so, wie er sich in einer `UIView` Unterklasse befindet. Beispielsweise kann das zuvor zum Zeichnen eines Dreiecks verwendete Codebeispiel zum Zeichnen eines Bilds im Arbeitsspeicher anstelle einer `UIView`verwendet werden, wie unten dargestellt:

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100),
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }

    return triangleImage;
}
```

Eine gängige Verwendung des Zeichnens zu einer Speicher gestützten Bitmap ist das Erfassen eines Bilds aus beliebigen `UIView`. Der folgende Code rendert z. b. die Ebene einer Ansicht in einen bitmapkontext und erstellt eine `UIImage` daraus:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Zeichnen von PDFs

Neben Bildern unterstützt die Kern Grafik das Zeichnen von PDF-Dateien. Wie Bilder können Sie eine PDF-Datei im Arbeitsspeicher Rendern und eine PDF-Datei zum Rendern in einer `UIView`lesen.

### <a name="pdf-in-a-uiview"></a>PDF in einer UIView

Kern Grafiken unterstützen auch das Lesen einer PDF-Datei aus einer Datei und das Rendern in einer Ansicht mithilfe der `CGPDFDocument`-Klasse. Die `CGPDFDocument`-Klasse stellt eine PDF-Datei im Code dar und kann zum Lesen und Zeichnen von Seiten verwendet werden.

Der folgende Code in einer `UIView`-Unterklasse liest z. b. eine PDF-Datei aus einer Datei in eine `CGPDFDocument`:

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }

     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

Die `Draw`-Methode kann dann die `CGPDFDocument` verwenden, um eine Seite in `CGPDFPage` zu lesen und Sie durch Aufrufen von `DrawPDFPage`zu erstellen, wie unten dargestellt:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);

        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {

        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);

        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);

        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>Speicher gesicherte PDF-Datei

Für eine in-Memory-PDF müssen Sie einen PDF-Kontext erstellen, indem Sie `BeginPDFContext`aufrufen. Das Zeichnen in die PDF-Datei ist differenzierter für Seiten. Jede Seite wird durch Aufrufen von `BeginPDFPage` gestartet und durch Aufrufen von `EndPDFContent`abgeschlossen, wobei der Grafik Code dazwischen liegt. Ebenso wie bei der Bild Zeichnung verwendet die Speicher gestützte PDF-Zeichnung einen Ursprung unten links. Dies kann durch Ändern der CTM-Darstellung wie bei Bildern berücksichtigt werden.

Der folgende Code zeigt, wie Sie Text in eine PDF-Datei zeichnen:

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();

using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }

//complete a PDF page
UIGraphics.EndPDFContent ();
```

Der resultierende Text wird in die PDF-Datei gezeichnet, die dann in einer `NSData` enthalten ist, die gespeichert, hochgeladen, per e-Mail versendet werden kann usw.

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben wir die Grafikfunktionen untersucht, die über das *Core-Grafik* Framework bereitgestellt werden. Wir haben gesehen, wie Kern Grafiken zum Zeichnen von Geometrie, Bildern und PDF-Dateien im Kontext einer `UIView,` sowie von Speicher gestützten Grafik Kontexten verwendet werden.

## <a name="related-links"></a>Verwandte Links

- [Beispiel für Core-Grafiken](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [Exemplarische Vorgehensweise zu Grafiken und Animationen](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Grundlegende Animations Rezepte](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
