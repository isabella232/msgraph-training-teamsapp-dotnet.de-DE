<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="71f6c-101">Microsoft Teams-Registerkarten-Anwendungen haben mehrere Optionen, um den Benutzer zu authentifizieren und Microsoft Graph aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="71f6c-101">Microsoft Teams tab applications have multiple options to authenticate the user and call Microsoft Graph.</span></span> <span data-ttu-id="71f6c-102">In dieser Übung implementieren Sie eine Registerkarte mit [einmaligem Anmelden](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso) , um ein Authentifizierungstoken auf dem Client abzurufen, und verwenden dann den [im-Auftrag-von-Fluss](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) auf dem Server, um diesen Token auszutauschen, um Zugriff auf Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="71f6c-102">In this exercise, you'll implement a tab that does [single sign-on](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso) to get an auth token on the client, then uses the [on-behalf-of flow](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) on the server to exchange that token to get access to Microsoft Graph.</span></span>

<span data-ttu-id="71f6c-103">Weitere Alternativen finden Sie in den folgenden Informationen.</span><span class="sxs-lookup"><span data-stu-id="71f6c-103">For other alternatives, see the following.</span></span>

- <span data-ttu-id="71f6c-104">[Erstellen Sie eine Microsoft Teams-Registerkarte mit dem Microsoft Graph-Toolkit](/graph/toolkit/get-started/build-a-microsoft-teams-tab).</span><span class="sxs-lookup"><span data-stu-id="71f6c-104">[Build a Microsoft Teams tab with the Microsoft Graph Toolkit](/graph/toolkit/get-started/build-a-microsoft-teams-tab).</span></span> <span data-ttu-id="71f6c-105">Dieses Beispiel ist vollständig clientseitig und verwendet das Microsoft Graph-Toolkit, um die Authentifizierung zu verarbeiten und Anrufe an Microsoft Graph vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="71f6c-105">This sample is completely client-side, and uses the Microsoft Graph Toolkit to handle authentication and making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="71f6c-106">[Microsoft Teams-Authentifizierungs Beispiel](https://github.com/OfficeDev/microsoft-teams-sample-auth-node).</span><span class="sxs-lookup"><span data-stu-id="71f6c-106">[Microsoft Teams Authentication Sample](https://github.com/OfficeDev/microsoft-teams-sample-auth-node).</span></span> <span data-ttu-id="71f6c-107">Dieses Beispiel enthält mehrere Beispiele für verschiedene Authentifizierungsszenarien.</span><span class="sxs-lookup"><span data-stu-id="71f6c-107">This sample contains multiple examples covering different authentication scenarios.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="71f6c-108">Erstellen des Projekts</span><span class="sxs-lookup"><span data-stu-id="71f6c-108">Create the project</span></span>

<span data-ttu-id="71f6c-109">Erstellen Sie zunächst eine ASP.net Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="71f6c-109">Start by creating an ASP.NET Core web app.</span></span>

1. <span data-ttu-id="71f6c-110">Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="71f6c-110">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="71f6c-111">Führen Sie den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="71f6c-111">Run the following command.</span></span>

    ```Shell
    dotnet new webapp -o GraphTutorial
    ```

1. <span data-ttu-id="71f6c-112">Nachdem das Projekt erstellt wurde, stellen Sie sicher, dass es funktioniert, indem Sie das aktuelle Verzeichnis in das **GraphTutorial** -Verzeichnis ändern und den folgenden Befehl in ihrer CLI ausführen.</span><span class="sxs-lookup"><span data-stu-id="71f6c-112">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

1. <span data-ttu-id="71f6c-113">Öffnen Sie den Browser, und navigieren Sie zu `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="71f6c-113">Open your browser and browse to `https://localhost:5001`.</span></span> <span data-ttu-id="71f6c-114">Wenn alles funktioniert, sollte eine standardmäßige ASP.net-Kern Seite angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="71f6c-114">If everything is working, you should see a default ASP.NET Core page.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71f6c-115">Wenn Sie eine Warnung erhalten, dass das Zertifikat für **localhost** nicht vertrauenswürdig ist, können Sie die .net-Kern-CLI verwenden, um das Entwicklungszertifikat zu installieren und ihm zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="71f6c-115">If you receive a warning that the certificate for **localhost** is un-trusted you can use the .NET Core CLI to install and trust the development certificate.</span></span> <span data-ttu-id="71f6c-116">Anweisungen zu bestimmten Betriebssystemen finden Sie unter [Erzwingen von HTTPS in ASP.net Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) .</span><span class="sxs-lookup"><span data-stu-id="71f6c-116">See [Enforce HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) for instructions for specific operating systems.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="71f6c-117">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="71f6c-117">Add NuGet packages</span></span>

<span data-ttu-id="71f6c-118">Bevor Sie fortfahren, installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="71f6c-118">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="71f6c-119">[Microsoft. Identity. Internet](https://www.nuget.org/packages/Microsoft.Identity.Web/) zum Authentifizieren und Anfordern von Zugriffstoken.</span><span class="sxs-lookup"><span data-stu-id="71f6c-119">[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) for authenticating and requesting access tokens.</span></span>
- <span data-ttu-id="71f6c-120">[Microsoft. Identity. Internet. Microsoft Graph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) zum Hinzufügen von Microsoft Graph-Unterstützung, die mit Microsoft. Identity. Internet konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="71f6c-120">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) for adding Microsoft Graph support configured with Microsoft.Identity.Web.</span></span>
- <span data-ttu-id="71f6c-121">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) zum Aktualisieren der Version dieses Pakets, das von Microsoft. Identity. Internet. Microsoft Graph installiert wurde.</span><span class="sxs-lookup"><span data-stu-id="71f6c-121">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) to update the version of this package installed by Microsoft.Identity.Web.MicrosoftGraph.</span></span>
- <span data-ttu-id="71f6c-122">[Microsoft. AspNetCore. MVC. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) zum Aktivieren von Newton Soft JSON-Konvertern zur Kompatibilität mit dem Microsoft Graph-SDK.</span><span class="sxs-lookup"><span data-stu-id="71f6c-122">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) to enable Newtonsoft JSON converters for compatibility with the Microsoft Graph SDK.</span></span>
- <span data-ttu-id="71f6c-123">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) für die Übersetzung von Windows-Zeitzonenbezeichnern in IANA-IDs.</span><span class="sxs-lookup"><span data-stu-id="71f6c-123">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for translating Windows time zone identifiers to IANA identifiers.</span></span>

1. <span data-ttu-id="71f6c-124">Führen Sie die folgenden Befehle in der CLI aus, um die Abhängigkeiten zu installieren.</span><span class="sxs-lookup"><span data-stu-id="71f6c-124">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.Web.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.16.0
    dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a><span data-ttu-id="71f6c-125">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="71f6c-125">Design the app</span></span>

<span data-ttu-id="71f6c-126">In diesem Abschnitt wird die grundlegende UI-Struktur der Anwendung erstellt.</span><span class="sxs-lookup"><span data-stu-id="71f6c-126">In this section you will create the basic UI structure of the application.</span></span>

> [!TIP]
> <span data-ttu-id="71f6c-127">Sie können einen beliebigen Text-Editor verwenden, um die Quelldateien für dieses Lernprogramm zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="71f6c-127">You can use any text editor to edit the source files for this tutorial.</span></span> <span data-ttu-id="71f6c-128">[Visual Studio Code](https://code.visualstudio.com/) stellt jedoch zusätzliche Features bereit, beispielsweise Debuggen und IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="71f6c-128">However, [Visual Studio Code](https://code.visualstudio.com/) provides additional features, such as debugging and Intellisense.</span></span>

1. <span data-ttu-id="71f6c-129">Öffnen Sie **./Pages/Shared/_Layout. cshtml** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code, um das globale Layout der APP zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="71f6c-129">Open **./Pages/Shared/_Layout.cshtml** and replace its entire contents with the following code to update the global layout of the app.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Pages/Shared/_Layout.cshtml" id="LayoutSnippet":::

    <span data-ttu-id="71f6c-130">Dadurch wird Bootstrap durch [Fluent UI](https://developer.microsoft.com/fluentui)ersetzt, das [Microsoft Teams SDK](/javascript/api/overview/msteams-client)hinzugefügt und das Layout vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="71f6c-130">This replaces Bootstrap with [Fluent UI](https://developer.microsoft.com/fluentui), adds the [Microsoft Teams SDK](/javascript/api/overview/msteams-client), and simplifies the layout.</span></span>

1. <span data-ttu-id="71f6c-131">Öffnen Sie **./wwwroot/js/site.js** , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="71f6c-131">Open **./wwwroot/js/site.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/wwwroot/js/site.js" id="ThemeSupportSnippet":::

    <span data-ttu-id="71f6c-132">Dadurch wird ein einfacher Design änderungshandler hinzugefügt, um die Standardtextfarbe für dunkle und kontrastreiche Designs zu ändern.</span><span class="sxs-lookup"><span data-stu-id="71f6c-132">This adds a simple theme change handler to change the default text color for dark and high contrast themes.</span></span>

1. <span data-ttu-id="71f6c-133">Öffnen Sie **./wwwroot/CSS/Site.CSS** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="71f6c-133">Open **./wwwroot/css/site.css** and replace its contents with the following.</span></span>

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. <span data-ttu-id="71f6c-134">Öffnen Sie **./pages/index.cshtml** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="71f6c-134">Open **./Pages/Index.cshtml** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="71f6c-135">Öffnen Sie **./Startup.cs** , und entfernen Sie die `app.UseHttpsRedirection();` `Configure` -Methode.</span><span class="sxs-lookup"><span data-stu-id="71f6c-135">Open **./Startup.cs** and remove the `app.UseHttpsRedirection();` line in the `Configure` method.</span></span> <span data-ttu-id="71f6c-136">Dies ist erforderlich, damit das ngrok-Tunneling funktioniert.</span><span class="sxs-lookup"><span data-stu-id="71f6c-136">This is necessary for ngrok tunneling to work.</span></span>

## <a name="run-ngrok"></a><span data-ttu-id="71f6c-137">Ausführen von ngrok</span><span class="sxs-lookup"><span data-stu-id="71f6c-137">Run ngrok</span></span>

<span data-ttu-id="71f6c-138">Microsoft Teams unterstützt kein lokales Hosting für apps.</span><span class="sxs-lookup"><span data-stu-id="71f6c-138">Microsoft Teams does not support local hosting for apps.</span></span> <span data-ttu-id="71f6c-139">Der Server, der Ihre APP hostet, muss über die Cloud über HTTPS-Endpunkte verfügbar sein.</span><span class="sxs-lookup"><span data-stu-id="71f6c-139">The server hosting your app must be available from the cloud using HTTPS endpoints.</span></span> <span data-ttu-id="71f6c-140">Für das lokale Debuggen können Sie ngrok verwenden, um eine öffentliche URL für das Lokal gehostete Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="71f6c-140">For debugging locally, you can use ngrok to create a public URL for your locally-hosted project.</span></span>

1. <span data-ttu-id="71f6c-141">Öffnen Sie die CLI, und führen Sie den folgenden Befehl aus, um ngrok zu starten.</span><span class="sxs-lookup"><span data-stu-id="71f6c-141">Open your CLI and run the following command to start ngrok.</span></span>

    ```Shell
    ngrok http 5000
    ```

1. <span data-ttu-id="71f6c-142">Nachdem ngrok gestartet wurde, kopieren Sie die HTTPS-Weiterleitungs-URL.</span><span class="sxs-lookup"><span data-stu-id="71f6c-142">Once ngrok starts, copy the HTTPS Forwarding URL.</span></span> <span data-ttu-id="71f6c-143">Es sollte wie folgt aussehen `https://50153897dd4d.ngrok.io` .</span><span class="sxs-lookup"><span data-stu-id="71f6c-143">It should look like `https://50153897dd4d.ngrok.io`.</span></span> <span data-ttu-id="71f6c-144">Sie benötigen diesen Wert in späteren Schritten.</span><span class="sxs-lookup"><span data-stu-id="71f6c-144">You'll need this value in later steps.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71f6c-145">Wenn Sie die ﻿kostenlose Version von ngrok verwenden, ändert sich die Weiterleitungs-URL jedes Mal, wenn Sie ngrok neu starten.</span><span class="sxs-lookup"><span data-stu-id="71f6c-145">If you are using the free version of ngrok, the forwarding URL changes every time you restart ngrok.</span></span> <span data-ttu-id="71f6c-146">Es wird empfohlen, dass Sie ngrok ausführen lassen, bis Sie dieses Lernprogramm durchführen, um die gleiche URL beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="71f6c-146">It's recommended that you leave ngrok running until you complete this tutorial to keep the same URL.</span></span> <span data-ttu-id="71f6c-147">Wenn Sie ngrok neu starten müssen, müssen Sie Ihre URL überall dort aktualisieren, wo Sie verwendet wird, und die app in Microsoft Teams erneut installieren.</span><span class="sxs-lookup"><span data-stu-id="71f6c-147">If you have to restart ngrok, you'll need to update your URL everywhere that it is used and reinstall the app in Microsoft Teams.</span></span>
