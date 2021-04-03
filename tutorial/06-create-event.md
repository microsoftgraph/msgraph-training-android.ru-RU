<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="21108-101">В этом разделе вы добавим возможность создания событий в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="21108-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="21108-102">Откройте **GraphHelper** и добавьте следующие `import` утверждения в верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="21108-102">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.models.Attendee;
    import com.microsoft.graph.models.DateTimeTimeZone;
    import com.microsoft.graph.models.EmailAddress;
    import com.microsoft.graph.models.ItemBody;
    import com.microsoft.graph.models.AttendeeType;
    import com.microsoft.graph.models.BodyType;
    ```

1. <span data-ttu-id="21108-103">Добавьте следующую функцию `GraphHelper` в класс, чтобы создать новое событие.</span><span class="sxs-lookup"><span data-stu-id="21108-103">Add the following function to the `GraphHelper` class to create a new event.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="CreateEventSnippet":::

## <a name="update-new-event-fragment"></a><span data-ttu-id="21108-104">Обновление нового фрагмента события</span><span class="sxs-lookup"><span data-stu-id="21108-104">Update new event fragment</span></span>

1. <span data-ttu-id="21108-105">Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**.</span><span class="sxs-lookup"><span data-stu-id="21108-105">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="21108-106">Назови класс `EditTextDateTimePicker` и выберите **ОК.**</span><span class="sxs-lookup"><span data-stu-id="21108-106">Name the class `EditTextDateTimePicker` and select **OK**.</span></span>

1. <span data-ttu-id="21108-107">Откройте новый файл и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="21108-107">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EditTextDateTimePicker.java" id="DateTimePickerSnippet":::

    <span data-ttu-id="21108-108">Этот класс обертывания управления, показывая дату и время выборщика, когда пользователь нажмет его, и обновление значения с даты `EditText` и времени выбрали.</span><span class="sxs-lookup"><span data-stu-id="21108-108">This class wraps an `EditText` control, showing a date and time picker when the user taps it, and updating the value with the date and time picked.</span></span>

1. <span data-ttu-id="21108-109">Откройте **приложение/res/layout/fragment_new_event.xml** и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="21108-109">Open **app/res/layout/fragment_new_event.xml** and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_new_event.xml":::

1. <span data-ttu-id="21108-110">Откройте **NewEventFragment** и добавьте следующие утверждения `import` в верхней части файла.</span><span class="sxs-lookup"><span data-stu-id="21108-110">Open **NewEventFragment** and add the following `import` statements at the top of the file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.Button;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.google.android.material.textfield.TextInputLayout;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    ```

1. <span data-ttu-id="21108-111">Добавьте в класс следующих `NewEventFragment` участников.</span><span class="sxs-lookup"><span data-stu-id="21108-111">Add the following members to the `NewEventFragment` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="InputsSnippet":::

1. <span data-ttu-id="21108-112">Добавьте следующие функции, чтобы показать и скрыть планку прогресса.</span><span class="sxs-lookup"><span data-stu-id="21108-112">Add the following functions to show and hide a progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="21108-113">Добавьте следующие функции, чтобы получить значения из элементов управления входом и вызвать `GraphHelper.createEvent` функцию.</span><span class="sxs-lookup"><span data-stu-id="21108-113">Add the following functions to get the values from the input controls and call the `GraphHelper.createEvent` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="CreateEventSnippet":::

1. <span data-ttu-id="21108-114">Замените `onCreateView` существующее следующим.</span><span class="sxs-lookup"><span data-stu-id="21108-114">Replace the existing `onCreateView` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="OnCreateViewSnippet":::

1. <span data-ttu-id="21108-115">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="21108-115">Save your changes and restart the app.</span></span> <span data-ttu-id="21108-116">Выберите элемент **меню New Event,** заполните форму и выберите **CREATE**.</span><span class="sxs-lookup"><span data-stu-id="21108-116">Select the **New Event** menu item, fill in the form, and select **CREATE**.</span></span>

    ![Снимок экрана формы создания события в приложении](images/create-event.png)
