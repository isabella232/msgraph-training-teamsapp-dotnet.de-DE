<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f4636-101">In diesem Abschnitt können Sie die Möglichkeit zum Erstellen von Ereignissen im Kalender des Benutzers hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="f4636-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-the-new-event-tab"></a><span data-ttu-id="f4636-102">Erstellen der neuen Ereignisregisterkarte</span><span class="sxs-lookup"><span data-stu-id="f4636-102">Create the new event tab</span></span>

1. <span data-ttu-id="f4636-103">Erstellen Sie eine neue Datei im **./Pages** -Verzeichnis mit dem Namen "New **Event. cshtml** ", und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="f4636-103">Create a new file in the **./Pages** directory named **NewEvent.cshtml** and add the following code.</span></span>

    :::code language="razor" source="../demo/GraphTutorial/Pages/NewEvent.cshtml":::

    <span data-ttu-id="f4636-104">Dadurch wird ein einfaches Formular implementiert und JavaScript hinzugefügt, um die Formulardaten an die WebAPI zu senden.</span><span class="sxs-lookup"><span data-stu-id="f4636-104">This implements a simple form, and adds JavaScript to post the form data to the Web API.</span></span>

## <a name="implement-the-web-api"></a><span data-ttu-id="f4636-105">Implementieren der Webdienst-API</span><span class="sxs-lookup"><span data-stu-id="f4636-105">Implement the Web API</span></span>

1. <span data-ttu-id="f4636-106">Erstellen Sie ein neues Verzeichnis mit dem Namen " **Models** " im Stamm des Projekts.</span><span class="sxs-lookup"><span data-stu-id="f4636-106">Create a new directory named **Models** in the root of the project.</span></span>

1. <span data-ttu-id="f4636-107">Erstellen Sie eine neue Datei im **./Models** -Verzeichnis mit dem Namen **NewEvent.cs** , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="f4636-107">Create a new file in the **./Models** directory named **NewEvent.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

1. <span data-ttu-id="f4636-108">Öffnen Sie **/Controllers/CalendarController.cs** , und fügen Sie die folgende `using` Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="f4636-108">Open **./Controllers/CalendarController.cs** and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    ```

1. <span data-ttu-id="f4636-109">Fügen Sie der **CalendarController** -Klasse die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="f4636-109">Add the following function to the **CalendarController** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="PostSnippet":::

    <span data-ttu-id="f4636-110">Dadurch kann ein HTTP-Beitrag an die WebAPI mit den Feldern des Formulars gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f4636-110">This allows an HTTP POST to the Web API with the fields of the form.</span></span>

1. <span data-ttu-id="f4636-111">Speichern Sie alle Änderungen, und starten Sie die Anwendung neu.</span><span class="sxs-lookup"><span data-stu-id="f4636-111">Save all of your changes and restart the application.</span></span> <span data-ttu-id="f4636-112">Aktualisieren Sie die app in Microsoft Teams, und wählen Sie die Registerkarte **Ereignis erstellen** aus. Füllen Sie das Formular aus, und wählen Sie **Erstellen** aus, um dem Kalender des Benutzers ein Ereignis hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f4636-112">Refresh the app in Microsoft Teams, and select the **Create event** tab. Fill out the form and select **Create** to add an event to the user's calendar.</span></span>

    ![Screenshot der Registerkarte "Ereignis erstellen"](images/create-event.png)
