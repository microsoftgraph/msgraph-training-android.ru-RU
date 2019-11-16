# <a name="completed-module-add-azure-ad-authentication"></a>Завершенный модуль: Добавление проверки подлинности Azure AD

Версия проекта в этом каталоге соответствует завершению работы с руководством [Добавление проверки подлинности Azure AD](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=3). Если вы используете эту версию проекта, вам необходимо выполнить остальные руководства, начиная с [получения данных календаря](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=4).

> **Примечание:** Предполагается, что вы уже зарегистрировали приложение на портале Azure, как указано в разделе [Регистрация приложения на портале](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=2). Эту версию примера необходимо настроить следующим образом:
>
> 1. Переименуйте `./GraphTutorial/msal_config.json.example` файл в `msal_config.json`.
> 1. Переместите `msal_config.json` файл в `./GraphTutorial/app/src/main/res/raw` каталог.
> 1. Измените `msal_config.json` файл и внесите следующие изменения.
>     1. Замените `YOUR_APP_ID_HERE` **идентификатором приложения** , полученным с портала Azure.
