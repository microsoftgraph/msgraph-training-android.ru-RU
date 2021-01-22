<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="64c62-101">В этом разделе вы добавим возможность создания событий в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="64c62-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="64c62-102">Откройте **GraphHelper** и добавьте следующие утверждения в `import` верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="64c62-102">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.models.extensions.Attendee;
    import com.microsoft.graph.models.extensions.DateTimeTimeZone;
    import com.microsoft.graph.models.extensions.EmailAddress;
    import com.microsoft.graph.models.extensions.ItemBody;
    import com.microsoft.graph.models.generated.AttendeeType;
    import com.microsoft.graph.models.generated.BodyType;
    ```

1. <span data-ttu-id="64c62-103">Добавьте в класс следующую `GraphHelper` функцию, чтобы создать новое событие.</span><span class="sxs-lookup"><span data-stu-id="64c62-103">Add the following function to the `GraphHelper` class to create a new event.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="CreateEventSnippet":::

## <a name="update-new-event-fragment"></a><span data-ttu-id="64c62-104">Обновление нового фрагмента события</span><span class="sxs-lookup"><span data-stu-id="64c62-104">Update new event fragment</span></span>

1. <span data-ttu-id="64c62-105">Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**</span><span class="sxs-lookup"><span data-stu-id="64c62-105">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="64c62-106">Назовем класс `EditTextDateTimePicker` и выберите **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="64c62-106">Name the class `EditTextDateTimePicker` and select **OK**.</span></span>

1. <span data-ttu-id="64c62-107">Откройте новый файл и замените его содержимое следующим:</span><span class="sxs-lookup"><span data-stu-id="64c62-107">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EditTextDateTimePicker.java" id="DateTimePickerSnippet":::

    <span data-ttu-id="64c62-108">Этот класс обтекает этот класс, показывает выбор даты и времени, когда пользователь нажми его, и обновляет значение с помощью даты `EditText` и времени.</span><span class="sxs-lookup"><span data-stu-id="64c62-108">This class wraps an `EditText` control, showing a date and time picker when the user taps it, and updating the value with the date and time picked.</span></span>

1. <span data-ttu-id="64c62-109">Откройте **приложение,res/layout/fragment_new_event.xml** замените его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="64c62-109">Open **app/res/layout/fragment_new_event.xml** and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_new_event.xml":::

1. <span data-ttu-id="64c62-110">Откройте **NewEventFragment** и добавьте следующие утверждения в `import` верхней части файла.</span><span class="sxs-lookup"><span data-stu-id="64c62-110">Open **NewEventFragment** and add the following `import` statements at the top of the file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.Button;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.google.android.material.textfield.TextInputLayout;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.Event;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalException;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    ```

1. <span data-ttu-id="64c62-111">Добавьте в класс следующие `NewEventFragment` члены.</span><span class="sxs-lookup"><span data-stu-id="64c62-111">Add the following members to the `NewEventFragment` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="InputsSnippet":::

1. <span data-ttu-id="64c62-112">Добавьте следующие функции, чтобы показать и скрыть ход выполнения.</span><span class="sxs-lookup"><span data-stu-id="64c62-112">Add the following functions to show and hide a progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="64c62-113">Добавьте следующие функции, чтобы получить значения из элементов управления вводом и вызвать `GraphHelper.createEvent` функцию.</span><span class="sxs-lookup"><span data-stu-id="64c62-113">Add the following functions to get the values from the input controls and call the `GraphHelper.createEvent` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="CreateEventSnippet":::

1. <span data-ttu-id="64c62-114">Замените `onCreateView` существующее на следующее.</span><span class="sxs-lookup"><span data-stu-id="64c62-114">Replace the existing `onCreateView` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="OnCreateViewSnippet":::

1. <span data-ttu-id="64c62-115">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="64c62-115">Save your changes and restart the app.</span></span> <span data-ttu-id="64c62-116">Выберите пункт **меню "Создать событие",** заполните форму и выберите **"СОЗДАТЬ".**</span><span class="sxs-lookup"><span data-stu-id="64c62-116">Select the **New Event** menu item, fill in the form, and select **CREATE**.</span></span>

    ![Снимок экрана: форма создания события в приложении](images/create-event.png)
