---
title: Arbeiten mit dem Android-Manifest
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 1438c012608b367c21ebcc401c058b186b917f53
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027805"
---
# <a name="working-with-the-android-manifest"></a>Arbeiten mit dem Android-Manifest

" **Androidmanifest. XML** " ist eine leistungsfähige Datei auf der Android-Plattform, die es Ihnen ermöglicht, die Funktionalität und die Anforderungen Ihrer Anwendung für Android zu beschreiben. Es ist jedoch nicht einfach, damit zu arbeiten. Mit xamarin. Android können Sie diese Schwierigkeit minimieren, indem Sie Ihren Klassen benutzerdefinierte Attribute hinzufügen, die dann zum automatischen Generieren des Manifests für Sie verwendet werden. Unser Ziel ist, dass 99% unserer Benutzer niemals " **androidmanifest. XML**" manuell ändern müssen. 

" **Androidmanifest. XML** " wird als Teil des Buildprozesses generiert, und der in " **Properties/androidmanifest. XML** " gefundene XML-Code wird mit XML zusammengeführt, das aus benutzerdefinierten Attributen generiert wird. Die sich ergebende zusammengeführte Datei " **androidmanifest. XML** " befindet sich im **obj** -Unterverzeichnis. Er befindet sich beispielsweise bei " **obj/Debug/Android/androidmanifest. XML** " für Debugbuilds. Der Zusammenführungsprozess ist trivial: er verwendet benutzerdefinierte Attribute innerhalb des Codes, um XML-Elemente zu generieren, und *fügt* diese Elemente in **androidmanifest. XML**ein. 

## <a name="the-basics"></a>Die Grundlagen

Zum Zeitpunkt der Kompilierung werden Assemblys auf nicht`abstract` Klassen überprüft, die von einer [Aktivität](xref:Android.App.Activity) abgeleitet werden und für die das [`[Activity]`](xref:Android.App.ActivityAttribute) Attribut deklariert ist. Anschließend werden diese Klassen und Attribute verwendet, um das Manifest zu erstellen. Beachten Sie z. B. folgenden Code: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Dies führt dazu, dass nichts in " **androidmanifest. XML**" generiert wird. Wenn Sie ein `<activity/>` Element generieren möchten, müssen Sie das [`[Activity]`](xref:Android.App.Activity) 
benutzerdefiniertes Attribut: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Dieses Beispiel bewirkt, dass das folgende XML-Fragment zu " **androidmanifest. XML**" hinzugefügt wird:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

Das `[Activity]`-Attribut hat keine Auswirkung auf `abstract` Typen. `abstract` Typen werden ignoriert.

### <a name="activity-name"></a>Name der Aktivität

Ab xamarin. Android 5,1 basiert der Typname einer Aktivität auf der md5sum des durch die Assembly qualifizierten Namens des exportierten Typs. Dadurch kann derselbe voll qualifizierte Name von zwei verschiedenen Assemblys bereitgestellt werden, und es wird kein Paketfehler ausgegeben. (Vor xamarin. Android 5,1 wurde der Standardtyp Name der Aktivität aus dem Namespace "Kleinbuchstaben" und dem Klassennamen erstellt.) 

Wenn Sie diese Standardeinstellung außer Kraft setzen und den Namen der Aktivität explizit angeben möchten, verwenden Sie die [`Name`](xref:Android.App.ActivityAttribute.Name) -Eigenschaft: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

In diesem Beispiel wird das folgende XML-Fragment erzeugt:

```xml
<activity android:name="awesome.demo.activity" />
```

> [!NOTE]
> Die `Name`-Eigenschaft sollte nur aus Gründen der Abwärtskompatibilität verwendet werden, da eine solche Umbenennung zur Laufzeit die Typsuche verlangsamen kann. Wenn Sie über Legacy Code verfügen, der erwartet, dass der standardtypname der Aktivität auf dem Namespace mit Kleinbuchstaben und dem Klassennamen basiert, finden Sie unter [Android Callable Wrapper Naming](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) Tipps zur Beibehaltung der Kompatibilität. 

### <a name="activity-title-bar"></a>Aktivitäts Titelleiste

Android übergibt Ihrer Anwendung standardmäßig eine Titelleiste, wenn Sie ausgeführt wird. Der Wert, der für diese verwendet wird, ist [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label). In den meisten Fällen unterscheidet sich dieser Wert vom Namen Ihrer Klasse. Um die Bezeichnung Ihrer APP auf der Titelleiste anzugeben, verwenden Sie die [`Label`](xref:Android.App.ActivityAttribute.Label) -Eigenschaft.
Beispiel: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

In diesem Beispiel wird das folgende XML-Fragment erzeugt:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

### <a name="launchable-from-application-chooser"></a>Startbare über die Anwendungs Auswahl

Standardmäßig wird Ihre Aktivität nicht auf dem Anwendungsstart Programm-Bildschirm von Android angezeigt. Dies liegt daran, dass in Ihrer Anwendung wahrscheinlich viele Aktivitäten vorhanden sind und Sie kein Symbol für jedes dieser Aktivitäten benötigen. Verwenden Sie die [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) -Eigenschaft, um anzugeben, welche über das Anwendungs Startfeld startbar sein soll. Beispiel: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

In diesem Beispiel wird das folgende XML-Fragment erzeugt:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

### <a name="activity-icon"></a>Aktivitäts Symbol

Standardmäßig wird Ihrer Aktivität das Standard Start Programmsymbol zugewiesen, das vom System bereitgestellt wird. Wenn Sie ein benutzerdefiniertes Symbol verwenden möchten, fügen Sie zuerst die **PNG** **-Datei Ressourcen/drawable**hinzu, legen Sie die Buildaktion auf **androidresource**fest, und geben Sie dann mithilfe der [`Icon`](xref:Android.App.ActivityAttribute.Icon) -Eigenschaft das zu verwendende Symbol an Beispiel: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

In diesem Beispiel wird das folgende XML-Fragment erzeugt:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

### <a name="permissions"></a>Berechtigungen

Wenn Sie dem Android-Manifest Berechtigungen hinzufügen (wie unter [Hinzufügen von Berechtigungen zu einem Android-Manifest](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)beschrieben), werden diese Berechtigungen in " **Properties/androidmanifest. XML**" aufgezeichnet. Wenn Sie z. b. die `INTERNET`-Berechtigung festlegen, wird " **Properties/androidmanifest. XML**" Folgendes Element hinzugefügt: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Debugbuilds legen automatisch einige Berechtigungen fest, um das Debuggen zu vereinfachen (z. b. `INTERNET` und `READ_EXTERNAL_STORAGE`) &ndash; diese Einstellungen nur in der generierten Datei " **obj/Debug/Android/androidmanifest. XML** " festgelegt sind und nicht als aktiviert angezeigt werden.  **Berechtigungs** Einstellungen. 

Wenn Sie z. b. die generierte Manifest-Datei unter " **obj/Debug/Android/androidmanifest. XML**" untersuchen, werden möglicherweise die folgenden hinzugefügten Berechtigungs Elemente angezeigt: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

In der releasebuildversion des Manifests (bei **obj/Debug/Android/androidmanifest. XML**) werden diese Berechtigungen *nicht* automatisch konfiguriert. Wenn Sie feststellen, dass der Wechsel zu einem Releasebuild bewirkt, dass Ihre APP eine im Debugbuild verfügbare Berechtigung verliert, vergewissern Sie sich, dass Sie diese Berechtigung explizit in den **erforderlichen Berechtigungs** Einstellungen für Ihre APP festgelegt haben (siehe **Build > Android Anwendung** in Visual Studio für Mac; Weitere Informationen finden Sie unter **Eigenschaften > Android-Manifest** in Visual Studio). 

## <a name="advanced-features"></a>Erweiterte Funktionen

### <a name="intent-actions-and-features"></a>Beabsichtigte Aktionen und Features

Das Android-Manifest bietet Ihnen die Möglichkeit, die Funktionen Ihrer Aktivität zu beschreiben. Dies erfolgt über [Intents](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) und die [`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
benutzerdefiniertes Attribut. Sie können angeben, welche Aktionen für Ihre Aktivität geeignet sind, indem Sie die [`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
-Konstruktor und welche Kategorien für die [`Categories`](xref:Android.App.IntentFilterAttribute.Categories) geeignet sind.
-Eigenschaft veranschaulicht. Mindestens eine Aktivität muss bereitgestellt werden (aus diesem Grund werden Aktivitäten im Konstruktor bereitgestellt). `[IntentFilter]` können mehrmals bereitgestellt werden, und jede Verwendung führt zu einem separaten `<intent-filter/>` Element innerhalb der `<activity/>`. Beispiel:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

In diesem Beispiel wird das folgende XML-Fragment erzeugt:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```

### <a name="application-element"></a>Application-Element

Das Android-Manifest bietet auch eine Möglichkeit, Eigenschaften für die gesamte Anwendung zu deklarieren. Dies erfolgt über das `<application>`-Element und sein Pendant, das benutzerdefinierte [Anwendungs](xref:Android.App.ApplicationAttribute) Attribut. Beachten Sie, dass es sich hierbei um Anwendungs weite (assemblyweite) Einstellungen anstelle von Aktivitäts Einstellungen handelt. In der Regel deklarieren Sie `<application>` Eigenschaften für die gesamte Anwendung und überschreiben diese Einstellungen dann (bei Bedarf) auf Aktivitäts Basis. 

Das folgende `Application`-Attribut wird z. b. zu **AssemblyInfo.cs** hinzugefügt, um anzugeben, dass die Anwendung deentschlbelt werden kann, dass der für den Benutzer lesbare Name **Meine app**ist und dass der `Theme.Light` Stil als Standarddesign für alle Aktivitäten verwendet wird: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Diese Deklaration bewirkt, dass das folgende XML-Fragment in **obj/Debug/Android/androidmanifest. XML**generiert wird:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

In diesem Beispiel werden alle Aktivitäten in der App standardmäßig auf den `Theme.Light` Stil eingestellt. Wenn Sie das Design einer Aktivität auf `Theme.Dialog`festgelegt haben, verwendet nur diese Aktivität den `Theme.Dialog` Stil, während alle anderen Aktivitäten in der App standardmäßig den `Theme.Light` Stil verwenden, der im `<application>` Element festgelegt ist. 

Das `Application`-Element ist nicht die einzige Möglichkeit, `<application>` Attribute zu konfigurieren. Alternativ können Sie Attribute direkt in das `<application>`-Element von " **Properties/androidmanifest. XML**" einfügen. Diese Einstellungen werden mit dem abschließenden `<application>`-Element zusammengeführt, das sich in **obj/Debug/Android/androidmanifest. XML**befindet. Beachten Sie, dass der Inhalt von **Properties/androidmanifest. XML** immer Daten überschreibt, die von benutzerdefinierten Attributen bereitgestellt werden. 

Es gibt viele Anwendungs weite Attribute, die Sie im `<application>`-Element konfigurieren können. Weitere Informationen zu diesen Einstellungen finden Sie im Abschnitt [Public Properties](xref:Android.App.ApplicationAttribute) von [applicationattribute](xref:Android.App.ApplicationAttribute). 

## <a name="list-of-custom-attributes"></a>Liste der benutzerdefinierten Attribute

- [Android. app. activityattribute](xref:Android.App.ActivityAttribute) : generiert ein [/Manifest/Application/Activity](https://developer.android.com/guide/topics/manifest/activity-element.html) XML-Fragment 
- [Android. app. applicationattribute](xref:Android.App.ApplicationAttribute) : generiert ein [/Manifest/Application](https://developer.android.com/guide/topics/manifest/application-element.html) XML-Fragment 
- [Android. app. instrumentationattribute](xref:Android.App.InstrumentationAttribute) : generiert ein [/Manifest/Instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) XML-Fragment 
- [Android. app. intentfilterattribute](xref:Android.App.IntentFilterAttribute) : generiert ein [//Intent-Filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) XML-Fragment 
- [Android. app. MetaDataAttribute](xref:Android.App.MetaDataAttribute) : generiert ein [//Meta-Data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) XML-Fragment 
- [Android. app. permissionattribute](xref:Android.App.PermissionAttribute) : generiert ein [//Permission](https://developer.android.com/guide/topics/manifest/permission-element.html) XML-Fragment 
- [Android. app. permissiongroupattribute](xref:Android.App.PermissionGroupAttribute) : generiert ein [//Permission-Group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) XML-Fragment 
- [Android. app. permissiontreeattribute](xref:Android.App.PermissionTreeAttribute) : generiert ein [//Permission-Tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) XML-Fragment 
- [Android. app. Service Attribute](xref:Android.App.ServiceAttribute) : generiert ein [/Manifest/Application/Service](https://developer.android.com/guide/topics/manifest/service-element.html) XML-Fragment 
- [Android. app. webelibraryattribute](xref:Android.App.UsesLibraryAttribute) : generiert ein [/Manifest/Application/uses-Library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) XML-Fragment 
- [Android. app. webanmissionattribute](xref:Android.App.UsesPermissionAttribute) : generiert ein [/Manifest/uses-Permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) XML-Fragment 
- [Android. Content. broadcastreceiverattribute](xref:Android.Content.BroadcastReceiverAttribute) : generiert ein [/Manifest/Application/Receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) XML-Fragment 
- [Android. Content. contentproviderattribute](xref:Android.Content.ContentProviderAttribute) : generiert ein [/Manifest/Application/Provider](https://developer.android.com/guide/topics/manifest/provider-element.html) XML-Fragment 
- [Android. Content. granturipermissionattribute](xref:Android.Content.GrantUriPermissionAttribute) : generiert ein [/Manifest/Application/Provider/Grant-URI-Permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML-Fragment
