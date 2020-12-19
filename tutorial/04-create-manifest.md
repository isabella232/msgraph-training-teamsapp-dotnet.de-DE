<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e13b5-101">Das App-Manifest beschreibt, wie die app in Microsoft Teams integriert wird und für die Installation von apps erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e13b5-101">The app manifest describes how the app integrates with Microsoft Teams and is required to install apps.</span></span> <span data-ttu-id="e13b5-102">In diesem Abschnitt verwenden Sie App Studio im Microsoft Teams-Client, um ein Manifest zu generieren.</span><span class="sxs-lookup"><span data-stu-id="e13b5-102">In this section you'll use App Studio in the Microsoft Teams client to generate a manifest.</span></span>

1. <span data-ttu-id="e13b5-103">Wenn Sie das App-Studio nicht bereits in Microsoft Teams installiert haben, [Installieren Sie es jetzt](/microsoftteams/platform/concepts/build-and-test/app-studio-overview).</span><span class="sxs-lookup"><span data-stu-id="e13b5-103">If you do not already have App Studio installed in Teams, [install it now](/microsoftteams/platform/concepts/build-and-test/app-studio-overview).</span></span>

1. <span data-ttu-id="e13b5-104">Starten Sie App Studio in Microsoft Teams, und wählen Sie den **Manifest-Editor** aus.</span><span class="sxs-lookup"><span data-stu-id="e13b5-104">Launch App Studio in Microsoft Teams and select the **Manifest editor**.</span></span>

1. <span data-ttu-id="e13b5-105">Wählen Sie **neue APP erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="e13b5-105">Select **Create a new app**.</span></span>

    ![Ein Screenshot des Manifest-Editors in App Studio in Microsoft Teams](images/app-studio-01.png)

1. <span data-ttu-id="e13b5-107">Füllen Sie auf der Seite mit den **App-Details** die erforderlichen Felder aus.</span><span class="sxs-lookup"><span data-stu-id="e13b5-107">On the **App details** page, fill in the required fields.</span></span>

    > [!NOTE]
    > <span data-ttu-id="e13b5-108">Sie können die Standardsymbole im Abschnitt **Branding** verwenden oder Ihre eigenen hochladen.</span><span class="sxs-lookup"><span data-stu-id="e13b5-108">You can use the default icons in the **Branding** section or upload your own.</span></span>

1. <span data-ttu-id="e13b5-109">Klicken Sie im linken Menü auf **Registerkarten** unter **Funktionen**.</span><span class="sxs-lookup"><span data-stu-id="e13b5-109">On the left-hand menu, select **Tabs** under **Capabilities**.</span></span>

1. <span data-ttu-id="e13b5-110">Wählen Sie unter **persönliche Registerkarte hinzufügen die** Option **hinzu** fügen aus.</span><span class="sxs-lookup"><span data-stu-id="e13b5-110">Select **Add** under **Add a personal tab**.</span></span>

    ![Screenshot der Registerkartenseite in App Studio](images/app-studio-02.png)

1. <span data-ttu-id="e13b5-112">Füllen Sie die Felder wie folgt aus, wobei `YOUR_NGROK_URL` es sich um die Weiterleitungs-URL handelt, die Sie im vorherigen Abschnitt kopiert haben.</span><span class="sxs-lookup"><span data-stu-id="e13b5-112">Fill in the fields as follows, where `YOUR_NGROK_URL` is the forwarding URL you copied in the previous section.</span></span> <span data-ttu-id="e13b5-113">Wählen Sie **Speichern** aus, wenn Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="e13b5-113">Select **Save** when done.</span></span>

    - <span data-ttu-id="e13b5-114">**Name:**`Create event`</span><span class="sxs-lookup"><span data-stu-id="e13b5-114">**Name:** `Create event`</span></span>
    - <span data-ttu-id="e13b5-115">**Entitäts-ID:**`createEventTab`</span><span class="sxs-lookup"><span data-stu-id="e13b5-115">**Entity ID:** `createEventTab`</span></span>
    - <span data-ttu-id="e13b5-116">**Inhalts-URL:**`YOUR_NGROK_URL/newevent`</span><span class="sxs-lookup"><span data-stu-id="e13b5-116">**Content URL:** `YOUR_NGROK_URL/newevent`</span></span>

1. <span data-ttu-id="e13b5-117">Wählen Sie unter **persönliche Registerkarte hinzufügen die** Option **hinzu** fügen aus.</span><span class="sxs-lookup"><span data-stu-id="e13b5-117">Select **Add** under **Add a personal tab**.</span></span>

1. <span data-ttu-id="e13b5-118">Füllen Sie die Felder wie folgt aus, wobei `YOUR_NGROK_URL` es sich um die Weiterleitungs-URL handelt, die Sie im vorherigen Abschnitt kopiert haben.</span><span class="sxs-lookup"><span data-stu-id="e13b5-118">Fill in the fields as follows, where `YOUR_NGROK_URL` is the forwarding URL you copied in the previous section.</span></span> <span data-ttu-id="e13b5-119">Wählen Sie **Speichern** aus, wenn Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="e13b5-119">Select **Save** when done.</span></span>

    - <span data-ttu-id="e13b5-120">**Name:**`Graph calendar`</span><span class="sxs-lookup"><span data-stu-id="e13b5-120">**Name:** `Graph calendar`</span></span>
    - <span data-ttu-id="e13b5-121">**Entitäts-ID:**`calendarTab`</span><span class="sxs-lookup"><span data-stu-id="e13b5-121">**Entity ID:** `calendarTab`</span></span>
    - <span data-ttu-id="e13b5-122">**Inhalts-URL:**`YOUR_NGROK_URL`</span><span class="sxs-lookup"><span data-stu-id="e13b5-122">**Content URL:** `YOUR_NGROK_URL`</span></span>

1. <span data-ttu-id="e13b5-123">Wählen Sie im Menü links die Option **Domains und Berechtigungen** unter **Fertig stellen** aus.</span><span class="sxs-lookup"><span data-stu-id="e13b5-123">On the left-hand menu, select **Domains and permissions** under **Finish**.</span></span>

1. <span data-ttu-id="e13b5-124">Legen Sie die **Aad-APP-ID** von Ihrer APP-Registrierung auf die Anwendungs-ID fest.</span><span class="sxs-lookup"><span data-stu-id="e13b5-124">Set the **AAD App ID** to the application ID from your app registration.</span></span>

1. <span data-ttu-id="e13b5-125">Legen Sie das Feld für **einmaliges Anmelden** aus Ihrer APP-Registrierung auf den Anwendungs-ID-URI fest.</span><span class="sxs-lookup"><span data-stu-id="e13b5-125">Set the **Single-Sign-On** field to the application ID URI from your app registration.</span></span>

1. <span data-ttu-id="e13b5-126">Klicken Sie im linken Menü auf **Testen und verteilen** unter **Fertig stellen**.</span><span class="sxs-lookup"><span data-stu-id="e13b5-126">On the left-hand menu, select **Test and distribute** under **Finish**.</span></span> <span data-ttu-id="e13b5-127">Klicken Sie auf **herunterladen**.</span><span class="sxs-lookup"><span data-stu-id="e13b5-127">Select **Download**.</span></span>

1. <span data-ttu-id="e13b5-128">Erstellen Sie ein neues Verzeichnis im Stamm des Projekts mit dem Namen **Manifest**.</span><span class="sxs-lookup"><span data-stu-id="e13b5-128">Create a new directory in the root of the project named **Manifest**.</span></span> <span data-ttu-id="e13b5-129">Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei in dieses Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="e13b5-129">Extract the contents of the downloaded ZIP file to this directory.</span></span>
