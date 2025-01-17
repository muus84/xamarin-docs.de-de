---
title: Text Übersetzung mithilfe der Translator-API
description: Die Microsoft Translator-API kann verwendet werden, um Sprache und Text über eine Rest-API zu übersetzen. In diesem Artikel wird erläutert, wie Sie mit dem Microsoft Textübersetzungs-API Text in einer xamarin. Forms-Anwendung in eine andere Sprache übersetzen.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 50d13532585e6edc3dac530558937ee6e0a02268
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032794"
---
# <a name="text-translation-using-the-translator-api"></a>Text Übersetzung mithilfe der Translator-API

[![Beispiel herunterladen](~/media/shared/download.png) Das Beispiel herunterladen](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Die Microsoft Translator-API kann verwendet werden, um Sprache und Text über eine Rest-API zu übersetzen. In diesem Artikel wird erläutert, wie Sie mit dem Microsoft Textübersetzungs-API Text in einer xamarin. Forms-Anwendung in eine andere Sprache übersetzen._

## <a name="overview"></a>Übersicht

Die Translator-API verfügt über zwei Komponenten:

- Eine textübersetzungs-Rest-API, mit der Text aus einer Sprache in Text einer anderen Sprache übersetzt werden soll. Die API erkennt automatisch die Sprache des Texts, der vor der Übersetzung gesendet wurde.
- Eine Sprachübersetzung-Rest-API, mit der Sprache aus einer Sprache in Text einer anderen Sprache umgewandelt wird. Die API integriert auch Text-in-Speech-Funktionen, um den übersetzten Text zurück zu sprechen.

Dieser Artikel konzentriert sich auf die Übersetzung von Text von einer Sprache in eine andere Sprache mithilfe der Textübersetzungs-API.

Ein API-Schlüssel muss für die Verwendung des Textübersetzungs-API abgerufen werden. Informationen hierzu finden Sie unter [registrieren für die Microsoft-Textübersetzungs-API](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Weitere Informationen zum Microsoft-Textübersetzungs-API finden Sie in der [Textübersetzungs-API-Dokumentation](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Authentifizierung

Jede Anforderung, die an den Textübersetzungs-API erfolgt, erfordert ein JSON Web Token (JWT)-Zugriffs Token, das beim `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`vom Cognitive Services-Tokendienst abgerufen werden kann. Ein Token kann abgerufen werden, indem eine Post-Anforderung an den Tokendienst gesendet wird. dabei wird eine `Ocp-Apim-Subscription-Key` Kopfzeile angegeben, die den API-Schlüssel als Wert enthält.

Im folgenden Codebeispiel wird gezeigt, wie Sie ein Zugriffs Token vom Tokendienst anfordern:

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

Das zurückgegebene Zugriffs Token, bei dem es sich um einen Base64-Text handelt, hat eine Ablaufzeit von 10 Minuten. Aus diesem Grund erneuert die Beispielanwendung alle 9 Minuten das Zugriffs Token.

Das Zugriffs Token muss in jedem Textübersetzungs-API Aufrufen als `Authorization` Header angegeben werden, dem die Zeichenfolge `Bearer`vorangestellt ist, wie im folgenden Codebeispiel gezeigt:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Weitere Informationen zum Cognitive Services-Tokendienst finden Sie unter [Authentifizierungs Token-API](https://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Ausführen von Text Übersetzung

Die Text Übersetzung kann erreicht werden, indem Sie eine GET-Anforderung an die `translate`-API bei `https://api.microsofttranslator.com/v2/http.svc/translate`senden. In der Beispielanwendung Ruft die `TranslateTextAsync`-Methode den Text Übersetzungsprozess auf:

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

Die `TranslateTextAsync`-Methode generiert einen Anforderungs-URI und ruft ein Zugriffs Token vom Tokendienst ab. Die Text Übersetzungs Anforderung wird dann an die `translate`-API gesendet, die eine XML-Antwort zurückgibt, die das Ergebnis enthält. Die XML-Antwort wird analysiert, und das Übersetzungsergebnis wird zur Anzeige an die Aufruf Methode zurückgegeben.

Weitere Informationen zu den Rest-APIs für die Text Übersetzung finden Sie unter [Microsoft Textübersetzungs-API](https://docs.microsofttranslator.com/text-translate.html).

### <a name="configuring-text-translation"></a>Konfigurieren von Text Übersetzung

Der Text Übersetzungsprozess kann durch Angabe von HTTP-Abfrage Parametern konfiguriert werden:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Diese Methode legt den Text fest, der übersetzt werden soll, und die Sprache, in die der Text übersetzt werden soll. Eine Liste der Sprachen, die von Microsoft Translator unterstützt werden, finden Sie [unter Unterstützte Sprachen in der Microsoft-Textübersetzungs-API](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Wenn eine Anwendung wissen muss, in welcher Sprache sich der Text befindet, kann die `Detect`-API aufgerufen werden, um die Sprache der Text Zeichenfolge zu ermitteln.

### <a name="sending-the-request"></a>Senden der Anforderung

Die `SendRequestAsync`-Methode führt die Get-Anforderung an die Rest-API der Text Übersetzung aus und gibt die Antwort zurück:

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Diese Methode erstellt die Get-Anforderung, indem das Zugriffs Token dem `Authorization`-Header hinzugefügt wird, dem die Zeichenfolge `Bearer`vorangestellt ist. Die Get-Anforderung wird dann an die `translate`-API gesendet, wobei die Anforderungs-URL den Text angibt, der übersetzt werden soll, und die Sprache, in die der Text übersetzt werden soll. Die Antwort wird dann gelesen und an die Aufruf Methode zurückgegeben.

Die `translate`-API sendet in der Antwort den HTTP-Statuscode 200 (OK), vorausgesetzt, die Anforderung ist gültig, was angibt, dass die Anforderung erfolgreich war und die angeforderten Informationen in der Antwort enthalten sind. Eine Liste möglicher Fehler Antworten finden Sie unter Response messages at [Get Translation](https://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>Verarbeiten der Antwort

Die API-Antwort wird im XML-Format zurückgegeben. Die folgenden XML-Daten zeigen eine typische erfolgreiche Antwortnachricht:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

In der Beispielanwendung wird die XML-Antwort in eine `XDocument`-Instanz analysiert, wobei der XML-Stamm Wert an die aufrufende Methode für die Anzeige zurückgegeben wird, wie in den folgenden Screenshots gezeigt:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurde erläutert, wie Sie mit dem Microsoft Textübersetzungs-API Text von einer Sprache in Text einer anderen Sprache in einer xamarin. Forms-Anwendung übersetzen. Zusätzlich zur Übersetzung von Text kann die Microsoft Translator-API auch Sprache aus einer Sprache in Text einer anderen Sprache umwandeln.

## <a name="related-links"></a>Verwandte Links

- [Textübersetzungs-API-Dokumentation](/azure/cognitive-services/translator/).
- [Nutzen eines Rest-Webdiensts](~/xamarin-forms/data-cloud/web-services/rest.md)
- [TODO-Cognitive Services (Beispiel)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Microsoft-Textübersetzungs-API](https://docs.microsofttranslator.com/text-translate.html).
