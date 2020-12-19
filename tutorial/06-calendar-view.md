<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="959e3-101">In diesem Abschnitt werden Sie Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="959e3-101">In this section you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="959e3-102">Für diese Anwendung verwenden Sie die [Microsoft Graph-Clientbibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="959e3-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

# <a name="get-a-calendar-view"></a><span data-ttu-id="959e3-103">Abrufen einer Kalenderansicht</span><span class="sxs-lookup"><span data-stu-id="959e3-103">Get a calendar view</span></span>

<span data-ttu-id="959e3-104">Eine Kalenderansicht ist eine Gruppe von Ereignissen aus dem Kalender des Benutzers, die zwischen zwei Zeitpunkten auftreten.</span><span class="sxs-lookup"><span data-stu-id="959e3-104">A calendar view is a set of events from the user's calendar that occur between two points of time.</span></span> <span data-ttu-id="959e3-105">Verwenden Sie diese, um die Ereignisse des Benutzers für die aktuelle Woche abzurufen.</span><span class="sxs-lookup"><span data-stu-id="959e3-105">You'll use this to get the user's events for the current week.</span></span>

1. <span data-ttu-id="959e3-106">Öffnen Sie **/Controllers/CalendarController.cs** , und fügen Sie die folgende Funktion zur **CalendarController** -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="959e3-106">Open **./Controllers/CalendarController.cs** and add the following function to the **CalendarController** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetStartOfWeekSnippet":::

1. <span data-ttu-id="959e3-107">Fügen Sie die folgende Funktion hinzu, um von Microsoft Graph-anrufen zurückgegebene Ausnahmen zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="959e3-107">Add the following function to handle exceptions returned from Microsoft Graph calls.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="HandleGraphExceptionSnippet":::

1. <span data-ttu-id="959e3-108">Ersetzen Sie die vorhandene `Get`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="959e3-108">Replace the existing `Get` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetSnippet" highlight="2,14-57":::

    <span data-ttu-id="959e3-109">Überprüfen Sie die Änderungen.</span><span class="sxs-lookup"><span data-stu-id="959e3-109">Review the changes.</span></span> <span data-ttu-id="959e3-110">Diese neue Version der Funktion:</span><span class="sxs-lookup"><span data-stu-id="959e3-110">This new version of the function:</span></span>

    - <span data-ttu-id="959e3-111">Gibt `IEnumerable<Event>` anstelle von zurück `string` .</span><span class="sxs-lookup"><span data-stu-id="959e3-111">Returns `IEnumerable<Event>` instead of `string`.</span></span>
    - <span data-ttu-id="959e3-112">Ruft die Postfacheinstellungen des Benutzers mithilfe von Microsoft Graph ab.</span><span class="sxs-lookup"><span data-stu-id="959e3-112">Gets the user's mailbox settings using Microsoft Graph.</span></span>
    - <span data-ttu-id="959e3-113">Verwendet die Zeitzone des Benutzers, um den Anfang und das Ende der aktuellen Woche zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="959e3-113">Uses the user's time zone to calculate the start and end of the current week.</span></span>
    - <span data-ttu-id="959e3-114">Ruft eine Kalenderansicht ab.</span><span class="sxs-lookup"><span data-stu-id="959e3-114">Gets a calendar view</span></span>
        - <span data-ttu-id="959e3-115">Verwendet die `.Header()` -Funktion, um eine Kopfzeile einzuschließen `Prefer: outlook.timezone` , wodurch die Anfangs-und Endzeiten der zurückgegebenen Ereignisse in die Zeitzone des Benutzers konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="959e3-115">Uses the `.Header()` function to include a `Prefer: outlook.timezone` header, which causes the returned events to have their start and end times converted to the user's timezone.</span></span>
        - <span data-ttu-id="959e3-116">Verwendet die `.Top()` -Funktion, um höchstens 50 Ereignisse anzufordern.</span><span class="sxs-lookup"><span data-stu-id="959e3-116">Uses the `.Top()` function to request at most 50 events.</span></span>
        - <span data-ttu-id="959e3-117">Verwendet die `.Select()` -Funktion, um nur die von der APP verwendeten Felder anzufordern.</span><span class="sxs-lookup"><span data-stu-id="959e3-117">Uses the `.Select()` function to request just the fields used by the app.</span></span>
        - <span data-ttu-id="959e3-118">Verwendet die `OrderBy()` -Funktion, um die Ergebnisse nach dem Startzeitpunkt zu sortieren.</span><span class="sxs-lookup"><span data-stu-id="959e3-118">Uses the `OrderBy()` function to sort the results by the start time.</span></span>

1. <span data-ttu-id="959e3-119">Speichern Sie die Änderungen, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="959e3-119">Save your changes and restart the app.</span></span> <span data-ttu-id="959e3-120">Aktualisieren Sie die Registerkarte in Microsoft Teams.</span><span class="sxs-lookup"><span data-stu-id="959e3-120">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="959e3-121">Die APP zeigt eine JSON-Auflistung der Ereignisse an.</span><span class="sxs-lookup"><span data-stu-id="959e3-121">The app displays a JSON listing of the events.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="959e3-122">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="959e3-122">Display the results</span></span>

<span data-ttu-id="959e3-123">Jetzt können Sie die Liste der Ereignisse auf eine benutzerfreundlichere Weise anzeigen.</span><span class="sxs-lookup"><span data-stu-id="959e3-123">Now you can display the list of events in a more user friendly way.</span></span>

1. <span data-ttu-id="959e3-124">Öffnen Sie **/pages/index.cshtml** , und fügen Sie die folgenden Funktionen innerhalb des- `<script>` Tags hinzu.</span><span class="sxs-lookup"><span data-stu-id="959e3-124">Open **./Pages/Index.cshtml** and add the following functions inside the `<script>` tag.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderHelpersSnippet":::

1. <span data-ttu-id="959e3-125">Ersetzen Sie die vorhandene `renderCalendar`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="959e3-125">Replace the existing `renderCalendar` function with the following.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderCalendarSnippet":::

1. <span data-ttu-id="959e3-126">Speichern Sie die Änderungen, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="959e3-126">Save your changes and restart the app.</span></span> <span data-ttu-id="959e3-127">Aktualisieren Sie die Registerkarte in Microsoft Teams.</span><span class="sxs-lookup"><span data-stu-id="959e3-127">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="959e3-128">Die APP zeigt Ereignisse im Kalender des Benutzers an.</span><span class="sxs-lookup"><span data-stu-id="959e3-128">The app displays events on the user's calendar.</span></span>

    ![Ein Screenshot der APP, die den Kalender des Benutzers anzeigt](images/calendar-view.png)
