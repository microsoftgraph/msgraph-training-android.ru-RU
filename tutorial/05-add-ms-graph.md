<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="dca4e-101">В этом упражнении вы будете включать Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="dca4e-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="dca4e-102">Для этого приложения для звонков в Microsoft Graph используется [SDK Microsoft Graph для Java.](https://github.com/microsoftgraph/msgraph-sdk-java)</span><span class="sxs-lookup"><span data-stu-id="dca4e-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="dca4e-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="dca4e-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="dca4e-104">В этом разделе вы расширит класс, чтобы добавить функцию для получения событий пользователя на текущей неделе и обновить для `GraphHelper` `CalendarFragment` использования этих новых функций.</span><span class="sxs-lookup"><span data-stu-id="dca4e-104">In this section you will extend the `GraphHelper` class to add a function to get the user's events for the current week and update `CalendarFragment` to use these new functions.</span></span>

1. <span data-ttu-id="dca4e-105">Откройте **GraphHelper** и добавьте следующие `import` утверждения в верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="dca4e-105">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.HeaderOption;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.EventCollectionPage;
    import com.microsoft.graph.requests.EventCollectionRequestBuilder;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.util.LinkedList;
    import java.util.List;
    import java.util.concurrent.CompletableFuture;
    ```

1. <span data-ttu-id="dca4e-106">Добавьте в класс следующие `GraphHelper` функции.</span><span class="sxs-lookup"><span data-stu-id="dca4e-106">Add the following functions to the `GraphHelper` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > <span data-ttu-id="dca4e-107">Рассмотрим, что делает `getCalendarView` код.</span><span class="sxs-lookup"><span data-stu-id="dca4e-107">Consider what the code in `getCalendarView` is doing.</span></span>
    >
    > - <span data-ttu-id="dca4e-108">Вызывается URL-адрес `/v1.0/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="dca4e-108">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
    >   - <span data-ttu-id="dca4e-109">Параметры `startDateTime` `endDateTime` и параметры запроса определяют начало и конец представления календаря.</span><span class="sxs-lookup"><span data-stu-id="dca4e-109">The `startDateTime` and `endDateTime` query parameters define the start and end of the calendar view.</span></span>
    >   - <span data-ttu-id="dca4e-110">в загонах microsoft Graph возвращается время начала и окончания каждого события в `Prefer: outlook.timezone` часовом поясе пользователя.</span><span class="sxs-lookup"><span data-stu-id="dca4e-110">the `Prefer: outlook.timezone` header causes the Microsoft Graph to return the start and end times of each event in the user's time zone.</span></span>
    >   - <span data-ttu-id="dca4e-111">Функция `select` ограничивает выходные данные, чтобы для каждого события возвращались только те поля, которые действительно используются в представлении.</span><span class="sxs-lookup"><span data-stu-id="dca4e-111">The `select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="dca4e-112">Функция `orderby` сортировать результаты по времени начала.</span><span class="sxs-lookup"><span data-stu-id="dca4e-112">The `orderby` function sorts the results by start time.</span></span>
    >   - <span data-ttu-id="dca4e-113">Функция `top` запрашивает 25 результатов на страницу.</span><span class="sxs-lookup"><span data-stu-id="dca4e-113">The `top` function requests 25 results per page.</span></span>
    > - <span data-ttu-id="dca4e-114">Функция проверяет наличие дополнительных результатов и запрашивает `processPage` дополнительные страницы при необходимости.</span><span class="sxs-lookup"><span data-stu-id="dca4e-114">The `processPage` function checks if there are more results available and requests additional pages if needed.</span></span>

1. <span data-ttu-id="dca4e-115">Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**.</span><span class="sxs-lookup"><span data-stu-id="dca4e-115">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="dca4e-116">Назови класс `GraphToIana` и выберите **ОК.**</span><span class="sxs-lookup"><span data-stu-id="dca4e-116">Name the class `GraphToIana` and select **OK**.</span></span>

1. <span data-ttu-id="dca4e-117">Откройте новый файл и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="dca4e-117">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. <span data-ttu-id="dca4e-118">Добавьте следующие `import` утверждения в верхнюю часть файла **CalendarFragment.**</span><span class="sxs-lookup"><span data-stu-id="dca4e-118">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.Event;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalException;
    import java.time.DayOfWeek;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    import java.time.temporal.ChronoUnit;
    import java.time.temporal.TemporalAdjusters;
    import java.util.List;
    ```

1. <span data-ttu-id="dca4e-119">Добавьте в класс следующий `CalendarFragment` член.</span><span class="sxs-lookup"><span data-stu-id="dca4e-119">Add the following member to the `CalendarFragment` class.</span></span>

    ```java
    private List<Event> mEventList = null;
    ```

1. <span data-ttu-id="dca4e-120">Добавьте в класс следующие `CalendarFragment` функции, чтобы скрыть и показать планку прогресса.</span><span class="sxs-lookup"><span data-stu-id="dca4e-120">Add the following functions to the `CalendarFragment` class to hide and show the progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="dca4e-121">Добавьте следующую функцию для вывода списка событий для отладки.</span><span class="sxs-lookup"><span data-stu-id="dca4e-121">Add the following function to output the event list for debugging purposes.</span></span>

    ```java
    private void addEventsToList() {
        // Temporary for debugging
        String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
        Log.d("GRAPH", jsonEvents);
    }
    ```

1. <span data-ttu-id="dca4e-122">Замените `onCreateView` существующую функцию `CalendarFragment` в классе следующей.</span><span class="sxs-lookup"><span data-stu-id="dca4e-122">Replace the existing `onCreateView` function in the `CalendarFragment` class with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

1. <span data-ttu-id="dca4e-123">Запустите приложение, вопишите и нажмите элемент **навигации Calendar** в меню.</span><span class="sxs-lookup"><span data-stu-id="dca4e-123">Run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="dca4e-124">Вы должны увидеть свалку JSON событий в журнале отлаживайки в Android Studio.</span><span class="sxs-lookup"><span data-stu-id="dca4e-124">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="dca4e-125">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="dca4e-125">Display the results</span></span>

<span data-ttu-id="dca4e-126">Теперь вы можете заменить свалку JSON чем-то, чтобы отобразить результаты в удобной для пользователя манере.</span><span class="sxs-lookup"><span data-stu-id="dca4e-126">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="dca4e-127">В этом разделе вы добавим фрагмент календаря, создайте макет для каждого элемента в этом разделе и создайте настраиваемый адаптер списка для того, чтобы поля каждого из них были соответствующими в `ListView` `ListView` `ListView` `Event` `TextView` представлении.</span><span class="sxs-lookup"><span data-stu-id="dca4e-127">In this section, you will add a `ListView` to the calendar fragment, create a layout for each item in the `ListView`, and create a custom list adapter for the `ListView` that maps the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

1. <span data-ttu-id="dca4e-128">Замените `TextView` в **приложении/res/layout/fragment_calendar.xml** на `ListView` .</span><span class="sxs-lookup"><span data-stu-id="dca4e-128">Replace the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. <span data-ttu-id="dca4e-129">Щелкните правой кнопкой **мыши папку app/res/layout** и выберите **файл ресурса "Новый"** и **"Макет".**</span><span class="sxs-lookup"><span data-stu-id="dca4e-129">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="dca4e-130">Назови `event_list_item` файл, измените **элемент Root на** и выберите `RelativeLayout` **ОК.**</span><span class="sxs-lookup"><span data-stu-id="dca4e-130">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and select **OK**.</span></span>

1. <span data-ttu-id="dca4e-131">Откройте файл **event_list_item.xml** и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="dca4e-131">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. <span data-ttu-id="dca4e-132">Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**.</span><span class="sxs-lookup"><span data-stu-id="dca4e-132">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="dca4e-133">Назови класс `EventListAdapter` и выберите **ОК.**</span><span class="sxs-lookup"><span data-stu-id="dca4e-133">Name the class `EventListAdapter` and select **OK**.</span></span>

1. <span data-ttu-id="dca4e-134">Откройте файл **EventListAdapter** и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="dca4e-134">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. <span data-ttu-id="dca4e-135">Откройте класс **CalendarFragment** и замените существующую `addEventsToList` функцию на следующую.</span><span class="sxs-lookup"><span data-stu-id="dca4e-135">Open the **CalendarFragment** class and replace the existing `addEventsToList` function with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. <span data-ttu-id="dca4e-136">Запустите приложение, вопишите и нажмите элемент **навигации Календарь.**</span><span class="sxs-lookup"><span data-stu-id="dca4e-136">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="dca4e-137">Список событий.</span><span class="sxs-lookup"><span data-stu-id="dca4e-137">You should see the list of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/calendar-list.png)
