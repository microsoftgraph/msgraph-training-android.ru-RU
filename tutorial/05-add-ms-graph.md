<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы включаете Microsoft Graph в приложение. Для этого приложения вы будете использовать [microsoft Graph SDK для Java](https://github.com/microsoftgraph/msgraph-sdk-java) для вызова Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Получить события календаря из Outlook

В этом разделе мы расширим класс, чтобы добавить функцию для получения событий пользователя за текущую неделю, и обновим, чтобы `GraphHelper` `CalendarFragment` использовать эти новые функции.

1. Откройте **GraphHelper** и добавьте следующие утверждения в `import` верхнюю часть файла.

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

1. Добавьте в класс следующие `GraphHelper` функции.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > Подумайте, что делает `getCalendarView` код.
    >
    > - Будет вызван URL-адрес `/v1.0/me/calendarview` .
    >   - Параметры `startDateTime` `endDateTime` и параметры запроса определяют начало и конец представления календаря.
    >   - Заглавная запись заставляет Microsoft Graph возвращать время начала и окончания каждого события в `Prefer: outlook.timezone` часовом поясе пользователя.
    >   - Функция `select` ограничивает поля, возвращаемые для каждого события, только теми, которые будут фактически использовать представление.
    >   - Функция `orderby` сортировать результаты по времени начала.
    >   - Функция `top` запрашивает 25 результатов на страницу.
    > - Определена возможность вызова ( ) для проверки на наличии дополнительных результатов и запроса `pagingCallback` дополнительных страниц при необходимости.

1. Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".** Назовем класс `GraphToIana` и выберите **"ОК".**

1. Откройте новый файл и замените его содержимое следующим:

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. Добавьте следующие утверждения в верхнюю часть файла `import` **CalendarFragment.**

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

1. Добавьте в класс следующий `CalendarFragment` член.

    ```java
    private List<Event> mEventList = null;
    ```

1. Добавьте в класс следующие `CalendarFragment` функции, чтобы скрыть и показать ход выполнения.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. Добавьте следующую функцию для предоставления функции в качестве `getCalendarView` функции. `GraphHelper`

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

1. Замените `onCreateView` существующую функцию `CalendarFragment` в классе на следующую.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

    Обратите внимание, что делает этот код. Сначала он `acquireTokenSilently` вызывается для получения маркера доступа. Вызывать этот метод каждый раз, когда требуется маркер доступа, является оптимальным методом, так как он зовется преимуществами возможностей кэшинга и обновления маркеров MSAL. Внутри msAL проверяет наличие кэшного маркера, а затем проверяет, истек ли срок его действия. Если маркер присутствует, а срок действия не истек, он просто возвращает кэшный маркер. Если срок действия истек, он пытается обновить маркер перед его возвратом.

    После получения маркера код вызывает метод для `getCalendarView` получения событий пользователя.

1. Запустите приложение, во войти  и коснитесь элемента навигации "Календарь" в меню. Вы должны увидеть дамп JSON событий в журнале отлаживки в Android Studio.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете заменить дамп JSON на что-то, чтобы отобразить результаты в удобном для пользователя виде. В этом разделе вы добавим a к фрагменту календаря, создайте макет для каждого элемента в элементе и создайте настраиваемый адаптер списка для того, чтобы сопостереть поля каждого из них с соответствующими в `ListView` `ListView` `ListView` `Event` `TextView` представлении.

1. Замените `TextView` в **app/res/layout/fragment_calendar.xml** на `ListView` .

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. Щелкните правой кнопкой мыши папку **app/res/layout** и выберите **"Новый"** и "Файл ресурсов **макета".**

1. Применив имя `event_list_item` файла, измените **корневой элемент** на `RelativeLayout` "ОК" и выберите **"ОК".**

1. Откройте **event_list_item.xml** файла и замените его содержимое на следующее.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**

1. Назовем класс `EventListAdapter` и выберите **"ОК".**

1. Откройте файл **EventListAdapter** и замените его содержимое на следующее.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. Откройте класс **CalendarFragment** и добавьте в класс следующую функцию.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. Замените временный код отладки из `success` переопределения на `addEventsToList();` .

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="SuccessSnippet" highlight="5":::

1. Запустите приложение, во войти и коснитесь элемента навигации **"Календарь".** Должен отлиться список событий.

    ![Снимок экрана с таблицей событий](./images/calendar-list.png)
