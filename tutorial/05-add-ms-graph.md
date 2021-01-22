<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8a2f5-101">В этом упражнении вы включаете Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="8a2f5-102">Для этого приложения вы будете использовать [microsoft Graph SDK для Java](https://github.com/microsoftgraph/msgraph-sdk-java) для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="8a2f5-103">Получить события календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="8a2f5-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="8a2f5-104">В этом разделе мы расширим класс, чтобы добавить функцию для получения событий пользователя за текущую неделю, и обновим, чтобы `GraphHelper` `CalendarFragment` использовать эти новые функции.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-104">In this section you will extend the `GraphHelper` class to add a function to get the user's events for the current week and update `CalendarFragment` to use these new functions.</span></span>

1. <span data-ttu-id="8a2f5-105">Откройте **GraphHelper** и добавьте следующие утверждения в `import` верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-105">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.HeaderOption;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import com.microsoft.graph.requests.extensions.IEventCollectionRequestBuilder;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.util.LinkedList;
    import java.util.List;
    ```

1. <span data-ttu-id="8a2f5-106">Добавьте в класс следующие `GraphHelper` функции.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-106">Add the following functions to the `GraphHelper` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > <span data-ttu-id="8a2f5-107">Подумайте, что делает `getCalendarView` код.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-107">Consider what the code in `getCalendarView` is doing.</span></span>
    >
    > - <span data-ttu-id="8a2f5-108">Будет вызван URL-адрес `/v1.0/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="8a2f5-108">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
    >   - <span data-ttu-id="8a2f5-109">Параметры `startDateTime` `endDateTime` и параметры запроса определяют начало и конец представления календаря.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-109">The `startDateTime` and `endDateTime` query parameters define the start and end of the calendar view.</span></span>
    >   - <span data-ttu-id="8a2f5-110">Заглавная запись заставляет Microsoft Graph возвращать время начала и окончания каждого события в `Prefer: outlook.timezone` часовом поясе пользователя.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-110">the `Prefer: outlook.timezone` header causes the Microsoft Graph to return the start and end times of each event in the user's time zone.</span></span>
    >   - <span data-ttu-id="8a2f5-111">Функция `select` ограничивает поля, возвращаемые для каждого события, только теми, которые будут фактически использовать представление.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-111">The `select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="8a2f5-112">Функция `orderby` сортировать результаты по времени начала.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-112">The `orderby` function sorts the results by start time.</span></span>
    >   - <span data-ttu-id="8a2f5-113">Функция `top` запрашивает 25 результатов на страницу.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-113">The `top` function requests 25 results per page.</span></span>
    > - <span data-ttu-id="8a2f5-114">Определена возможность вызова ( ) для проверки на наличии дополнительных результатов и запроса `pagingCallback` дополнительных страниц при необходимости.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-114">A callback is defined (`pagingCallback`) to check if there are more results available and to request additional pages if needed.</span></span>

1. <span data-ttu-id="8a2f5-115">Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**</span><span class="sxs-lookup"><span data-stu-id="8a2f5-115">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="8a2f5-116">Назовем класс `GraphToIana` и выберите **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="8a2f5-116">Name the class `GraphToIana` and select **OK**.</span></span>

1. <span data-ttu-id="8a2f5-117">Откройте новый файл и замените его содержимое следующим:</span><span class="sxs-lookup"><span data-stu-id="8a2f5-117">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. <span data-ttu-id="8a2f5-118">Добавьте следующие утверждения в верхнюю часть файла `import` **CalendarFragment.**</span><span class="sxs-lookup"><span data-stu-id="8a2f5-118">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.Event;
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

1. <span data-ttu-id="8a2f5-119">Добавьте в класс следующий `CalendarFragment` член.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-119">Add the following member to the `CalendarFragment` class.</span></span>

    ```java
    private List<Event> mEventList = null;
    ```

1. <span data-ttu-id="8a2f5-120">Добавьте в класс следующие `CalendarFragment` функции, чтобы скрыть и показать ход выполнения.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-120">Add the following functions to the `CalendarFragment` class to hide and show the progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="8a2f5-121">Добавьте следующую функцию для предоставления функции в качестве `getCalendarView` функции. `GraphHelper`</span><span class="sxs-lookup"><span data-stu-id="8a2f5-121">Add the following function to provide a callback for the `getCalendarView` function in `GraphHelper`.</span></span>

    ```java
    private ICallback<List<Event>> getCalendarViewCallback() {
        return new ICallback<List<Event>>() {
            @Override
            public void success(List<Event> eventList) {
                mEventList = eventList;

                // Temporary for debugging
                String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
                Log.d("GRAPH", jsonEvents);

                hideProgressBar();
            }

            @Override
            public void failure(ClientException ex) {
                hideProgressBar();
                Log.e("GRAPH", "Error getting events", ex);
                Snackbar.make(getView(),
                    ex.getMessage(),
                    BaseTransientBottomBar.LENGTH_LONG).show();
            }
        };
    }
    ```

1. <span data-ttu-id="8a2f5-122">Замените `onCreateView` существующую функцию `CalendarFragment` в классе на следующую.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-122">Replace the existing `onCreateView` function in the `CalendarFragment` class with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

    <span data-ttu-id="8a2f5-123">Обратите внимание, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-123">Notice what this code does.</span></span> <span data-ttu-id="8a2f5-124">Сначала он `acquireTokenSilently` вызывается для получения маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-124">First, it calls `acquireTokenSilently` to get the access token.</span></span> <span data-ttu-id="8a2f5-125">Вызывать этот метод каждый раз, когда требуется маркер доступа, является оптимальным методом, так как он зовется преимуществами возможностей кэшинга и обновления маркеров MSAL.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-125">Calling this method every time an access token is needed is a best practice because it takes advantage of MSAL's caching and token refresh abilities.</span></span> <span data-ttu-id="8a2f5-126">Внутри msAL проверяет наличие кэшного маркера, а затем проверяет, истек ли срок его действия.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-126">Internally, MSAL checks for a cached token, then checks if it is expired.</span></span> <span data-ttu-id="8a2f5-127">Если маркер присутствует, а срок действия не истек, он просто возвращает кэшный маркер.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-127">If the token is present and not expired, it just returns the cached token.</span></span> <span data-ttu-id="8a2f5-128">Если срок действия истек, он пытается обновить маркер перед его возвратом.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-128">If it is expired, it attempts to refresh the token before returning it.</span></span>

    <span data-ttu-id="8a2f5-129">После получения маркера код вызывает метод для `getCalendarView` получения событий пользователя.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-129">Once the token is retrieved, the code then calls the `getCalendarView` method to get the user's events.</span></span>

1. <span data-ttu-id="8a2f5-130">Запустите приложение, во войти  и коснитесь элемента навигации "Календарь" в меню.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-130">Run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="8a2f5-131">Вы должны увидеть дамп JSON событий в журнале отлаживки в Android Studio.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-131">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="8a2f5-132">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="8a2f5-132">Display the results</span></span>

<span data-ttu-id="8a2f5-133">Теперь вы можете заменить дамп JSON на что-то, чтобы отобразить результаты в удобном для пользователя виде.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-133">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="8a2f5-134">В этом разделе вы добавим a к фрагменту календаря, создайте макет для каждого элемента в элементе и создайте настраиваемый адаптер списка для того, чтобы сопостереть поля каждого из них с соответствующими в `ListView` `ListView` `ListView` `Event` `TextView` представлении.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-134">In this section, you will add a `ListView` to the calendar fragment, create a layout for each item in the `ListView`, and create a custom list adapter for the `ListView` that maps the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

1. <span data-ttu-id="8a2f5-135">Замените `TextView` в **app/res/layout/fragment_calendar.xml** на `ListView` .</span><span class="sxs-lookup"><span data-stu-id="8a2f5-135">Replace the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. <span data-ttu-id="8a2f5-136">Щелкните правой кнопкой мыши папку **app/res/layout** и выберите **"Новый"** и "Файл ресурсов **макета".**</span><span class="sxs-lookup"><span data-stu-id="8a2f5-136">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="8a2f5-137">Применив имя `event_list_item` файла, измените **корневой элемент** на `RelativeLayout` "ОК" и выберите **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="8a2f5-137">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and select **OK**.</span></span>

1. <span data-ttu-id="8a2f5-138">Откройте **event_list_item.xml** файла и замените его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-138">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. <span data-ttu-id="8a2f5-139">Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**</span><span class="sxs-lookup"><span data-stu-id="8a2f5-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="8a2f5-140">Назовем класс `EventListAdapter` и выберите **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="8a2f5-140">Name the class `EventListAdapter` and select **OK**.</span></span>

1. <span data-ttu-id="8a2f5-141">Откройте файл **EventListAdapter** и замените его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-141">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. <span data-ttu-id="8a2f5-142">Откройте класс **CalendarFragment** и добавьте в класс следующую функцию.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-142">Open the **CalendarFragment** class and add the following function to the class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. <span data-ttu-id="8a2f5-143">Замените временный код отладки из `success` переопределения на `addEventsToList();` .</span><span class="sxs-lookup"><span data-stu-id="8a2f5-143">Replace the temporary debugging code from the `success` override with `addEventsToList();`.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="SuccessSnippet" highlight="5":::

1. <span data-ttu-id="8a2f5-144">Запустите приложение, во войти и коснитесь элемента навигации **"Календарь".**</span><span class="sxs-lookup"><span data-stu-id="8a2f5-144">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="8a2f5-145">Должен отлиться список событий.</span><span class="sxs-lookup"><span data-stu-id="8a2f5-145">You should see the list of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/calendar-list.png)
