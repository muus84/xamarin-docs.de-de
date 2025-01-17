---
title: 'Xamarin.Essentials: Erkennen einer Schüttelbewegung'
description: Mit der Klasse „Accelerometer“ von Xamarin.Essentials können Sie ermitteln, wenn das Gerät geschüttelt wird.
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: d2aef2c32b3fe2815f1176779a8c28ae8d421ed1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832600"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials: Erkennen einer Schüttelbewegung

Mit der **[Accelerometer](accelerometer.md)** -Klasse können Sie den Sensor für die Beschleunigungsmessung des Geräts überwachen, der die Beschleunigung des Geräts im dreidimensionalen Raum angibt. Außerdem können Sie Ereignisse einrichten, die ausgelöst werden, wenn der Benutzer das Gerät schüttelt.

## <a name="get-started"></a>Erste Schritte

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>Verwenden der Erkennung von Schüttelbewegungen

Fügen Sie Ihrer Klasse einen Verweis auf Xamarin.Essentials hinzu:

```csharp
using Xamarin.Essentials;
```

Zum Erkennen von Schüttelbewegungen des Geräts müssen Sie die Beschleunigungsmesserfunktionalität verwenden, indem Sie die Methoden `Start` und `Stop` aufrufen, um auf Änderungen an der Beschleunigung zu lauschen und das Schütteln zu erkennen. Jedes Mal, wenn eine Schüttelbewegung erkannt wird, wird ein `ShakeDetected`-Ereignis ausgelöst. Es wird empfohlen, `Game` oder schneller für die `SensorSpeed` zu verwenden. Sie können sie z.B. wie folgt verwenden:

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="implementation-details"></a>Implementierungsdetails

Die API zum Erkennen von Schüttelbewegungen verwendet unformatierte Messwerte vom Beschleunigungsmesser, um die Beschleunigung zu berechnen. Sie nutzt einen einfachen Warteschlangenmechanismus, um zu ermitteln, ob 3/4 der letzten Beschleunigungsmesserereignisse in der letzten halben Sekunde aufgetreten sind. Die Beschleunigung wird durch Addieren der X-, Y- und Z-Messwerte des Beschleunigungsmessers zum Quadrat und Vergleichen mit einem bestimmten Schwellenwert berechnet.

## <a name="api"></a>API

- [Quellcode für einen Beschleunigungsmesser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Dokumentation für Beschleunigungsmesser-API](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Zugehörige Videos

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Detect-Shake-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
