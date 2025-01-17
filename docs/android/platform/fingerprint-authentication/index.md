---
title: Fingerabdruckauthentifizierung
description: In diesem Handbuch wird erläutert, wie Sie die Fingerabdruckauthentifizierung, die in Android 6,0 eingeführt wurde, in eine xamarin. Android-Anwendung einfügen.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 4a4b6ee7a123683a9d5a140c46c0b3542767ffa3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027525"
---
# <a name="fingerprint-authentication"></a>Fingerabdruckauthentifizierung

_In diesem Handbuch wird erläutert, wie Sie die Fingerabdruckauthentifizierung, die in Android 6,0 eingeführt wurde, in eine xamarin. Android-Anwendung einfügen._

## <a name="fingerprint-authentication-overview"></a>Übersicht über Fingerabdruckauthentifizierung

Die Ankunft von Fingerabdruckscannern auf Android-Geräten bietet Anwendungen eine Alternative zur herkömmlichen Benutzernamen-/Kennwort-Methode der Benutzerauthentifizierung. Die Verwendung von Fingerabdrücken zum Authentifizieren eines Benutzers ermöglicht einer Anwendung die Einbindung von Sicherheit, die weniger stark aufdringlich ist als ein Benutzername und ein Kennwort.

Die fingerprintmanager-APIs richten sich an Geräte mit einem Fingerabdruckscanner und führen die API-Ebene 23 (Android 6,0) oder höher aus. Die APIs finden Sie im `Android.Hardware.Fingerprints`-Namespace. Die Android-Unterstützungs Bibliothek V4 stellt Versionen der Fingerabdruck-APIs bereit, die für ältere Android-Versionen vorgesehen sind. Die Kompatibilitäts-APIs, die im `Android.Support.v4.Hardware.Fingerprint`-Namespace enthalten sind, werden über das [xamarin. Android. Support. v4-nuget-Paket](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)verteilt.

Der [fingerprintmanager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (und das zugehörige Unterstützungs Bibliotheks Pendant, [fingerprintmanagercompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) ist die primäre Klasse für die Verwendung der Fingerabdruck-Scan Hardware. Bei dieser Klasse handelt es sich um einen Android SDK Wrapper um den systemebenendienst, der Interaktionen mit der Hardware selbst verwaltet. Er ist verantwortlich für das Starten des Fingerabdruckscanners und das reagieren auf Feedback des Scanners. Diese Klasse verfügt über eine relativ unkomplizierte Schnittstelle mit nur drei Membern:

- **`Authenticate`** &ndash; diese Methode den Hardware Scanner initialisiert und den Dienst im Hintergrund startet und darauf wartet, dass der Benutzer seinen Fingerabdruck scannt.
- **`EnrolledFingerprints`** &ndash; diese Eigenschaft `true` zurück, wenn der Benutzer mindestens einen Fingerabdruck beim Gerät registriert hat.
- **`HardwareDetected`** &ndash; diese Eigenschaft verwendet wird, um zu bestimmen, ob das Gerät Fingerabdruck Scans unterstützt.

Die `FingerprintManager.Authenticate`-Methode wird von einer Android-Anwendung verwendet, um den Fingerabdruckscanner zu starten. Der folgende Code Ausschnitt ist ein Beispiel dafür, wie Sie ihn mit den Kompatibilitäts-APIs der Unterstützungs Bibliothek aufrufen:

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

In dieser Anleitung wird erläutert, wie Sie die `FingerprintManager`-APIs verwenden, um eine Android-Anwendung mit Fingerabdruckauthentifizierung zu verbessern. Es wird erläutert, wie ein `CryptoObject` instanziiert und erstellt wird, um die Ergebnisse des Fingerabdruckscanners zu sichern. Wir untersuchen, wie eine Anwendung `FingerprintManager.AuthenticationCallback` Unterklassen und auf Feedback des Fingerabdruckscanners reagieren soll. Abschließend erfahren Sie, wie Sie einen Fingerabdruck auf einem Android-Gerät oder-Emulator registrieren und mit **ADB** einen Fingerabdruck Scan simulieren.

## <a name="requirements"></a>Anforderungen

Die Fingerabdruckauthentifizierung erfordert Android 6,0 (API-Ebene 23) oder höher und ein Gerät mit einem Fingerabdruckscanner. 

Für jeden Benutzer, der authentifiziert werden soll, muss bereits ein Fingerabdruck für das Gerät registriert sein. Dies umfasst das Einrichten einer Bildschirmsperre, die ein Kennwort, eine PIN, ein wischen-Muster oder eine Gesichtserkennung verwendet. Es ist möglich, einige der Fingerabdruck Authentifizierungsfunktionen in einem Android-Emulator zu simulieren.  Weitere Informationen zu diesen beiden Themen finden Sie im Abschnitt zum [Anmelden eines Fingerabdrucks](enrolling-fingerprint.md) . 

## <a name="related-links"></a>Verwandte Links

- [Beispiel-App für Fingerabdruck Handbuch](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [Beispiel für Fingerabdruck](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Anfordern von Berechtigungen zur Laufzeit](https://developer.android.com/training/permissions/requesting.html)
- [Android. Hardware. Fingerabdruck](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [Android. Support. v4. Hardware. Fingerabdruck](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android. Content. Context](xref:Android.Content.Context)
- [Fingerabdruck-und Zahlungs-API (Video)](https://youtu.be/VOn7VrTRlA4)
