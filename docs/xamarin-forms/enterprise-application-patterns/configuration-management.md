---
title: Konfigurationsverwaltung
description: In diesem Kapitel wird erläutert, wie die eshoponcontainers-Mobile App die Konfigurations Verwaltung implementiert, um App-Einstellungen und Benutzereinstellungen bereitzustellen.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: cc83a18cd8c391c6228cf9d813ecf8bca795caba
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760297"
---
# <a name="configuration-management"></a>Konfigurationsverwaltung

Einstellungen ermöglichen die Trennung von Daten, die das Verhalten einer App aus dem Code konfigurieren, sodass das Verhalten geändert werden kann, ohne die APP neu zu erstellen. Es gibt zwei Arten von Einstellungen: App-Einstellungen und Benutzereinstellungen.

App-Einstellungen sind Daten, die von einer App erstellt und verwaltet werden. Es können Daten wie z. b. fixierte Webdienst-Endpunkte, API-Schlüssel und der Lauf Zeit Status enthalten. App-Einstellungen sind an das vorhanden sein der APP gebunden und sind nur für diese APP sinnvoll.

Benutzereinstellungen sind die anpassbaren Einstellungen einer APP, die sich auf das Verhalten der APP auswirken und keine häufige erneute Anpassung erfordern. Beispielsweise kann eine APP den Benutzer angeben, wo Daten abgerufen werden sollen und wie Sie auf dem Bildschirm angezeigt werden soll.

Xamarin. Forms enthält ein dauerhaftes Wörterbuch, das zum Speichern von Einstellungsdaten verwendet werden kann. Auf dieses Wörterbuch kann mithilfe der [`Application.Current.Properties`](xref:Xamarin.Forms.Application.Properties) -Eigenschaft zugegriffen werden, und alle darin enthaltenen Daten werden gespeichert, wenn die app in den Standbymodus wechselt, und werden wieder hergestellt, wenn die APP fortgesetzt oder neu gestartet wird. Außerdem verfügt die [`Application`](xref:Xamarin.Forms.Application) -Klasse auch über eine [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) -Methode, die es einer App ermöglicht, Ihre Einstellungen bei Bedarf zu speichern. Weitere Informationen zu diesem Wörterbuch finden Sie unter [Eigenschaften Wörterbuch](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

Ein Nachteil beim Speichern von Daten mithilfe des permanenten Wörterbuchs xamarin. Forms besteht darin, dass es nicht einfach an Daten gebunden ist. Daher verwendet das eshoponcontainers-Mobile App die XAM. Plugins. Settings-Bibliothek, die über [nuget](https://www.nuget.org/packages/Xam.Plugins.Settings/)verfügbar ist. Diese Bibliothek bietet eine konsistente, typsichere und plattformübergreifende Methode zum beibehalten und Abrufen von App-und Benutzereinstellungen, während die von den einzelnen Plattformen bereitgestellte Native Einstellungs Verwaltung verwendet wird. Außerdem ist es einfach, die Datenbindung zu verwenden, um auf Einstellungsdaten zuzugreifen, die von der Bibliothek verfügbar gemacht werden.

> [!NOTE]
> Obwohl in der XAM. plugin. Settings-Bibliothek sowohl App-als auch Benutzereinstellungen gespeichert werden können, wird nicht zwischen den beiden unterschieden.

## <a name="creating-a-settings-class"></a>Erstellen einer Einstellungs Klasse

Wenn Sie die XAM. Plugins. Settings-Bibliothek verwenden, sollte eine einzelne statische Klasse erstellt werden, die die APP-und Benutzereinstellungen enthält, die für die APP erforderlich sind. Das folgende Codebeispiel zeigt die Settings-Klasse im eshoponcontainers-Mobile App:

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

Einstellungen können über die `ISettings` API gelesen und geschrieben werden, die von der XAM. Plugins. Settings-Bibliothek bereitgestellt wird. Diese Bibliothek stellt einen Singleton bereit, der für den Zugriff auf die API `CrossSettings.Current`verwendet werden kann, und die Einstellungs Klasse einer APP sollte diesen Singleton `ISettings` über eine-Eigenschaft verfügbar machen.

> [!NOTE]
> Using-Direktiven für die "Plugin. Settings"-und "Plugin. Settings. Abstractions"-Namespaces sollten einer Klasse hinzugefügt werden, die Zugriff auf die XAM. Plugins. Settings-Bibliothekstypen benötigt.

## <a name="adding-a-setting"></a>Hinzufügen einer Einstellung

Jede Einstellung besteht aus einem Schlüssel, einem Standardwert und einer Eigenschaft. Das folgende Codebeispiel zeigt alle drei Elemente für eine Benutzereinstellung, die die Basis-URL für die Onlinedienste darstellt, mit der die eshoponcontainers-Mobile App eine Verbindung herstellt:

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

Der Schlüssel ist immer eine Konstante Zeichenfolge, die den Schlüsselnamen definiert. der Standardwert für die Einstellung ist ein statischer Schreib geschützter Wert des erforderlichen Typs. Durch die Angabe eines Standardwerts wird sichergestellt, dass ein gültiger Wert verfügbar ist, wenn eine nicht festgelegte Einstellung abgerufen wird.

Die `UrlBase` statische Eigenschaft verwendet zwei Methoden aus der `ISettings` API, um den Einstellungs Wert zu lesen oder zu schreiben. Die `ISettings.GetValueOrDefault` -Methode wird verwendet, um den Wert einer Einstellung aus dem plattformspezifischen Speicher abzurufen. Wenn kein Wert für die Einstellung definiert ist, wird stattdessen der Standardwert abgerufen. Ebenso wird die `ISettings.AddOrUpdateValue` -Methode verwendet, um den Wert einer Einstellung in einem plattformspezifischen Speicher beizubehalten.

Anstatt einen Standardwert in der `Settings` -Klasse zu definieren, erhält die `UrlBaseDefault` Zeichenfolge ihren Wert `GlobalSetting` aus der-Klasse. Das folgende Codebeispiel zeigt die `BaseEndpoint` -Eigenschaft `UpdateEndpoint` und die-Methode in dieser Klasse:

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Jedes Mal, `BaseEndpoint` wenn die-Eigenschaft fest `UpdateEndpoint` gelegt wird, wird die-Methode aufgerufen. Diese Methode aktualisiert eine Reihe von Eigenschaften, die alle auf der `UrlBase` Benutzereinstellung basieren, die von der `Settings` -Klasse bereitgestellt wird, die verschiedene Endpunkte darstellt, mit denen die eshoponcontainers-Mobile App eine Verbindung herstellt.

## <a name="data-binding-to-user-settings"></a>Datenbindung an Benutzereinstellungen

Im Mobile App eshoponcontainers werden `SettingsView` von zwei Benutzereinstellungen verfügbar gemacht. Mit diesen Einstellungen können Sie konfigurieren, ob die APP Daten von als docker-Container bereitgestellten als docker-Container abrufen soll oder ob die APP Daten von mockdiensten abrufen soll, die keine Internetverbindung benötigen. Beim Abrufen von Daten aus containerisierten microservices muss eine Basis Endpunkt-URL für die microservices angegeben werden. In Abbildung 7-1 wird `SettingsView` der angezeigt, wenn der Benutzer das Abrufen von Daten aus containerisierten microservices ausgewählt hat.

![](configuration-management-images/settings-endpoint.png "Benutzereinstellungen, die von der eshoponcontainers-Mobile App verfügbar gemacht werden")

**Abbildung 7-1**: Benutzereinstellungen, die von der eshoponcontainers-Mobile App verfügbar gemacht werden

Die Datenbindung kann zum Abrufen und Festlegen von Einstellungen verwendet werden, `Settings` die von der-Klasse verfügbar gemacht werden. Dies wird durch Steuerelemente auf der Ansichts Bindung erreicht, um Modell Eigenschaften anzuzeigen, die wiederum auf `Settings` Eigenschaften in der-Klasse zugreifen, und das Aufrufen einer Benachrichtigung über geänderte Eigenschaften, wenn sich der Einstellungs Wert geändert hat. Informationen dazu, wie die eshoponcontainers-Mobile App Ansichts Modelle erstellt und diese Ansichten zuordnet, finden Sie unter [Automatisches Erstellen eines Ansichts Modells mit einem Ansichts Modell-Locator](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Das folgende Codebeispiel zeigt das [`Entry`](xref:Xamarin.Forms.Entry) -Steuerelement `SettingsView` aus, das es dem Benutzer ermöglicht, eine Basis-Endpunkt-URL für die containerisierten microservices einzugeben:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Dieses [`Entry`](xref:Xamarin.Forms.Entry) Steuerelement wird mithilfe `Endpoint` einer bidirektionalen Bindung an die-Eigenschaft der `SettingsViewModel` -Klasse gebunden. Im folgenden Codebeispiel wird die Endpoint-Eigenschaft veranschaulicht:

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Wenn die `Endpoint` -Eigenschaft festgelegt `UpdateEndpoint` ist, wird die-Methode aufgerufen, vorausgesetzt, dass der angegebene Wert gültig ist und die Benachrichtigung über eine geänderte Eigenschaft ausgelöst wird. Die `UpdateEndpoint`-Methode wird in folgendem Codebeispiel veranschaulicht:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Diese Methode aktualisiert die `UrlBase` -Eigenschaft in `Settings` der-Klasse mit dem vom Benutzer eingegebenen Wert der Basis Endpunkt-URL, wodurch Sie im plattformspezifischen Speicher beibehalten wird.

Wenn auf `SettingsView` die navigiert wird, wird `InitializeAsync` die-Methode `SettingsViewModel` in der-Klasse ausgeführt. Im folgenden Codebeispiel wird diese Methode veranschaulicht:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

Mit der-Methode `Endpoint` wird die-Eigenschaft auf den `UrlBase` Wert der- `Settings` Eigenschaft in der-Klasse festgelegt. Der Zugriff `UrlBase` auf die-Eigenschaft bewirkt, dass die XAM. Plugins. Settings-Bibliothek den Einstellungs Wert aus dem plattformspezifischen Speicher abruft. Informationen dazu, wie die `InitializeAsync` -Methode aufgerufen wird, finden Sie unter [übergeben von Parametern während der Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Mit diesem Mechanismus wird sichergestellt, dass Benutzereinstellungen immer dann, wenn ein Benutzer zur einstellungenansicht navigiert, aus dem plattformspezifischen Speicher abgerufen und über die Datenbindung angezeigt werden. Wenn der Benutzer dann die Einstellungs Werte ändert, stellt die Datenbindung sicher, dass Sie sofort im plattformspezifischen Speicher beibehalten werden.

## <a name="summary"></a>Zusammenfassung

Einstellungen ermöglichen die Trennung von Daten, die das Verhalten einer App aus dem Code konfigurieren, sodass das Verhalten geändert werden kann, ohne die APP neu zu erstellen. App-Einstellungen sind Daten, die von einer App erstellt und verwaltet werden, und Benutzereinstellungen sind die anpassbaren Einstellungen einer APP, die sich auf das Verhalten der APP auswirken und keine häufige erneute Anpassung erfordern.

Die XAM. Plugins. Settings-Bibliothek bietet eine konsistente, typsichere und plattformübergreifende Methode zum beibehalten und Abrufen von App-und Benutzereinstellungen, und die Datenbindung kann verwendet werden, um auf die mit der Bibliothek erstellten Einstellungen zuzugreifen.

## <a name="related-links"></a>Verwandte Links

- [Download-e-Book (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eshoponcontainers (GitHub) (Beispiel)](https://github.com/dotnet-architecture/eShopOnContainers)
