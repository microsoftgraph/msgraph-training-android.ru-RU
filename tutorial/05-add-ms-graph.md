<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете включать Microsoft Graph в приложение. Для этого приложения для звонков в Microsoft Graph используется [SDK Microsoft Graph для Java.](https://github.com/microsoftgraph/msgraph-sdk-java)

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

В этом разделе вы расширит класс, чтобы добавить функцию для получения событий пользователя на текущей неделе и обновить для `GraphHelper` `CalendarFragment` использования этих новых функций.

1. Откройте **GraphHelper** и добавьте следующие `import` утверждения в верхнюю часть файла.

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

1. Добавьте в класс следующие `GraphHelper` функции.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > Рассмотрим, что делает `getCalendarView` код.
    >
    > - Вызывается URL-адрес `/v1.0/me/calendarview`.
    >   - Параметры `startDateTime` `endDateTime` и параметры запроса определяют начало и конец представления календаря.
    >   - в загонах microsoft Graph возвращается время начала и окончания каждого события в `Prefer: outlook.timezone` часовом поясе пользователя.
    >   - Функция `select` ограничивает выходные данные, чтобы для каждого события возвращались только те поля, которые действительно используются в представлении.
    >   - Функция `orderby` сортировать результаты по времени начала.
    >   - Функция `top` запрашивает 25 результатов на страницу.
    > - Функция проверяет наличие дополнительных результатов и запрашивает `processPage` дополнительные страницы при необходимости.

1. Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**. Назови класс `GraphToIana` и выберите **ОК.**

1. Откройте новый файл и замените его содержимое следующим.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. Добавьте следующие `import` утверждения в верхнюю часть файла **CalendarFragment.**

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

1. Добавьте в класс следующий `CalendarFragment` член.

    ```java
    private List<Event> mEventList = null;
    ```

1. Добавьте в класс следующие `CalendarFragment` функции, чтобы скрыть и показать планку прогресса.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. Добавьте следующую функцию для вывода списка событий для отладки.

    ```java
    private void addEventsToList() {
        // Temporary for debugging
        String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
        Log.d("GRAPH", jsonEvents);
    }
    ```

1. Замените `onCreateView` существующую функцию `CalendarFragment` в классе следующей.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

1. Запустите приложение, вопишите и нажмите элемент **навигации Calendar** в меню. Вы должны увидеть свалку JSON событий в журнале отлаживайки в Android Studio.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете заменить свалку JSON чем-то, чтобы отобразить результаты в удобной для пользователя манере. В этом разделе вы добавим фрагмент календаря, создайте макет для каждого элемента в этом разделе и создайте настраиваемый адаптер списка для того, чтобы поля каждого из них были соответствующими в `ListView` `ListView` `ListView` `Event` `TextView` представлении.

1. Замените `TextView` в **приложении/res/layout/fragment_calendar.xml** на `ListView` .

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. Щелкните правой кнопкой **мыши папку app/res/layout** и выберите **файл ресурса "Новый"** и **"Макет".**

1. Назови `event_list_item` файл, измените **элемент Root на** и выберите `RelativeLayout` **ОК.**

1. Откройте файл **event_list_item.xml** и замените его содержимое следующим.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**.

1. Назови класс `EventListAdapter` и выберите **ОК.**

1. Откройте файл **EventListAdapter** и замените его содержимое следующим.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. Откройте класс **CalendarFragment** и замените существующую `addEventsToList` функцию на следующую.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. Запустите приложение, вопишите и нажмите элемент **навигации Календарь.** Список событий.

    ![Снимок экрана с таблицей событий](./images/calendar-list.png)
