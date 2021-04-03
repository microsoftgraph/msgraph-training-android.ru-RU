<!-- markdownlint-disable MD002 MD041 -->

В этом разделе вы добавим возможность создания событий в календаре пользователя.

1. Откройте **GraphHelper** и добавьте следующие `import` утверждения в верхнюю часть файла.

    ```java
    import com.microsoft.graph.models.Attendee;
    import com.microsoft.graph.models.DateTimeTimeZone;
    import com.microsoft.graph.models.EmailAddress;
    import com.microsoft.graph.models.ItemBody;
    import com.microsoft.graph.models.AttendeeType;
    import com.microsoft.graph.models.BodyType;
    ```

1. Добавьте следующую функцию `GraphHelper` в класс, чтобы создать новое событие.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="CreateEventSnippet":::

## <a name="update-new-event-fragment"></a>Обновление нового фрагмента события

1. Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**. Назови класс `EditTextDateTimePicker` и выберите **ОК.**

1. Откройте новый файл и замените его содержимое следующим.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EditTextDateTimePicker.java" id="DateTimePickerSnippet":::

    Этот класс обертывания управления, показывая дату и время выборщика, когда пользователь нажмет его, и обновление значения с даты `EditText` и времени выбрали.

1. Откройте **приложение/res/layout/fragment_new_event.xml** и замените его содержимое следующим.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_new_event.xml":::

1. Откройте **NewEventFragment** и добавьте следующие утверждения `import` в верхней части файла.

    ```java
    import android.util.Log;
    import android.widget.Button;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.google.android.material.textfield.TextInputLayout;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    ```

1. Добавьте в класс следующих `NewEventFragment` участников.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="InputsSnippet":::

1. Добавьте следующие функции, чтобы показать и скрыть планку прогресса.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="ProgressBarSnippet":::

1. Добавьте следующие функции, чтобы получить значения из элементов управления входом и вызвать `GraphHelper.createEvent` функцию.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="CreateEventSnippet":::

1. Замените `onCreateView` существующее следующим.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="OnCreateViewSnippet":::

1. Сохраните изменения и перезапустите приложение. Выберите элемент **меню New Event,** заполните форму и выберите **CREATE**.

    ![Снимок экрана формы создания события в приложении](images/create-event.png)
