---
title: Arbeiten mit dem UI-Thread in xamarin. IOS
description: In diesem Dokument wird beschrieben, wie Sie mit dem UI-Thread in xamarin. IOS arbeiten. Es wird erläutert, wie die Ausführung des UI-Threads, ein Hintergrund Thread Beispiel, und Async/warten überprüft wird.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: ee7ab7c5d0503cffd2c12a493f314f191d912e92
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002834"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Arbeiten mit dem UI-Thread in xamarin. IOS

Anwendungs Benutzerschnittstellen sind immer Single Thread, auch bei Multithread-Geräten – es gibt nur eine Darstellung des Bildschirms, und Änderungen an den angezeigten Änderungen müssen über einen einzelnen "Zugriffspunkt" koordiniert werden. Dadurch wird verhindert, dass mehrere Threads gleichzeitig versuchen, dasselbe Pixel zu aktualisieren (z. b.).

Der Code sollte nur Änderungen an den Steuerelementen der Benutzeroberfläche vom Hauptthread (oder UI) aus vornehmen. Alle Aktualisierungen der Benutzeroberfläche, die in einem anderen Thread (z. b. einem Rückruf oder einem Hintergrund Thread) auftreten, werden möglicherweise nicht auf dem Bildschirm gerendert oder können sogar zu einem Absturz führen.

## <a name="ui-thread-execution"></a>UI-Thread Ausführung

Wenn Sie Steuerelemente in einer Ansicht erstellen oder ein vom Benutzer initiiertes Ereignis, z. b. eine Berührung, behandeln, wird der Code bereits im Kontext des UI-Threads ausgeführt.

Wenn Code in einem Hintergrund Thread, in einer Aufgabe oder einem Rückruf ausgeführt wird, wird er wahrscheinlich nicht auf dem Hauptbenutzer Oberflächen-Thread ausgeführt. In diesem Fall sollten Sie den Code in einem aufzurufenden `InvokeOnMainThread` oder `BeginInvokeOnMainThread` wie folgt einschließen:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

Die `InvokeOnMainThread`-Methode wird auf `NSObject` definiert, sodass Sie aus Methoden aufgerufen werden kann, die für ein beliebiges UIKit-Objekt definiert sind (z. b. eine Ansicht oder ein Ansichts Controller).

Beim Debuggen von xamarin. IOS-Anwendungen wird ein Fehler ausgelöst, wenn der Code versucht, auf ein UI-Steuerelement vom falschen Thread aus zuzugreifen. Dies hilft Ihnen, diese Probleme mit der invokeonmainthread-Methode aufzuspüren und zu beheben. Dies tritt nur beim Debuggen auf und löst in Releasebuilds keinen Fehler aus. Die Fehlermeldung wird wie folgt angezeigt:

 ![](ui-thread-images/image10.png "UI Thread Execution")

 <a name="Background_Thread_Example" />

## <a name="background-thread-example"></a>Beispiel für Hintergrund Thread

Es folgt ein Beispiel, das versucht, auf ein Benutzeroberflächen Steuerelement (ein `UILabel`) von einem Hintergrund Thread aus mithilfe eines einfachen Threads zuzugreifen:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Dieser Code löst beim Debuggen die `UIKitThreadAccessException` aus. Um das Problem zu beheben (und sicherzustellen, dass nur über den Hauptbenutzer Oberflächen-Thread auf das Benutzeroberflächen Steuerelement zugegriffen wird), packen Sie Code, der auf UI-Steuerelemente verweist, in einem `InvokeOnMainThread` Ausdruck wie folgt

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

Sie müssen dies nicht für die restlichen Beispiele in diesem Dokument verwenden, aber es ist ein wichtiges Konzept, wenn Ihre APP Netzwerk Anforderungen stellt, das Benachrichtigungs Center oder andere Methoden verwendet, die einen Abschluss Handler erfordern, der auf einem anderen ausgeführt wird. Aden.

 <a name="Async_Await_Example" />

## <a name="asyncawait-example"></a>Beispiel für Async/Erwartung

Wenn Sie die C# 5 Async/erwarteschlüsselwörter verwenden`InvokeOnMainThread`ist nicht erforderlich, da die Methode bei Abschluss einer erwarteten Aufgabe im aufrufenden Thread fortgesetzt wird.

Dieser Beispielcode (der bei einem verzögerten Methodenaufruf, nur zu Demonstrationszwecken, erwartet wird) zeigt eine Async-Methode an, die im UI-Thread aufgerufen wird (es handelt sich um einen touchupin-Handler). Da die enthaltende Methode im UI-Thread aufgerufen wird, können UI-Vorgänge wie das Festlegen des Texts auf einem `UILabel` oder das zeigen einer `UIAlertView` sicher aufgerufen werden, nachdem asynchrone Vorgänge für Hintergrundthreads abgeschlossen wurden.

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

Wenn eine asynchrone Methode von einem Hintergrund Thread (nicht dem Hauptbenutzer Oberflächen-Thread) aufgerufen wird, ist `InvokeOnMainThread` weiterhin erforderlich.

## <a name="related-links"></a>Verwandte Links

- [Steuerelemente (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Threading](~/ios/app-fundamentals/threading.md)
