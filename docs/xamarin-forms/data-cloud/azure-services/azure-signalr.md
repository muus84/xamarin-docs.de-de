---
title: Azure signalr Service mit xamarin. Forms
description: Einstieg in den Azure signalr-Dienst und Azure Functions mit xamarin. Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: a4d0f5c5ceefcfe9a36a5fcf10c6fb4937c1db90
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739213"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Azure signalr Service mit xamarin. Forms

[![Beispiel herunterladen](~/media/shared/download.png) Das Beispiel herunterladen](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.net Core signalr ist ein Anwendungsmodell, das das Hinzufügen von Echtzeitkommunikation zu Anwendungen vereinfacht. Der Azure signalr Service ermöglicht eine schnelle Entwicklung und Bereitstellung skalierbarer signalr-Anwendungen. Azure Functions sind kurzlebige und Server lose Code Methoden, die kombiniert werden können, um ereignisgesteuerte, skalierbare Anwendungen zu bilden.

In diesem Artikel und Beispiel wird gezeigt, wie Sie den Azure signalr-Dienst und Azure Functions mit xamarin. Forms kombinieren, um Echtzeitnachrichten an verbundene Clients zu übermitteln.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Erstellen eines Azure signalr-Dienstanbieter und einer Azure Functions-App

Die Beispielanwendung umfasst drei Hauptkomponenten: einen Azure signalr Service-Hub, eine Azure Functions Instanz mit zwei Funktionen und eine mobile Anwendung, die Nachrichten senden und empfangen kann. Diese Komponenten interagieren wie folgt:

1. Die Mobile Anwendung ruft eine **Aushandlungs** -Azure-Funktion auf, um Informationen über den signalr-Hub zu erhalten.
1. Die Mobile Anwendung verwendet die Aushandlungs Informationen, um sich selbst beim signalr-Hub zu registrieren, und bildet eine Verbindung.
1. Nach der Registrierung sendet die Mobile Anwendung Nachrichten an die Azure-Funktion von **Talk** .
1. Die **Talk** -Funktion übergibt die eingehende Nachricht an den signalr-Hub.
1. Der signalr-Hub überträgt die Nachricht an alle verbundenen mobilen Anwendungs Instanzen, einschließlich des ursprünglichen Absenders.

> [!NOTE]
> Die **Aushandlungs** -und **Talk** -Funktionen in der Beispielanwendung können lokal mit Visual Studio 2019 und den Azure-Lauf Zeit Tools ausgeführt werden. Allerdings kann der Azure signalr-Dienst nicht lokal emuliert werden, und es ist schwierig, Lokal gehostete Azure Functions auf physischen oder virtuellen Geräten zu Testzwecken verfügbar zu machen. Es wird empfohlen, die Azure Functions in einer Azure Functions-app-Instanz bereitzustellen, da dies plattformübergreifende Tests ermöglicht. Weitere Informationen zur Bereitstellung finden Sie unter Bereitstellen von [Azure Functions mit Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Erstellen eines Azure signalr-Dienstanbieter

Ein Azure signalr-Dienst kann erstellt werden, indem Sie in der linken oberen Ecke der Azure-Portal **Ressource erstellen** auswählen und nach **signalr**suchen. Der Azure signalr Service kann im Free-Tarif erstellt werden. Der Azure signalr-Dienst muss sich im **Server losen** Dienst Modus befinden. Wenn Sie versehentlich den Dienst Modus Standard oder klassisch ausgewählt haben, können Sie ihn später in den Eigenschaften des Azure signalr-Dienstanbieter ändern.

Der folgende Screenshot zeigt die Erstellung eines neuen Azure signalr-Dienstanbieter:

![Screenshot der Azure signalr-Dienst Erstellung im Azure-Portal](azure-signalr-images/azure-signalr-create.png "Erstellen eines Azure signalr-Dienstanbieter")

Nach der Erstellung enthält der Abschnitt " **Schlüssel** " eines Azure signalr-Dienstanbieter eine **Verbindungs Zeichenfolge**, die verwendet wird, um die Azure Functions-App mit dem signalr-Hub zu verbinden. Der folgende Screenshot zeigt, wo Sie die Verbindungs Zeichenfolge im Azure signalr-Dienst finden:

![Screenshot der Azure signalr-Verbindungs Zeichenfolge im Azure-Portal](azure-signalr-images/azure-signalr-connection-string.png "Azure signalr-Verbindungs Zeichenfolge")

Diese Verbindungs Zeichenfolge wird zum Bereitstellen von [Azure Functions mit Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019)verwendet.

### <a name="create-an-azure-functions-app"></a>Erstellen einer Azure Functions-App

Um die Beispielanwendung zu testen, sollten Sie eine neue Azure Functions-app in der Azure-Portal erstellen. Notieren Sie sich den **APP-Namen** , da diese URL in der **Constants.cs** -Beispieldatei verwendet wird. Der folgende Screenshot zeigt die Erstellung einer neuen Azure Functions-App mit dem Namen "xdocsfunctions":

[![Screenshot Azure Functions App-Erstellung](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Azure Functions kann in einer Azure Functions-app-Instanz aus Visual Studio 2019 bereitgestellt werden. In den folgenden Abschnitten wird die Bereitstellung von zwei Funktionen in der Beispielanwendung für eine Azure Functions-app-Instanz beschrieben.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Erstellen Azure Functions in Visual Studio 2019

Die Beispielanwendung enthält eine Klassenbibliothek mit dem Namen **Chatserver**, die zwei Server lose Azure Functions in Dateien mit dem Namen **Negotiate.cs** und **Talk.cs**enthält.

Die `Negotiate`-Funktion antwortet auf Webanforderungen mit einem `SignalRConnectionInfo`-Objekt, das eine `AccessToken`-Eigenschaft und eine `Url`-Eigenschaft enthält. Die Mobile Anwendung verwendet diese Werte, um sich selbst beim signalr-Hub zu registrieren. Der folgende Code zeigt die `Negotiate`-Funktion:

```csharp
[FunctionName("Negotiate")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous,"get",Route = "negotiate")]
    HttpRequest req,
    [SignalRConnectionInfo(HubName = "simplechat")]
    SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

Die `Talk` Funktion antwortet auf HTTP POST-Anforderungen, die im Post-Text ein Nachrichten Objekt bereitstellen. Der Post-Text wird in einen `SignalRMessage` transformiert und an den signalr-Hub weitergeleitet. Der folgende Code zeigt die `Talk`-Funktion:

```csharp
[FunctionName("Talk")]
public static async Task<IActionResult> Run(
    [HttpTrigger(
        AuthorizationLevel.Anonymous,
        "post",
        Route = "talk")]
    HttpRequest req,
    [SignalR(HubName = "simplechat")]
    IAsyncCollector<SignalRMessage> questionR,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string json = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic obj = JsonConvert.DeserializeObject(json);

        var name = obj.name.ToString();
        var text = obj.text.ToString();

        var jObject = new JObject(obj);

        await questionR.AddAsync(
            new SignalRMessage
            {
                Target = "newMessage",
                Arguments = new[] { jObject }
            });

        return new OkObjectResult($"Hello {name}, your message was '{text}'");
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("There was an error: " + ex.Message);
    }
}
```

Weitere Informationen zu Azure Functions und Azure Functions-apps finden Sie in [Azure Functions Dokumentation](/azure/azure-functions/).

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Bereitstellen von Azure Functions mit Visual Studio 2019

Mit Visual Studio 2019 können Sie Funktionen für eine Azure Functions-App bereitstellen. In Azure gehostete Funktionen vereinfachen plattformübergreifende Tests durch Bereitstellung eines verfügbaren Test Endpunkts für alle Geräte.

Wenn Sie Azure Functions mit der rechten Maustaste auf die Beispiel Funktions-APP klicken und **veröffentlichen** auswählen, wird das Dialogfeld geöffnet Wenn Sie die vorherigen Schritte zum Einrichten einer Azure-Funktionen-App befolgt haben, können Sie **vorhandene auswählen** auswählen, um die Beispielanwendungen in ihrer Azure Functions-APP zu veröffentlichen. Der folgende Screenshot zeigt die Optionen für das Veröffentlichen von Dialogfeld in Visual Studio 2019:

![Dialogfeld "Auswahl veröffentlichen" in Visual Studio 2019](azure-signalr-images/vs-publish-target.png "Veröffentlichungs Optionen in Visual Studio 2019")

Nachdem Sie sich bei Ihrem Microsoft-Konto angemeldet haben, können Sie Ihre Azure Functions-App als Veröffentlichungsziel suchen und auswählen. Der folgende Screenshot zeigt ein Beispiel Azure Functions-App im Visual Studio 2019-Veröffentlichungs Dialogfeld:

![Eine Azure Functions-App im Visual Studio 2019-Veröffentlichungs Dialogfeld](azure-signalr-images/vs-app-selection.png "Azure Functions-App im Visual Studio 2019-Veröffentlichungs Dialogfeld")

Nachdem Sie eine Azure Functions app-Instanz ausgewählt haben, werden die Website-URL, die Konfiguration und andere Informationen über die Ziel Azure Functions-App angezeigt. Wählen Sie **Azure App Service Einstellungen bearbeiten** aus, und geben Sie die Verbindungs Zeichenfolge im Feld **Remote** ein. Die Verbindungs Zeichenfolge wird von den **Aushandlungs** -und **Talk** -Funktionen verwendet, um eine Verbindung mit dem Azure signalr-Dienst herzustellen, und ist im Abschnitt " **Schlüssel** " des Azure signalr-Dienstanbieter im Azure-Portal verfügbar Weitere Informationen zur Verbindungs Zeichenfolge finden Sie unter [Erstellen eines Azure signalr-Dienstanbieter](#create-an-azure-signalr-service).

Nachdem Sie die Verbindungs Zeichenfolge eingegeben haben, können Sie auf **veröffentlichen** klicken, um die Funktionen für die Azure Functions-App bereitzustellen. Nach Abschluss des Vorgangs werden die Funktionen in der Azure Functions-App im Azure-Portal aufgelistet. Der folgende Screenshot zeigt die veröffentlichten Funktionen in der Azure-Portal:

![In der Azure Functions-App veröffentlichte Funktionen](azure-signalr-images/azure-functions-deployed.png "In der Azure Functions-App veröffentlichte Funktionen")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Integrieren von Azure signalr Service mit xamarin. Forms

Die Integration zwischen dem Azure signalr-Dienst und der xamarin. Forms-Anwendung ist eine signalr-Dienstklasse, die in der `MainPage`-Klasse instanziiert wird, mit Ereignis Handlern, die drei Ereignissen zugewiesen sind. Weitere Informationen zu diesen Ereignis Handlern finden Sie unter [Verwenden der signalr-Dienstklasse in xamarin. Forms](#use-the-signalr-service-class-in-xamarinforms).

Die Beispielanwendung enthält eine **Constants.cs** -Klasse, die mit dem URL-Endpunkt ihrer Azure Functions-App angepasst werden muss. Legen Sie den Wert der `HostName`-Eigenschaft auf die Adresse Ihrer Azure Functions-App fest. Der folgende Code zeigt die **Constants.cs** -Eigenschaften mit einem Beispiel `HostName` Werts:

```csharp
public static class Constants
{
    public static string HostName { get; set; } = "https://example-functions-app.azurewebsites.net/";

    // Used to differentiate message types sent via SignalR. This
    // sample only uses a single message type.
    public static string MessageName { get; set; } = "newMessage";

    public static string Username
    {
        get
        {
            return $"{Device.RuntimePlatform} User";
        }
    }
}
```

> [!NOTE]
> Die `Username`-Eigenschaft in der **Constants.cs** -Beispieldatei verwendet den `RuntimePlatform` Wert des Geräts als Benutzername. Dies vereinfacht das Testen von Geräten plattformübergreifend und identifiziert das Gerät, das die Nachricht sendet. In einer realen Anwendung wäre dieser Wert wahrscheinlich ein eindeutiger Benutzername, der während der Registrierung oder Anmeldung erfasst wird.

### <a name="the-signalr-service-class"></a>Die signalr-Dienstklasse

Die `SignalRService`-Klasse im **Chatclient** -Projekt in der Beispielanwendung zeigt eine-Implementierung, die Funktionen in einer Azure Functions-App zum Herstellen einer Verbindung mit einem Azure signalr-Dienst aufruft.

Die `SendMessageAsync`-Methode in der `SignalRService`-Klasse wird zum Senden von Nachrichten an Clients verwendet, die mit dem Azure signalr-Dienst verbunden sind. Diese Methode führt eine HTTP POST-Anforderung an die **Talk** -Funktion aus, die in der Azure Functions-App gehostet wird, einschließlich eines JSON-serialisierten `Message`-Objekts als Post-Nutzlast. Die Funktion **Talk** übergibt die Nachricht an den Azure signalr-Dienst für die Übertragung an alle verbundenen Clients. Der folgende Code veranschaulicht die `SendMessageAsync`-Methode:

```csharp
public async Task SendMessageAsync(string username, string message)
{
    IsBusy = true;

    var newMessage = new Message
    {
        Name = username,
        Text = message
    };

    var json = JsonConvert.SerializeObject(newMessage);
    var content = new StringContent(json, Encoding.UTF8, "application/json");
    var result = await client.PostAsync($"{Constants.HostName}/api/talk", content);

    IsBusy = false;
}
```

Die `ConnectAsync`-Methode in der `SignalRService`-Klasse führt eine HTTP GET-Anforderung an die **Aushandlungs** Funktion aus, die in der Azure Functions App gehostet wird. Die **Aushandlungs** Funktion gibt JSON zurück, das in eine Instanz der `NegotiateInfo`-Klasse deserialisiert wird. Nachdem das `NegotiateInfo` Objekt abgerufen wurde, wird es verwendet, um sich direkt beim Azure signalr-Dienst mithilfe einer Instanz der `HubConnection`-Klasse zu registrieren.

ASP.net Core signalr überträgt eingehende Daten von der geöffneten Verbindung in Nachrichten und ermöglicht Entwicklern das Definieren von Nachrichten Typen und das Binden von Ereignis Handlern an eingehende Nachrichten nach Typ. Die `ConnectAsync`-Methode registriert einen Ereignishandler für den Nachrichten Namen, der in der **Constants.cs** -Datei der Beispielanwendung definiert ist. diese ist standardmäßig "newMessage".

Der folgende Code veranschaulicht die `ConnectAsync`-Methode:

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
            .WithUrl(negotiate.Url, options =>
            {
                options.AccessTokenProvider = async () => negotiate.AccessToken;
            })
            .Build();

        connection.On<JObject>(Constants.MessageName, AddNewMessage);
        await connection.StartAsync();

        IsConnected = true;
        IsBusy = false;

        Connected?.Invoke(this, true, "Connection successful.");
    }
    catch (Exception ex)
    {
        ConnectionFailed?.Invoke(this, false, ex.Message);
    }
}
```

Die `AddNewMessage`-Methode wird wie im vorherigen Code gezeigt als Ereignishandler in der `ConnectAsync`-Nachricht gebunden. Wenn eine Nachricht empfangen wird, wird die `AddNewMessage`-Methode mit den Nachrichten Daten aufgerufen, die als `JObject` bereitgestellt werden. Die `AddNewMessage`-Methode konvertiert die `JObject` in eine Instanz der `Message`-Klasse und ruft dann den Handler für `NewMessageReceived` auf, wenn ein solcher gebunden wurde. Der folgende Code veranschaulicht die `AddNewMessage`-Methode:

```csharp
public void AddNewMessage(JObject message)
{
    Message messageModel = new Message
    {
        Name = message.GetValue("name").ToString(),
        Text = message.GetValue("text").ToString(),
        TimeReceived = DateTime.Now
    };

    NewMessageReceived?.Invoke(this, messageModel);
}
```

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Verwenden der signalr-Dienstklasse in xamarin. Forms

Die Verwendung der signalr-Dienstklasse in xamarin. Forms erfolgt durch das Binden der `SignalRService`-Klassen Ereignisse in der `MainPage` Code Behind-Klasse.

Das `Connected`-Ereignis in der `SignalRService`-Klasse wird ausgelöst, wenn eine signalr-Verbindung erfolgreich abgeschlossen wurde. Das `ConnectionFailed`-Ereignis in der `SignalRService`-Klasse wird ausgelöst, wenn eine signalr-Verbindung fehlschlägt. Die `SignalR_ConnectionChanged`-Ereignishandlermethode ist an beide Ereignisse im `MainPage`-Konstruktor gebunden. Dieser Ereignishandler aktualisiert die Status der Verbindungs-und sende Schaltfläche auf der Grundlage des Verbindungs `success` Arguments und fügt die von dem Ereignis bereitgestellte Meldung mithilfe der `AddMessage`-Methode der Chat Warteschlange hinzu. Der folgende Code zeigt die `SignalR_ConnectionChanged` Ereignishandlermethode:

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

Das `NewMessageReceived`-Ereignis in der `SignalRService`-Klasse wird ausgelöst, wenn eine neue Nachricht vom Azure signalr-Dienst empfangen wird. Die `SignalR_NewMessageReceived`-Ereignishandlermethode ist an das `NewMessageReceived`-Ereignis im `MainPage`-Konstruktor gebunden. Dieser Ereignishandler konvertiert das eingehende `Message` Objekt in eine Zeichenfolge und fügt es mithilfe der `AddMessage`-Methode der Chat Warteschlange hinzu. Der folgende Code zeigt die `SignalR_NewMessageReceived` Ereignishandlermethode:

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

Die `AddMessage`-Methode fügt der Chat Warteschlange eine neue Nachricht als `Label` Objekt hinzu. Die `AddMessage`-Methode wird häufig von Ereignis Handlern von außerhalb des Hauptbenutzer Oberflächen-Threads aufgerufen, sodass die Aktualisierungen der Benutzeroberfläche im Haupt Thread erzwungen werden, um Ausnahmen zu verhindern. Der folgende Code veranschaulicht die `AddMessage`-Methode:

```csharp
void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        Label label = new Label
        {
            Text = message,
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        };

        messageList.Children.Add(label);
    });
}
```

## <a name="test-the-application"></a>Testen der Anwendung

Die signalr Chat-Anwendung kann auf Ios, Android und UWP getestet werden, sofern Sie über Folgendes verfügen:

1. Sie haben einen Azure signalr-Dienst erstellt.
1. Eine Azure Functions-App wurde erstellt.
1. Die **Constants.cs** -Datei wurde mit dem Azure Functions App-Endpunkt angepasst.

Sobald diese Schritte abgeschlossen sind und die Anwendung ausgeführt wird, wird durch Klicken auf die Schaltfläche **verbinden** eine Verbindung mit dem Azure signalr-Dienst hergestellt. Wenn Sie eine Nachricht eingeben und auf die Schaltfläche " **senden** " klicken, werden Meldungen in der Chat Warteschlange für jede verbundene Mobile Anwendung angezeigt.

## <a name="related-links"></a>Verwandte Links

* [Erstellen von mobilen Echtzeitanwendungen mit xamarin und signalr](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Einführung zu SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Einführung in Azure Functions](/azure/azure-functions/functions-overview)
* [Dokumentation zu Azure Functions](/azure/azure-functions/)
* [MVVM signalr Chat-Beispiel](https://github.com/lbugnion/sample-xamarin-signalr)
