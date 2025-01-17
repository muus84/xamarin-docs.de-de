---
title: Vision Framework in xamarin. IOS
description: In diesem Dokument wird beschrieben, wie Sie das IOS 11-Vision-Framework in xamarin. IOS verwenden. Insbesondere werden Rechteck Erkennung und Gesichtserkennung erläutert.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/31/2017
ms.openlocfilehash: b58e7b1fffed3253d9765401d52f16b751db134d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032073"
---
# <a name="vision-framework-in-xamarinios"></a>Vision Framework in xamarin. IOS

Das Vision-Framework bietet IOS 11 eine Reihe neuer Features für die Bildverarbeitung, einschließlich:

- [Rechteck Erkennung](#rectangles)
- [Gesichtserkennung](#faces)
- Machine Learning Abbild Analyse (in [coreml](~/ios/platform/introduction-to-ios11/coreml.md)erläutert)
- Barcode Erkennung
- Bild Ausrichtungs Analyse
- Text Erkennung
- Horizont aliterkennung
- Objekterkennung & Nachverfolgung

![Foto mit drei Rechtecke erkannt](vision-images/found-rectangles-tiny.png) ![Foto mit zwei Gesichtern erkannt](vision-images/xamarin-home-faces-tiny.png)

Die Rechteck Erkennung und-Gesichtserkennung werden unten ausführlicher erläutert.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Rechteck Erkennung

Das [Beispiel "visionrects](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles) " zeigt, wie ein Bild verarbeitet und die erkannten Rechtecke gezeichnet werden.

### <a name="1-initialize-the-vision-request"></a>1. Initialisieren Sie die Vision Request.

Erstellen Sie in `ViewDidLoad`eine `VNDetectRectanglesRequest`, die auf die `HandleRectangles`-Methode verweist, die am Ende jeder Anforderung aufgerufen wird:

Die `MaximumObservations`-Eigenschaft sollte ebenfalls festgelegt werden. andernfalls wird Sie standardmäßig auf 1 festgelegt, und nur ein einzelnes Ergebnis wird zurückgegeben.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. Starten der Maschinelles Verarbeitung

Der folgende Code beginnt mit der Verarbeitung der Anforderung. Im Beispiel " **visionrects** " wird dieser Code ausgeführt, nachdem der Benutzer ein Bild ausgewählt hat:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Dieser Handler übergibt die `ciImage` an das Vision Framework `VNDetectRectanglesRequest`, das in Schritt 1 erstellt wurde.

### <a name="3-handle-the-results-of-vision-processing"></a>3. behandeln der Ergebnisse der Maschinelles Verarbeitung

Nachdem die Rechteck Erkennung fertiggestellt wurde, führt das Framework die `HandleRectangles`-Methode aus, eine Zusammenfassung, die unten dargestellt wird:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. Anzeigen der Ergebnisse

Die `OverlayRectangles`-Methode im " **visionrechgles** "-Beispiel verfügt über drei Funktionen:

- Rendern des Quell Bilds
- Zeichnen eines Rechtecks, um anzugeben, wo jedes erkannt wurde, und
- Hinzufügen einer Text Bezeichnung für jedes Rechteck mithilfe von CoreGraphics.

Sehen Sie sich die Quelle des Beispiels für die exakte CoreGraphics [-](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles) Methode an.

![Foto mit drei Rechtecke erkannt](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. weitere Verarbeitung

Die Rechteck Erkennung ist oft nur der erste Schritt in einer Kette von Vorgängen, z. b. mit [diesem coremlvision-Beispiel](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), bei dem die Rechtecke an ein coreml-Modell zum Analysieren von handschriftlichen Ziffern übermittelt werden.

<a name="faces" />

## <a name="face-detection"></a>Gesichtserkennung

Das [Beispiel "visiongesichter](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces) " funktioniert ähnlich wie das Beispiel " **visionrechgles** " unter Verwendung einer anderen Vision Request-Klasse.

### <a name="1-initialize-the-vision-request"></a>1. Initialisieren Sie die Vision Request.

Erstellen Sie in `ViewDidLoad`eine `VNDetectFaceRectanglesRequest`, die auf die `HandleRectangles`-Methode verweist, die am Ende jeder Anforderung aufgerufen wird.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. Starten der Maschinelles Verarbeitung

Der folgende Code beginnt mit der Verarbeitung der Anforderung. Im Beispiel " **visiongesichter** " wird dieser Code ausgeführt, nachdem der Benutzer ein Bild ausgewählt hat:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Dieser Handler übergibt die `ciImage` an das Vision Framework `VNDetectFaceRectanglesRequest`, das in Schritt 1 erstellt wurde.

### <a name="3-handle-the-results-of-vision-processing"></a>3. behandeln der Ergebnisse der Maschinelles Verarbeitung

Sobald die Gesichtserkennung vollständig ist, führt der Handler die `HandleRectangles` Methode aus, die die Fehlerbehandlung ausführt und die Begrenzungen der erkannten Gesichter anzeigt, und ruft den `OverlayRectangles` auf, um umschließende Rechtecke auf der ursprünglichen Abbildung zu zeichnen:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. Anzeigen der Ergebnisse

Die `OverlayRectangles`-Methode im " **visiongesichter** "-Beispiel verfügt über drei Funktionen:

- Rendern des Quell Bilds
- Zeichnen eines Rechtecks für jedes erkannte Gesicht, und
- Hinzufügen einer Text Bezeichnung für jedes Gesicht mithilfe von CoreGraphics.

Sehen Sie sich die Quelle des Beispiels für die exakte CoreGraphics [-](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces) Methode an.

![Foto mit zwei Gesichtern erkannt](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. weitere Verarbeitung

Das Vision-Framework bietet zusätzliche Funktionen zum Erkennen von Gesichtsmerkmalen, wie z. b. Augen und Mund. Verwenden Sie den `VNDetectFaceLandmarksRequest` Typ, der `VNFaceObservation` Ergebnisse zurückgibt, wie in Schritt 3 oben, jedoch mit zusätzlichen `VNFaceLandmark` Daten.

## <a name="related-links"></a>Verwandte Links

- [Vision Rechtecke (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles)
- [Vision Gesichter (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces)
- [Fortschritte im Kern Image: Filter, Metal, Vision und mehr (WWDC) (Video)](https://developer.apple.com/videos/play/wwdc2017/510/)
