<!-- markdownlint-disable MD002 MD041 -->

Microsoft Teams-Registerkarten-Anwendungen haben mehrere Optionen, um den Benutzer zu authentifizieren und Microsoft Graph aufzurufen. In dieser Übung implementieren Sie eine Registerkarte mit [einmaligem Anmelden](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso) , um ein Authentifizierungstoken auf dem Client abzurufen, und verwenden dann den [im-Auftrag-von-Fluss](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) auf dem Server, um diesen Token auszutauschen, um Zugriff auf Microsoft Graph zu erhalten.

Weitere Alternativen finden Sie in den folgenden Informationen.

- [Erstellen Sie eine Microsoft Teams-Registerkarte mit dem Microsoft Graph-Toolkit](/graph/toolkit/get-started/build-a-microsoft-teams-tab). Dieses Beispiel ist vollständig clientseitig und verwendet das Microsoft Graph-Toolkit, um die Authentifizierung zu verarbeiten und Anrufe an Microsoft Graph vorzunehmen.
- [Microsoft Teams-Authentifizierungs Beispiel](https://github.com/OfficeDev/microsoft-teams-sample-auth-node). Dieses Beispiel enthält mehrere Beispiele für verschiedene Authentifizierungsszenarien.

## <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie zunächst eine ASP.net Core-Webanwendung.

1. Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten. Führen Sie den folgenden Befehl aus.

    ```Shell
    dotnet new webapp -o GraphTutorial
    ```

1. Nachdem das Projekt erstellt wurde, stellen Sie sicher, dass es funktioniert, indem Sie das aktuelle Verzeichnis in das **GraphTutorial** -Verzeichnis ändern und den folgenden Befehl in ihrer CLI ausführen.

    ```Shell
    dotnet run
    ```

1. Öffnen Sie den Browser, und navigieren Sie zu `https://localhost:5001` . Wenn alles funktioniert, sollte eine standardmäßige ASP.net-Kern Seite angezeigt werden.

> [!IMPORTANT]
> Wenn Sie eine Warnung erhalten, dass das Zertifikat für **localhost** nicht vertrauenswürdig ist, können Sie die .net-Kern-CLI verwenden, um das Entwicklungszertifikat zu installieren und ihm zu vertrauen. Anweisungen zu bestimmten Betriebssystemen finden Sie unter [Erzwingen von HTTPS in ASP.net Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) .

## <a name="add-nuget-packages"></a>Hinzufügen von NuGet-Paketen

Bevor Sie fortfahren, installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.

- [Microsoft. Identity. Internet](https://www.nuget.org/packages/Microsoft.Identity.Web/) zum Authentifizieren und Anfordern von Zugriffstoken.
- [Microsoft. Identity. Internet. Microsoft Graph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) zum Hinzufügen von Microsoft Graph-Unterstützung, die mit Microsoft. Identity. Internet konfiguriert ist.
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) zum Aktualisieren der Version dieses Pakets, das von Microsoft. Identity. Internet. Microsoft Graph installiert wurde.
- [Microsoft. AspNetCore. MVC. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) zum Aktivieren von Newton Soft JSON-Konvertern zur Kompatibilität mit dem Microsoft Graph-SDK.
- [TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) für die Übersetzung von Windows-Zeitzonenbezeichnern in IANA-IDs.

1. Führen Sie die folgenden Befehle in der CLI aus, um die Abhängigkeiten zu installieren.

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.Web.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.16.0
    dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a>Entwerfen der App

In diesem Abschnitt wird die grundlegende UI-Struktur der Anwendung erstellt.

> [!TIP]
> Sie können einen beliebigen Text-Editor verwenden, um die Quelldateien für dieses Lernprogramm zu bearbeiten. [Visual Studio Code](https://code.visualstudio.com/) stellt jedoch zusätzliche Features bereit, beispielsweise Debuggen und IntelliSense.

1. Öffnen Sie **./Pages/Shared/_Layout. cshtml** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code, um das globale Layout der APP zu aktualisieren.

    :::code language="cshtml" source="../demo/GraphTutorial/Pages/Shared/_Layout.cshtml" id="LayoutSnippet":::

    Dadurch wird Bootstrap durch [Fluent UI](https://developer.microsoft.com/fluentui)ersetzt, das [Microsoft Teams SDK](/javascript/api/overview/msteams-client)hinzugefügt und das Layout vereinfacht.

1. Öffnen Sie **./wwwroot/js/site.js** , und fügen Sie den folgenden Code hinzu.

    :::code language="javascript" source="../demo/GraphTutorial/wwwroot/js/site.js" id="ThemeSupportSnippet":::

    Dadurch wird ein einfacher Design änderungshandler hinzugefügt, um die Standardtextfarbe für dunkle und kontrastreiche Designs zu ändern.

1. Öffnen Sie **./wwwroot/CSS/Site.CSS** , und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. Öffnen Sie **./pages/index.cshtml** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```cshtml
    @page
    @model IndexModel
    @{
      ViewData["Title"] = "Home page";
    }

    <div id="tab-container">
      <h1 class="ms-fontSize-24 ms-fontWeight-semibold">Loading...</h1>
    </div>

    @section Scripts
    {
      <script>
      </script>
    }
    ```

1. Öffnen Sie **./Startup.cs** , und entfernen Sie die `app.UseHttpsRedirection();` `Configure` -Methode. Dies ist erforderlich, damit das ngrok-Tunneling funktioniert.

## <a name="run-ngrok"></a>Ausführen von ngrok

Microsoft Teams unterstützt kein lokales Hosting für apps. Der Server, der Ihre APP hostet, muss über die Cloud über HTTPS-Endpunkte verfügbar sein. Für das lokale Debuggen können Sie ngrok verwenden, um eine öffentliche URL für das Lokal gehostete Projekt zu erstellen.

1. Öffnen Sie die CLI, und führen Sie den folgenden Befehl aus, um ngrok zu starten.

    ```Shell
    ngrok http 5000
    ```

1. Nachdem ngrok gestartet wurde, kopieren Sie die HTTPS-Weiterleitungs-URL. Es sollte wie folgt aussehen `https://50153897dd4d.ngrok.io` . Sie benötigen diesen Wert in späteren Schritten.

> [!IMPORTANT]
> Wenn Sie die ﻿kostenlose Version von ngrok verwenden, ändert sich die Weiterleitungs-URL jedes Mal, wenn Sie ngrok neu starten. Es wird empfohlen, dass Sie ngrok ausführen lassen, bis Sie dieses Lernprogramm durchführen, um die gleiche URL beizubehalten. Wenn Sie ngrok neu starten müssen, müssen Sie Ihre URL überall dort aktualisieren, wo Sie verwendet wird, und die app in Microsoft Teams erneut installieren.
