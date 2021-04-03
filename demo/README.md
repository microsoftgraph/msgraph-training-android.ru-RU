# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="34245-101">Запуск завершенного проекта</span><span class="sxs-lookup"><span data-stu-id="34245-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34245-102">Необходимые условия</span><span class="sxs-lookup"><span data-stu-id="34245-102">Prerequisites</span></span>

<span data-ttu-id="34245-103">Чтобы запустить завершенный проект в этой папке, необходимо следующее:</span><span class="sxs-lookup"><span data-stu-id="34245-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="34245-104">[Android Studio](https://developer.android.com/studio/) установлена на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="34245-104">[Android Studio](https://developer.android.com/studio/) installed on your development machine.</span></span> <span data-ttu-id="34245-105">(**Примечание.** Этот учебник был написан с Android Studio версии 4.1.3 и Android 10.0 SDK.</span><span class="sxs-lookup"><span data-stu-id="34245-105">(**Note:** This tutorial was written with Android Studio version 4.1.3 and the Android 10.0 SDK.</span></span> <span data-ttu-id="34245-106">Действия в этом руководстве могут работать с другими версиями, но они не были проверены.)</span><span class="sxs-lookup"><span data-stu-id="34245-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="34245-107">Либо личная учетная запись Майкрософт с почтовым ящиком в Outlook.com, либо учетная запись Microsoft work или school.</span><span class="sxs-lookup"><span data-stu-id="34245-107">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="34245-108">Если у вас нет учетной записи Майкрософт, существует несколько вариантов получения бесплатной учетной записи:</span><span class="sxs-lookup"><span data-stu-id="34245-108">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="34245-109">Вы можете [зарегистрироваться для новой личной учетной записи Майкрософт.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="34245-109">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="34245-110">Вы можете [зарегистрироваться в программе разработчиков Office 365,](https://developer.microsoft.com/office/dev-program) чтобы получить бесплатную подписку на Office 365.</span><span class="sxs-lookup"><span data-stu-id="34245-110">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-an-application-with-the-azure-portal"></a><span data-ttu-id="34245-111">Регистрация приложения на портале Azure</span><span class="sxs-lookup"><span data-stu-id="34245-111">Register an application with the Azure Portal</span></span>

1. <span data-ttu-id="34245-112">Откройте браузер и перейдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com) и войдите с помощью **личной учетной записи** (т.е. учетной записи Майкрософт) или **рабочей или учебной учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="34245-112">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="34245-113">Выберите **Azure Active Directory** на панели навигации слева, затем выберите **Регистрация приложений** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="34245-113">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="34245-114">Снимок экрана регистрации приложения</span><span class="sxs-lookup"><span data-stu-id="34245-114">A screenshot of the App registrations</span></span> ](../tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="34245-115">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="34245-115">Select **New registration**.</span></span> <span data-ttu-id="34245-116">На странице **Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="34245-116">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="34245-117">Введите **имя** `Android Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="34245-117">Set **Name** to `Android Graph Tutorial`.</span></span>
    - <span data-ttu-id="34245-118">Введите **поддерживаемые типы учетных записей** для **учетных записей в любом каталоге организаций и личных учетных записей Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="34245-118">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="34245-119">В статье Перенаправление **URI** установите выпадение в общедоступный **клиент/родной (мобильный** & рабочий стол) и задайте значение , заменив его именем `msauth://YOUR_PACKAGE_NAME/callback` пакета `YOUR_PACKAGE_NAME` проекта.</span><span class="sxs-lookup"><span data-stu-id="34245-119">Under **Redirect URI**, set the dropdown to **Public client/native (mobile & desktop)** and set the value to `msauth://YOUR_PACKAGE_NAME/callback`, replacing `YOUR_PACKAGE_NAME` with your project's package name.</span></span>

    ![Снимок экрана со страницей регистрации приложения](../tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="34245-121">Нажмите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="34245-121">Select **Register**.</span></span> <span data-ttu-id="34245-122">На странице **Учебник по графику** Android скопируйте значение ID приложения **(клиента)** и сохраните его, оно потребуется на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="34245-122">On the **Android Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Снимок экрана с идентификатором приложения для новой регистрации](../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="34245-124">Настройка примера</span><span class="sxs-lookup"><span data-stu-id="34245-124">Configure the sample</span></span>

1. <span data-ttu-id="34245-125">Переименуй файл в каталог и перемести `msal_config.json.example` `msal_config.json` его в `GraphTutorial/app/src/main/res/raw` каталог.</span><span class="sxs-lookup"><span data-stu-id="34245-125">Rename the `msal_config.json.example` file to `msal_config.json` and move the file into the `GraphTutorial/app/src/main/res/raw` directory.</span></span>
1. <span data-ttu-id="34245-126">Изменить файл `msal_config.json` и внести следующие изменения.</span><span class="sxs-lookup"><span data-stu-id="34245-126">Edit the `msal_config.json` file and make the following changes.</span></span>
    1. <span data-ttu-id="34245-127">`YOUR_APP_ID_HERE`Замените **id приложения,** который вы получили с портала Azure.</span><span class="sxs-lookup"><span data-stu-id="34245-127">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the Azure portal.</span></span>
    1. <span data-ttu-id="34245-128">Замените `com.example.graphtutorial` имя пакета.</span><span class="sxs-lookup"><span data-stu-id="34245-128">Replace `com.example.graphtutorial` with your package name.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="34245-129">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="34245-129">Run the sample</span></span>

<span data-ttu-id="34245-130">В Android Studio выберите **выполнить "приложение"** в меню **Run.**</span><span class="sxs-lookup"><span data-stu-id="34245-130">In Android Studio, select **Run 'app'** on the **Run** menu.</span></span>
