---
title: Pushbenachrichtigungen in ios
description: In diesem Dokument wird beschrieben, wie Sie in ios 9 und früher mit Pushbenachrichtigungen arbeiten. Es werden Zertifikate erläutert, die beim Apple Push Notification Gateway-Dienst (APNs) registriert sind.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 468d0e16a3bd5745a243b2d7c09e642e3aeffd1d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031370"
---
# <a name="push-notifications-in-ios"></a>Pushbenachrichtigungen in ios

> [!IMPORTANT]
> Die Informationen in diesem Abschnitt beziehen sich auf IOS 9 und ältere Versionen, die Sie für ältere IOS-Versionen unterstützen. Informationen zu IOS 10 und höher finden Sie im [Handbuch zum Benutzer Benachrichtigungs Framework](~/ios/platform/user-notifications/index.md) , das sowohl lokale als auch Remote Benachrichtigungen auf einem IOS-Gerät unterstützt.

Pushbenachrichtigungen sollten kurz gehalten werden und nur genügend Daten enthalten, um die Mobile Anwendung zu benachrichtigen, dass Sie sich für ein Update an die Serveranwendung wenden sollte. Wenn z. b. neue e-Mail-Nachrichten eintreffen, wird die Mobile Anwendung nur von der Serveranwendung benachrichtigt, dass eine neue e-Mail eingetroffen ist. Die Benachrichtigung enthält nicht die neue e-Mail selbst. Die Mobile Anwendung ruft dann die neuen e-Mail-Nachrichten vom Server ab.

Im Mittelpunkt der Pushbenachrichtigungen unter IOS befindet sich der *Apple Push Notification Gateway-Dienst (APNs)* . Dabei handelt es sich um einen von Apple bereitgestellten Dienst, der für das Routing von Benachrichtigungen von einem Anwendungsserver an IOS-Geräte zuständig ist.
Die folgende Abbildung veranschaulicht die pushbenachrichtigungstopologie für ios:![](remote-notifications-in-ios-images/image4.png "Diese Abbildung veranschaulicht die pushbenachrichtigungstopologie für IOS.")

Bei Remote Benachrichtigungen selbst handelt es sich um JSON-formatierte Zeichen folgen, die dem Format und den Protokollen entsprechen, die im Abschnitt " [Benachrichtigungs Nutzlast](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) " des [lokalen und Push-Benachrichtigungs-Programmier Handbuchs](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) in der [IOS ](https://developer.apple.com/devcenter/ios/index.action).

Apple unterhält zwei Umgebungen von APNs: einen *Sandkasten* und eine *Produktions* Umgebung. Die Sandbox Umgebung ist für Tests während der Entwicklungsphase gedacht und wird unter `gateway.sandbox.push.apple.com` an TCP-Port 2195 gefunden. Die Produktionsumgebung soll in Anwendungen verwendet werden, die bereitgestellt wurden. Sie finden Sie unter `gateway.push.apple.com` an TCP-Port 2195.

## <a name="requirements"></a>Anforderungen

Für die Pushbenachrichtigung müssen die folgenden Regeln beachtet werden, die von der Architektur der APNs vorgegeben werden:

- **256 Byte-Nachrichten Limit** : die gesamte Nachrichtengröße der Benachrichtigung darf nicht mehr als 256 Byte betragen.
- **Keine bestätigungsbestätigung** -APNs stellt dem Absender keine Benachrichtigung bereit, dass eine Nachricht ihn an den beabsichtigten Empfänger gerichtet hat. Wenn das Gerät nicht erreichbar ist und mehrere sequenzielle Benachrichtigungen gesendet werden, gehen alle Benachrichtigungen außer den letzten Benachrichtigungen verloren. Nur die aktuelle Benachrichtigung wird an das Gerät übermittelt.
- **Jede Anwendung erfordert ein sicheres Zertifikat** : die Kommunikation mit APNs muss über SSL erfolgen.

## <a name="creating-and-using-certificates"></a>Erstellen und Verwenden von Zertifikaten

Für jede der im vorherigen Abschnitt erwähnten Umgebungen ist ein eigenes Zertifikat erforderlich. In diesem Abschnitt wird erläutert, wie Sie ein Zertifikat erstellen, einem Bereitstellungs Profil zuordnen und dann ein persönliches Informationsaustausch Zertifikat für die Verwendung mit pushsharp erhalten.

1. Wechseln Sie zum Erstellen von Zertifikaten zum IOS-Bereitstellungs Portal auf der Apple-Website, wie im folgenden Screenshot gezeigt (Beachten Sie das Menü Element App-IDs auf der linken Seite):

    [![](remote-notifications-in-ios-images/image5new.png "The iOS Provisioning Portal on Apples website")](remote-notifications-in-ios-images/image5new.png#lightbox)

2. Navigieren Sie als nächstes zum Abschnitt der APP-ID, und erstellen Sie eine neue APP-ID, wie im folgenden Screenshot zu sehen:

    [![](remote-notifications-in-ios-images/image6new.png "Navigate to the App IDs section and create a new app ID")](remote-notifications-in-ios-images/image6new.png#lightbox)

3. Wenn Sie auf die Schaltfläche " **+** " klicken, können Sie die Beschreibung und einen Bündel Bezeichner für die APP-ID eingeben, wie im folgenden Screenshot zu sehen:

    [![](remote-notifications-in-ios-images/image7new.png "Enter the description and a Bundle Identifier for the app ID")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Stellen Sie sicher, dass Sie die **explizite APP-ID** auswählen und dass der Bündel Bezeichner nicht mit einem `*` endet. Dadurch wird ein Bezeichner erstellt, der für mehrere Anwendungen geeignet ist, und pushbenachrichtigungszertifikate müssen für eine einzelne Anwendung verwendet werden.

5. Wählen Sie unter APP Services die Option **Pushbenachrichtigungen**:

    [![](remote-notifications-in-ios-images/image8new.png "Select Push Notifications")](remote-notifications-in-ios-images/image8new.png#lightbox)

6. Und drücken Sie **Submit** , um die Registrierung der neuen APP-ID zu bestätigen:

    [![](remote-notifications-in-ios-images/image9new.png "Confirm registration of the new App ID")](remote-notifications-in-ios-images/image9new.png#lightbox)

7. Als nächstes müssen Sie das Zertifikat für die APP-ID erstellen. Navigieren Sie im linken Navigationsbereich zu **Zertifikate > alle** , und wählen Sie die Schaltfläche `+` aus, wie im folgenden Screenshot zu sehen:

    [![](remote-notifications-in-ios-images/image10new.png "Create the certificate for the app ID")](remote-notifications-in-ios-images/image8.png#lightbox)

8. Wählen Sie aus, ob Sie ein Entwicklungs-oder Produktions Zertifikat verwenden möchten:

    [![](remote-notifications-in-ios-images/image11new.png "Select a Development or Production certificate")](remote-notifications-in-ios-images/image11new.png#lightbox)

9. Wählen Sie dann die neue APP-ID aus, die wir soeben erstellt haben:

    [![](remote-notifications-in-ios-images/image12new.png "Select the new App ID just created")](remote-notifications-in-ios-images/image12new.png#lightbox)

10. Dadurch werden Anweisungen angezeigt, die Sie durch die Erstellung einer *Zertifikat Signier Anforderung* mithilfe der **Keychain-Zugriffs** Anwendung auf Ihrem Mac führen.

11. Nachdem das Zertifikat erstellt wurde, muss es als Teil des Buildprozesses zum Signieren der Anwendung verwendet werden, damit es bei APNs registriert werden kann. Hierfür muss ein Bereitstellungs Profil erstellt und installiert werden, von dem das Zertifikat verwendet wird.

12. Um ein Entwicklungs Bereitstellungs Profil zu erstellen, navigieren Sie zum Abschnitt **Bereitstellungs profile** , und befolgen Sie die Schritte zum Erstellen, indem Sie die soeben erstellte APP-ID verwenden.

13. Nachdem Sie das Bereitstellungs Profil erstellt haben, öffnen Sie den **Xcode-Planer** , und aktualisieren Sie ihn. Wenn das von Ihnen erstellte Bereitstellungs Profil nicht angezeigt wird, ist es möglicherweise erforderlich, das Profil aus dem IOS-Bereitstellungs Portal herunterzuladen und manuell zu importieren. Der folgende Screenshot zeigt ein Beispiel für den Planer mit dem hinzugefügten Bereitstellungs Profil:  
    [![](remote-notifications-in-ios-images/image13new.png "This screen shot shows an example of the Organizer with the provision profile added")](remote-notifications-in-ios-images/image13new.png#lightbox)

14. An diesem Punkt müssen Sie das xamarin. IOS-Projekt so konfigurieren, dass dieses neu erstellte Bereitstellungs Profil verwendet wird. Dies erfolgt über das Dialogfeld " **Projektoptionen** " unter der Registerkarte " **IOS Bundle Signing** " (so wie im folgenden Screenshot gezeigt):  
    [![](remote-notifications-in-ios-images/image11.png "Configure the Xamarin.iOS project to use this newly created provisioning profile")](remote-notifications-in-ios-images/image11.png#lightbox)

An diesem Punkt ist die Anwendung für die Verwendung von Pushbenachrichtigungen konfiguriert. Mit dem Zertifikat sind jedoch noch einige weitere Schritte erforderlich. Dieses Zertifikat weist das Format "der" auf, das nicht mit pushsharp kompatibel ist, das ein Zertifikat für den persönlichen Informationsaustausch (PKCS12) erfordert. Um das Zertifikat so zu konvertieren, dass es von pushsharp verwendet werden kann, führen Sie die folgenden abschließenden Schritte aus:

1. **Herunterladen der Zertifikat Datei** : Melden Sie sich beim IOS-Bereitstellungs Portal an, wählen Sie die Registerkarte Zertifikate aus, wählen Sie das Zertifikat aus, das dem richtigen Bereitstellungs Profil zugeordnet ist, und wählen **Sie**
1. **Öffnen des Keychain-Zugriffs** : diese Anwendung ist eine GUI-Schnittstelle für das Kennwort-Verwaltungssystem in OS X.
1. **Zertifikat importieren** : Wenn das Zertifikat nicht bereits installiert ist, wird die **Datei... Importieren Sie Elemente** aus dem Menü Keychain-Zugriff. Navigieren Sie zum Zertifikat, das Sie zuvor exportiert haben, und wählen Sie es aus.
1. **Exportieren des Zertifikats** : Erweitern Sie das Zertifikat, damit der zugehörige private Schlüssel sichtbar ist, klicken Sie mit der rechten Maustaste auf den Schlüssel, und wählen Sie exportieren aus. Sie werden aufgefordert, einen Dateinamen und ein Kennwort für die exportierte Datei einzugeben.

An diesem Punkt werden Zertifikate erstellt. Wir haben ein Zertifikat erstellt, das zum Signieren von IOS-Anwendungen verwendet wird, und dieses Zertifikat in ein Format konvertiert, das in einer Serveranwendung mit pushsharp verwendet werden kann. Im nächsten Abschnitt sehen wir uns an, wie IOS-Anwendungen mit APNs interagieren.

## <a name="registering-with-apns"></a>Registrieren bei APNs

Bevor eine IOS-Anwendung eine Remote Benachrichtigung empfangen kann, muss Sie sich bei APNs registrieren. APNs generiert ein eindeutiges Geräte Token und gibt dieses an die IOS-Anwendung zurück. Die IOS-Anwendung übernimmt dann das Geräte Token und registriert sich dann beim Anwendungsserver. Sobald dies geschieht, ist die Registrierung vollständig, und der Anwendungsserver kann Benachrichtigungen per Push an das Mobile Gerät überführen.

Theoretisch kann sich das Geräte Token ändern, wenn sich eine IOS-Anwendung selbst bei APNs registriert, aber in der Praxis geschieht dies nicht häufig. Als Optimierung kann eine Anwendung das aktuellste Geräte Token Zwischenspeichern und den Anwendungsserver nur dann aktualisieren, wenn er sich ändert. Das folgende Diagramm veranschaulicht den Registrierungsvorgang und den Abruf eines Geräte Tokens:

 ![](remote-notifications-in-ios-images/image12.png "This diagram illustrates the process of registration and obtaining a device token")

Die Registrierung bei APNs wird in der `FinishedLaunching`-Methode der Anwendungs Delegatklasse behandelt, indem `RegisterForRemoteNotificationTypes` für das aktuelle `UIApplication`-Objekt aufgerufen wird. Wenn eine IOS-Anwendung bei APNs registriert wird, muss Sie auch angeben, welche Typen von Remote Benachrichtigungen Sie empfangen möchten. Diese Remote Benachrichtigungs Typen werden im Enumerations`UIRemoteNotificationType`deklariert. Der folgende Code Ausschnitt ist ein Beispiel dafür, wie eine IOS-Anwendung registriert werden kann, um Benachrichtigungen zu Remote Warnungen und Benachrichtigungen zu empfangen:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

Die APNs-Registrierungs Anforderung erfolgt im Hintergrund. wenn die Antwort empfangen wird, ruft IOS die Methode `RegisteredForRemoteNotifications` in der `AppDelegate`-Klasse auf und übergibt das registrierte Geräte Token. Das Token wird in einem `NSData` Objekt enthalten sein. Der folgende Code Ausschnitt zeigt, wie das von APNs bereitgestellte Geräte Token abgerufen wird:

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

Wenn die Registrierung aus irgendeinem Grund fehlschlägt (z. b. wenn das Gerät nicht mit dem Internet verbunden ist), ruft IOS `FailedToRegisterForRemoteNotifications` für die Anwendungs Delegatklasse auf. Der folgende Code Ausschnitt zeigt, wie eine Warnung angezeigt wird, die der Benutzer mitteilt, dass die Registrierung fehlgeschlagen ist:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Gerätetokenverwaltung

Geräte Token laufen ab oder ändern sich im Laufe der Zeit. Daher müssen Server Anwendungen einige Haus Bereinigungen durchführen und diese abgelaufenen oder geänderten Token bereinigen. Wenn eine Anwendung als Pushbenachrichtigung an ein Gerät sendet, das über ein abgelaufenes Token verfügt, wird das abgelaufene Token von APNs aufgezeichnet und gespeichert. Server können dann APNs Abfragen, um herauszufinden, welche Token abgelaufen sind.

APNs, die zur Bereitstellung eines *Feedback Dienstanbieter* verwendet werden: ein HTTPS-Endpunkt, der über das Zertifikat authentifiziert wird, das zum Senden von Pushbenachrichtigungen erstellt wurde, und Daten zu den abgelaufenen Token zurücksendet. Dies wurde von Apple als veraltet markiert und entfernt.

Stattdessen gibt es einen neuen HTTP-Statuscode für den Fall, der zuvor vom Feedback Dienst gemeldet wurde:

> 410-das Geräte Token ist für das Thema nicht mehr aktiv.

Außerdem wird ein neuer `timestamp` JSON-Datenschlüssel im Antworttext angezeigt:

> Wenn der Wert im Header ": Status" den Wert "410" hat, ist der Wert dieses Schlüssels der letzte Zeitpunkt, zu dem APNs bestätigt hat, dass das Geräte Token für das Thema nicht mehr gültig war.
>
> Setzen Sie die Pushbenachrichtigungen so lange aus, bis das Gerät ein Token mit einem späteren Zeitstempel beim Anbieter registriert.

## <a name="summary"></a>Zusammenfassung

In diesem Abschnitt werden die wichtigsten Konzepte in Bezug auf Pushbenachrichtigungen in ios vorgestellt. Es wurde die Rolle des Apple Push Notification Gateway-Diensts (APNs) erläutert. Anschließend wurde die Erstellung und Verwendung der Sicherheitszertifikate abgedeckt, die für APNs unverzichtbar sind. Schließlich wurde in diesem Dokument erläutert, wie Anwendungsserver die *Feedback Dienste* verwenden können, um das Nachverfolgen abgelaufener Geräte Token zu beenden.

## <a name="related-links"></a>Verwandte Links

- [Benachrichtigungen: Anzeigen von lokalen Benachrichtigungen und Remote Benachrichtigungen (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
- [Lokale und Pushbenachrichtigungen für Entwickler](https://developer.apple.com/notifications/)
- [UIApplication](https://docs.microsoft.com/dotnet/api/uikit.uiapplication)
- [Uiremotenotificationtype](https://docs.microsoft.com/dotnet/api/uikit.UIRemoteNotificationType)
