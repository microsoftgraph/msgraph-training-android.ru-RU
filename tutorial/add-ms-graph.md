<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5615b-101">В этом упражнении вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="5615b-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="5615b-102">Для этого приложения вы будете использовать [пакет SDK Microsoft Graph для Java](https://github.com/microsoftgraph/msgraph-sdk-java) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="5615b-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="5615b-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="5615b-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="5615b-104">Начните с расширения `GraphHelper` класса, чтобы добавить функцию, чтобы получить события пользователя.</span><span class="sxs-lookup"><span data-stu-id="5615b-104">Start by extending the `GraphHelper` class to add a function to get the user's events.</span></span> <span data-ttu-id="5615b-105">Откройте файл **графхелпер** и добавьте приведенные ниже `import` операторы в начало файла.</span><span class="sxs-lookup"><span data-stu-id="5615b-105">Open the **GraphHelper** file and add the following `import` statements to the top of the file.</span></span>

```java
import com.microsoft.graph.options.Option;
import com.microsoft.graph.options.QueryOption;
import com.microsoft.graph.requests.extensions.IEventCollectionPage;
import java.util.LinkedList;
import java.util.List;
```

<span data-ttu-id="5615b-106">Добавьте в `GraphHelper` класс следующие функции.</span><span class="sxs-lookup"><span data-stu-id="5615b-106">Add the following functions to the `GraphHelper` class.</span></span>

```java
public void getEvents(String accessToken, ICallback<IEventCollectionPage> callback) {
    mAccessToken = accessToken;

    // Use query options to sort by created time
    final List<Option> options = new LinkedList<Option>();
    options.add(new QueryOption("orderby", "createdDateTime DESC"));


    // GET /me/events
    mClient.me().events().buildRequest(options)
            .select("subject,organizer,start,end")
            .get(callback);

}

// Debug function to get the JSON representation of a Graph
// object
public String serializeObject(Object object) {
    return mClient.getSerializer().serializeObject(object);
}
```

<span data-ttu-id="5615b-107">РасСмотрите, какие действия `getEvents` выполняет код.</span><span class="sxs-lookup"><span data-stu-id="5615b-107">Consider what the code in `getEvents` is doing.</span></span>

- <span data-ttu-id="5615b-108">URL-адрес, который будет вызываться — это `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="5615b-108">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="5615b-109">`select` Функция ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.</span><span class="sxs-lookup"><span data-stu-id="5615b-109">The `select` function limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="5615b-110">`QueryOption` Имя `orderby` используется для сортировки результатов по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="5615b-110">The `QueryOption` named `orderby` is used to sort the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="5615b-111">Теперь обновите `CalendarFragment` их, чтобы использовать эти новые функции.</span><span class="sxs-lookup"><span data-stu-id="5615b-111">Now update `CalendarFragment` to use these new functions.</span></span> <span data-ttu-id="5615b-112">Добавьте приведенные `import` ниже операторы в начало файла **календарфрагмент** .</span><span class="sxs-lookup"><span data-stu-id="5615b-112">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

```java
import android.util.Log;
import android.widget.ListView;
import android.widget.ProgressBar;
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.models.extensions.Event;
import com.microsoft.graph.requests.extensions.IEventCollectionPage;
import com.microsoft.identity.client.AuthenticationCallback;
import com.microsoft.identity.client.AuthenticationResult;
import com.microsoft.identity.client.exception.MsalException;
import java.util.List;
```

<span data-ttu-id="5615b-113">Добавьте в `CalendarFragment` класс следующие элементы.</span><span class="sxs-lookup"><span data-stu-id="5615b-113">Add the following members to the `CalendarFragment` class.</span></span>

```java
private List<Event> mEventList = null;
private ProgressBar mProgress = null;
```

<span data-ttu-id="5615b-114">Теперь добавьте в `CalendarFragment` класс следующие функции для скрытия и отображения индикатора выполнения, а также для предоставления обратного вызова для `getEvents` функции в. `GraphHelper`</span><span class="sxs-lookup"><span data-stu-id="5615b-114">Now add the following functions to the `CalendarFragment` class to hide and show the progress bar, and to provide a callback for the `getEvents` function in `GraphHelper`.</span></span>

```java
private void showProgressBar() {
    getActivity().runOnUiThread(new Runnable() {
        @Override
        public void run() {
            mProgress.setVisibility(View.VISIBLE);
        }
    });
}

private void hideProgressBar() {
    getActivity().runOnUiThread(new Runnable() {
        @Override
        public void run() {
            mProgress.setVisibility(View.GONE);
        }
    });
}

private ICallback<IEventCollectionPage> getEventsCallback() {
    return new ICallback<IEventCollectionPage>() {
        @Override
        public void success(IEventCollectionPage iEventCollectionPage) {
            mEventList = iEventCollectionPage.getCurrentPage();

            // Temporary for debugging
            String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
            Log.d("GRAPH", jsonEvents);

            hideProgressBar();
        }

        @Override
        public void failure(ClientException ex) {
            Log.e("GRAPH", "Error getting events", ex);
            hideProgressBar();
        }
    };
}
```

<span data-ttu-id="5615b-115">Наконец, переопределите `onCreate` функцию в `GraphHelper` классе, чтобы получить события пользователя из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="5615b-115">Finally, override the `onCreate` function in the `GraphHelper` class to get the user's events from Microsoft Graph.</span></span>

```java
@Override
public void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    mProgress = getActivity().findViewById(R.id.progressbar);
    showProgressBar();

    // Get a current access token
    AuthenticationHelper.getInstance()
            .acquireTokenSilently(new AuthenticationCallback() {
                @Override
                public void onSuccess(AuthenticationResult authenticationResult) {
                    final GraphHelper graphHelper = GraphHelper.getInstance();

                    // Get the user's events
                    graphHelper.getEvents(authenticationResult.getAccessToken(),
                            getEventsCallback());
                }

                @Override
                public void onError(MsalException exception) {
                    Log.e("AUTH", "Could not get token silently", exception);
                    hideProgressBar();
                }

                @Override
                public void onCancel() {
                    hideProgressBar();
                }
            });
}
```

<span data-ttu-id="5615b-116">Обратите внимание на то, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="5615b-116">Notice what this code does.</span></span> <span data-ttu-id="5615b-117">Сначала он вызывает метод `acquireTokenSilently` Get для получения маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="5615b-117">First, it calls `acquireTokenSilently` to get the access token.</span></span> <span data-ttu-id="5615b-118">Каждый раз, когда необходим маркер доступа, рекомендуется вызывать этот метод, так как он использует преимущества кэширования MSAL и возможности обновления маркеров.</span><span class="sxs-lookup"><span data-stu-id="5615b-118">Calling this method every time an access token is needed is a best practice because it takes advantage of MSAL's caching and token refresh abilities.</span></span> <span data-ttu-id="5615b-119">MSAL проверяет наличие кэшированного маркера, а затем проверяет его срок действия.</span><span class="sxs-lookup"><span data-stu-id="5615b-119">Internally, MSAL checks for a cached token, then checks if it is expired.</span></span> <span data-ttu-id="5615b-120">Если маркер присутствует, а срок его действия не истек, он просто возвращает кэшированный маркер.</span><span class="sxs-lookup"><span data-stu-id="5615b-120">If the token is present and not expired, it just returns the cached token.</span></span> <span data-ttu-id="5615b-121">Если срок действия истек, он пытается обновить маркер перед его возвратом.</span><span class="sxs-lookup"><span data-stu-id="5615b-121">If it is expired, it attempts to refresh the token before returning it.</span></span>

<span data-ttu-id="5615b-122">После получения маркера код вызывает `getEvents` метод, чтобы получить события пользователя.</span><span class="sxs-lookup"><span data-stu-id="5615b-122">Once the token is retrieved, the code then calls the `getEvents` method to get the user's events.</span></span>

<span data-ttu-id="5615b-123">Теперь вы можете запустить приложение, войти и нажать в меню элемент Навигация \*\*\*\* по календарю.</span><span class="sxs-lookup"><span data-stu-id="5615b-123">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="5615b-124">В журнале отладки в Android Studio должен появиться дамп JSON событий.</span><span class="sxs-lookup"><span data-stu-id="5615b-124">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="5615b-125">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="5615b-125">Display the results</span></span>

<span data-ttu-id="5615b-126">Теперь вы можете заменить дамп JSON на какой-то способ отобразить результаты в удобном для пользователя виде.</span><span class="sxs-lookup"><span data-stu-id="5615b-126">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="5615b-127">Сначала замените элемент `TextView` **app/res/layout/фрагмент_календар. XML** на `ListView`.</span><span class="sxs-lookup"><span data-stu-id="5615b-127">Start by replacing the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

```xml
<ListView
    android:id="@+id/eventlist"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:divider="@color/colorPrimary"
    android:dividerHeight="1dp" />
```

<span data-ttu-id="5615b-128">Затем создайте макет для каждого элемента в файле `ListView`.</span><span class="sxs-lookup"><span data-stu-id="5615b-128">Next, create a layout for each item in the `ListView`.</span></span> <span data-ttu-id="5615b-129">Щелкните правой кнопкой мыши папку **app/res/layout** и выберите команду **создать**, а затем — **файл ресурсов макета**.</span><span class="sxs-lookup"><span data-stu-id="5615b-129">Right-click the **app/res/layout** folder and choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="5615b-130">ПриСвойте файлу `event_list_item`имя, измените **корневой элемент** на `RelativeLayout`, а затем нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="5615b-130">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and choose **OK**.</span></span> <span data-ttu-id="5615b-131">Откройте файл **евент_лист_итем. XML** и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="5615b-131">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">

    <TextView
        android:id="@+id/eventsubject"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Subject"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/eventorganizer"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/eventsubject"
        android:text="Adele Vance"
        android:textSize="15sp" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/eventorganizer"
        android:orientation="horizontal">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingEnd="2sp"
            android:text="Start:"
            android:textSize="15sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/eventstart"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="1:30 PM 2/19/2019"
            android:textSize="15sp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingStart="5sp"
            android:paddingEnd="2sp"
            android:text="End:"
            android:textSize="15sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/eventend"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="1:30 PM 2/19/2019"
            android:textSize="15sp" />
    </LinearLayout>
</RelativeLayout>
```

<span data-ttu-id="5615b-132">Теперь создайте настраиваемый адаптер списка для `ListView`.</span><span class="sxs-lookup"><span data-stu-id="5615b-132">Now create a custom list adapter for the `ListView`.</span></span> <span data-ttu-id="5615b-133">Это необходимо, чтобы создать представления для элементов списка и сопоставить их поля `Event` с соответствующими `TextView` элементами в представлении.</span><span class="sxs-lookup"><span data-stu-id="5615b-133">This is necessary to create the views for the items in the list, and to map the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

<span data-ttu-id="5615b-134">Щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**.</span><span class="sxs-lookup"><span data-stu-id="5615b-134">Right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="5615b-135">НаЗовите класс `EventListAdapter` и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="5615b-135">Name the class `EventListAdapter` and choose **OK**.</span></span> <span data-ttu-id="5615b-136">Откройте файл **евентлистадаптер** и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="5615b-136">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

```java
package com.example.graphtutorial;

import android.content.Context;
import android.support.annotation.NonNull;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.TextView;

import com.microsoft.graph.models.extensions.DateTimeTimeZone;
import com.microsoft.graph.models.extensions.Event;

import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.util.List;
import java.util.TimeZone;

public class EventListAdapter extends ArrayAdapter<Event> {
    private Context mContext;
    private int mResource;
    private ZoneId mLocalTimeZoneId;

    // Used for the ViewHolder pattern
    // https://developer.android.com/training/improving-layouts/smooth-scrolling
    static class ViewHolder {
        TextView subject;
        TextView organizer;
        TextView start;
        TextView end;
    }

    public EventListAdapter(Context context, int resource, List<Event> events) {
        super(context, resource, events);
        mContext = context;
        mResource = resource;
        mLocalTimeZoneId = TimeZone.getDefault().toZoneId();
    }

    @NonNull
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        Event event = getItem(position);

        ViewHolder holder;

        if (convertView == null) {
            LayoutInflater inflater = LayoutInflater.from(mContext);
            convertView = inflater.inflate(mResource, parent, false);

            holder = new ViewHolder();
            holder.subject = convertView.findViewById(R.id.eventsubject);
            holder.organizer = convertView.findViewById(R.id.eventorganizer);
            holder.start = convertView.findViewById(R.id.eventstart);
            holder.end = convertView.findViewById(R.id.eventend);

            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
        }

        holder.subject.setText(event.subject);
        holder.organizer.setText(event.organizer.emailAddress.name);
        holder.start.setText(getLocalDateTimeString(event.start));
        holder.end.setText(getLocalDateTimeString(event.end));

        return convertView;
    }

    // Convert Graph's DateTimeTimeZone format to
    // a LocalDateTime, then return a formatted string
    private String getLocalDateTimeString(DateTimeTimeZone dateTime) {
        ZonedDateTime localDateTime = LocalDateTime.parse(dateTime.dateTime)
                .atZone(ZoneId.of(dateTime.timeZone))
                .withZoneSameInstant(mLocalTimeZoneId);

        return String.format("%s %s",
                localDateTime.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM)),
                localDateTime.format(DateTimeFormatter.ofLocalizedTime(FormatStyle.SHORT)));
    }
}
```

<span data-ttu-id="5615b-137">Наконец, обновите `CalendarFragment` класс, чтобы он `EventListAdapter` использовался для `ListView`инициализации.</span><span class="sxs-lookup"><span data-stu-id="5615b-137">Finally, update the `CalendarFragment` class to use the `EventListAdapter` to initialize the `ListView`.</span></span> <span data-ttu-id="5615b-138">Откройте класс **календарфрагмент** и добавьте приведенную ниже функцию в класс.</span><span class="sxs-lookup"><span data-stu-id="5615b-138">Open the **CalendarFragment** class and add the following function to the class.</span></span>

```java
private void addEventsToList() {
    getActivity().runOnUiThread(new Runnable() {
        @Override
        public void run() {
            ListView eventListView = getView().findViewById(R.id.eventlist);

            EventListAdapter listAdapter = new EventListAdapter(getActivity(),
                    R.layout.event_list_item, mEventList);

            eventListView.setAdapter(listAdapter);
        }
    });
}
```

<span data-ttu-id="5615b-139">Добавьте следующую строку кода в `success` переопределение после `mEventList = iEventCollectionPage.getCurrentPage();` строки.</span><span class="sxs-lookup"><span data-stu-id="5615b-139">Add the following line of code in the `success` override after the `mEventList = iEventCollectionPage.getCurrentPage();` line.</span></span>

```java
addEventsToList();
```

<span data-ttu-id="5615b-140">Запустите приложение, войдите в систему и нажмите элемент навигации \*\*\*\* по календарю.</span><span class="sxs-lookup"><span data-stu-id="5615b-140">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="5615b-141">Вы должны увидеть список событий.</span><span class="sxs-lookup"><span data-stu-id="5615b-141">You should see the list of events.</span></span>

![Снимок экрана С таблицей событий](./images/calendar-list.png)
