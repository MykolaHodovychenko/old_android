# Широковещательные сообщения и уведомления.

## Теоретическая часть

Широковещательные сообщения – механизм оповещения приложений о наступлении определенных событий.  Пользовательские приложения могут отправлять и получать сообщения от других приложений, а также могут получать сообщения от системных приложений Android. Также, широковещательные сообщения могут предназначаться другим компонентам этого же приложения (например, один из способов взаимодействия `Service` и `Activity` это обмен широковещательными сообщениями).

<p align="center" style="margin:auto">
  <img src="img/img_01.png" width="300"/>
</p>

Механизм широковещательных сообщений реализуется и поддерживается средствами операционной системы (операционная система выступает в роли «брокера сообщений»). Все сообщения отсылаются через контекст «в операционную систему», а все желающие получать сообщения должны быть зарегистрированы в операционной системе.

Основные сценарии использования механизма широковещательных сообщений:

- Реакция на системные события и на события устройства (основной сценарий использования широковещательных сообщений);
- Обмен сообщениями между приложениями (как правило, оба приложения должны быть написаны вами, так как чтобы принять сообщение, необходимо точно знать его идентификатор);
- Обмен сообщениями между компонентами одного приложения (обмен сообщениями между service и activity или между service).

Примеров использования широковещательных сообщений много – ОС рассылает сообщения при наступлении различных системных событий (количество таких событий очень велико), приложения могут рассылать сообщения, если произошло некоторое событие, которое может быть интересно другим приложениям (например, появление новых данных, загрузка файла) и так далее.
Работа механизма широковещательных сообщений основана на модели publisher-subscriber («подписчик-издатель»), поэтому нам необходимо отдельно рассмотреть вопрос отсылки и приема широковещательного сообщения.

### Отправка широковещательных сообщений

Давайте сначала разберемся, что же собой представляет само сообщение. Оберткой для широковещательного сообщения служит объект класса `Intent`.

Очень важным параметром намерения является параметр `action`. В этом параметре указывается идентификатор сообщения (некоторая строка, как правило, сформированная определенным образом). Например для системного сообщения об изменении режима «airplane mode» идентификатором является строка `android.intent.action.AIRPLANE_MODE`). Именно идентификатор сообщения служит способом получить это сообщение в дальнейшем.

С помощью методов `putExtra()` и объекта `Bundle` можно передать данные в виде каких-то значений, но часто бывает достаточно самого факта прихода сообщения, чтобы ваше приложение отреагировало. Например, вместе с сообщением об изменении режима «airplane mode», система передает переменную, которая дает нам информацию о том, был ли режим «airplane mode» включен или выключен.

Как уже было сказано, механизм широковещательных сообщений реализуется средствами операционной системы, поэтому для отсылки сообщения нам необходимо иметь контекст, чтобы воспользоваться специальным методом `sendBroadcast()`.

Таким образом, чтобы выслать широковещательное сообщение, необходимо выполнить следующие действия:

1. создать объект типа Intent;
2. с помощью метода `setAction()` указать action сообщения;
3. с помощью методов `putExtra()` добавить данные (если они нужны);
4. с помощью метода `Context.sendBroadcast()` выслать сообщение.

Широковещательные сообщения могут быть **явными** (**explicit**) и **неявными** (**implicit**):

- неявное широковещательное сообщение (implicit broadcast) - это сообщение, которое не предназначается специально для вашего приложения, поэтому она не является эксклюзивной для вашего приложения. Из-за этого поведения, неявные широковещательные сообщения не имеют целевого атрибута. Примером неявного сообщения может быть входящее SMS-сообщения;
- явное широковещательное сообщение (explicit broadcast) - это то, что предназначается специально для вашего приложения на заранее известном компоненте. Для отправки явного сообщения, в объекте `Intent` необходимо установить свойство `component`, где указать название пакета приложения, а также полное название класса приемника ШС.

Есть три способа отправки сообщений:

- метод `sendOrderedBroadcast()` обеспечивает одновременную отправку широковещательных сообщений только одному приемнику. Каждый приемник ШС может, в свою очередь, передавать данные следующему приемнику или останавливать распространение широковещательного сообщения;
- метод `sendBroadcast()` - в этом случае, сообщение получает все приемники ШС, которые подписаны на получение этого сообщения;
- метод `LocalBroadcastManager.sendBroadcast()` отправляет ШС только получателям, определенным в данном конкретном приложении.

### Приемник широковещательных сообщений

Для приема широковещательных сообщений нам необходимо создать **приемник широковещательных сообщений** (**Broadcast Receiver**). Broadcast Receiver – это класс, который создается разработчиком, и чья задача состоит в получении широковещательного сообщения и реагировании на него.

Приемник ШС является отдельным компонентом приложения, так как может быть запущен отдельно от Activity, Service и Content Provider и может являться стартовой точкой приложения. В некоторых случаях, приемник ШС может быть запущен операционной системой и сам запустить стартовое Activity.

Приемник ШС можно использовать двумя способами: объявить в манифесте, зарегистрировать с помощью контекста. Эти два способа использования приемника существенно отличаются друг от друга. Рассмотрим каждый из этих способов.

#### Объявление приемника в манифесте приложения

Приемник, объявленный в манифесте, регистрируется при установке приложения и имеет жизненный цикл, независимый от других компонентов. Такой приемник может служить точкой входа в приложение. Если такой приемник получает сообщения, ОС запускает приложение и создает объект приемника ШС. Для каждого полученного сообщения, создается свой объект приемника. Как только метод `onReceive()` отработает, объект приемника уничтожается.

Чтобы объявить приемник в манифесте, необходимо указать элемент `<receiver>`, где указать класс приемника. Внутри элемента `<receiver>` необходимо указать элемент `<intent-filter>`, где необходимо указать, на какие широковещательные сообщения подписывается приемник.

#### Регистрация приемника в методах жизненного цикла контекста

Такой вариант регистрации является основным и целесообразен, если реакция на сообщение напрямую связана с этим контекстом. Например, мы хотим как-то отобразить в Activity факт прихода сообщения или какие-то данные, которые пришли к нам вместе с сообщением. Или сообщения каким-то образом влияют на работу запущенной вами службы.

Регистрацию приемника и снятие его с регистрации следует проводить в парных методах жизненного цикла (`onCreate()` - `onDestroy()`, `onStart()` - `onStop()`, `onResume()` - `onPause()` и так далее).

## Практическая часть

Для начала рассмотрим вопрос создания приемников широковещательных сообщений. 

Создадим новый проект и в нем объявим класс приемника широковещательных сообщений. Для создания приемника необходимо создать класс-наследник стандартного класса приемника ШС, который называется `BroadcastReceiver`.

Класс `BroadcastReceiver` является абстрактным, поэтому нам необходимо реализовать метод `onReceive()`, который будет вызван, когда приемник получит широковещательное сообщение.

```kotlin
class ExampleBroadcastReceiver : BroadcastReceiver() {
    
    override fun onReceive(context: Context?, intent: Intent?) {
        TODO("Not yet implemented")
    }
}
```

Так как приемник может обрабатывать несколько сообщений, то в теле метода необходимо проверить, какое сообщение получено приемником. Это позволяет предоставить свой обработчик для каждого сообщения.

```kotlin
class ExampleBroadcastReceiver : BroadcastReceiver() {
    
    override fun onReceive(context: Context?, intent: Intent?) {
        when(intent?.action) {
            Intent.ACTION_BATTERY_OKAY -> batteryOkay()
            Intent.ACTION_BATTERY_LOW -> batteryLow()
        }
    }

    private fun batteryLow() = Unit
    private fun batteryOkay() = Unit
}
```

Давайте теперь поговорим о ключевой составляющей приемника ШС – это подписка приемника на широковещательные сообщения. Так как для ШС используется модель «издатель-подписчик», то наш «подписчик», он же приемник, должен явно указать брокеру сообщений (то бишь, нашей операционной системе), на какие события он «подписывается», то есть сообщения с каким идентификатором он принимает.

Идентификатор, как вы помните, указывается с помощью метода `setAction()`. Таким образом, если вы хотите, чтобы ваше приложение А высылало сообщения приложению Б, тогда в сообщении из приложения A должен быть указан тот же идентификатор, на который должен подписаться приемник из приложения Б.


К примеру, создадим приложение которое будет реагировать на системное событие `ACTION_BOOT_COMPLETED`, которое рассылается системой один раз после того как устройство загрузилось и готово к работе.

Для начала создадим класс приемника широковещательных сообщений.

Далее, заходим в манифест приложения, добавляем разрешение на прием сообщения типа `ACTION_BOOT_COMPLETED`, а также регистрируем приемник в теге `<application>`, при регистрации указываем, на какие события подписывается приемник.

Далее редактируем содержимое класса `MainActivity`

Запускаем приложение, после чего перезагружаем устройство

В качестве примера создадим приложение и добавим приемник для получения SMS-сообщений и их последующей обработки.

Для начала создадим класс приемника сообщений

Далее создадим в `MainActivity` поле класса - объект приемника. Регистрация будет происходить в методе `onStart()`, снятие с регистрации - в методе `onStop()`.

Обратите внимание, что для регистрации приемника мы должны предусмотреть объект IntentFilter. Он необходим для указания параметра action тех сообщений, которые мы хотим получать.

### Приемник ШС в манифесте

```kotlin
class MainActivity : AppCompatActivity() {

    private val requestPermissionLauncher = registerForActivityResult(ActivityResultContracts.RequestPermission()) {
        Log.d(MainActivity::class.simpleName, "Permission result: $it")
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

    }

    override fun onResume() {
        super.onResume()
        requestPermissionLauncher.launch(Manifest.permission.RECEIVE_SMS)
    }
}
```

```kotlin
class BootReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {

        if (intent?.action == Telephony.Sms.Intents.SMS_RECEIVED_ACTION) {
            for (msg in Telephony.Sms.Intents.getMessagesFromIntent(intent)) {
                val str = "From ${msg.originatingAddress} : ${msg.messageBody}"
                Toast.makeText(context, str, Toast.LENGTH_LONG).show()
            }
        }
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="ua.opsu.broadcast_example">

    <uses-permission android:name="android.permission.READ_SMS"/>
    <uses-permission android:name="android.permission.RECEIVE_SMS"/>

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Broadcast_example">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <receiver
            android:name=".BootReceiver"
            android:enabled="true"
            android:directBootAware="false"
            android:permission="android.permission.RECEIVE_BOOT_COMPLETED"
            android:exported="true">
            <intent-filter>
                <action android:name="android.provider.Telephony.SMS_RECEIVED"/>
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

### Приемник ШС в контексте

```kotlin
class MainActivity : AppCompatActivity() {

    private val requestPermissionLauncher =
        registerForActivityResult(ActivityResultContracts.RequestPermission()) {
            Log.d(MainActivity::class.simpleName, "Permission result: $it")
        }

    private val receiver: BroadcastReceiver = SmsReceiver()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }

    override fun onStart() {
        super.onStart()

        val filter = IntentFilter(Telephony.Sms.Intents.SMS_RECEIVED_ACTION)
        registerReceiver(receiver, filter)
        // LocalBroadcastManager.getInstance().registerReceiver(receiver, filter)
    }

    override fun onStop() {
        super.onStop()
        unregisterReceiver(receiver)
        // LocalBroadcastManager.getInstance().unregisterReceiver(receiver)
    }

    override fun onResume() {
        super.onResume()
        requestPermissionLauncher.launch(Manifest.permission.RECEIVE_SMS)
    }
}
```

### Отправка явного широковещательного сообщения

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val btn = findViewById<Button>(R.id.button)
        btn.setOnClickListener {
            val i = Intent().apply {
                this.action = "MY_CUSTOM_ACTION"
                this.component = ComponentName("ua.opsu.broadcast_example", "ua.opsu.broadcast_example.ExampleReceiver")
                sendBroadcast(this)
            }
        }
    }
}
```

Второе приложение

```kotlin
class ExampleReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        if (intent?.action == "MY_CUSTOM_ACTION") {
            Toast.makeText(context,"GOT IT!", Toast.LENGTH_LONG).show()
        }
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="ua.opsu.broadcast_example">

    <uses-permission android:name="android.permission.READ_SMS"/>
    <uses-permission android:name="android.permission.RECEIVE_SMS"/>

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Broadcast_example">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <receiver
            android:name=".ExampleReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="MY_CUSTOM_ACTION"/>
            </intent-filter>
        </receiver>
    </application>
</manifest>
```


### Отправка неявного широковещательного сообщения

```kotlin
class App : Application() {

    private val receiver = ExampleReceiver()

    override fun onCreate() {
        super.onCreate()

        val filter = IntentFilter("MY_CUSTOM_ACTION")
        registerReceiver(receiver, filter)
    }

    override fun onTerminate() {
        super.onTerminate()

        unregisterReceiver(receiver)
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="ua.opsu.broadcast_example">

    <uses-permission android:name="android.permission.READ_SMS"/>
    <uses-permission android:name="android.permission.RECEIVE_SMS"/>

    <application
        android:allowBackup="true"
        android:name=".App"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Broadcast_example">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val btn = findViewById<Button>(R.id.button)
        btn.setOnClickListener {
            Intent().apply {
                this.action = "MY_CUSTOM_ACTION"
                sendBroadcast(this)
            }
        }
    }
}
```

## Уведомления (Notifications)

Пример простого уведомления

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val name = "IMPORTANT"
            val descText = "Important notifications"
            val importance = NotificationManager.IMPORTANCE_HIGH
            val mChannel = NotificationChannel("1", descText, importance)
            mChannel.description = descText

            val notificationManager = getSystemService(NOTIFICATION_SERVICE) as NotificationManager
            notificationManager.createNotificationChannel(mChannel)
        }

        val btn = findViewById<Button>(R.id.button)
        btn.setOnClickListener {

            val builder = NotificationCompat.Builder(this, "1")
                .setSmallIcon(R.drawable.ic_notification_icon)
                .setContentTitle("My notification")
                .setContentText("Lorem ipsum dolor sit amet")
                .setStyle(NotificationCompat.BigTextStyle().bigText(getString(R.string.lipsum)))
                .setPriority(NotificationCompat.PRIORITY_DEFAULT)

            NotificationManagerCompat.from(this).apply {
                this.notify(Random.nextInt(),builder.build())
            }
        }
    }
}
```

Уведомление с действием по тапу

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val name = "IMPORTANT"
            val descText = "Important notifications"
            val importance = NotificationManager.IMPORTANCE_HIGH
            val mChannel = NotificationChannel("1", descText, importance)
            mChannel.description = descText

            val notificationManager = getSystemService(NOTIFICATION_SERVICE) as NotificationManager
            notificationManager.createNotificationChannel(mChannel)
        }

        val btn = findViewById<Button>(R.id.button)
        btn.setOnClickListener {

            val intent = Intent(this,SecondActivity::class.java)

            val pendingIntent = PendingIntent.getActivity(this,0,intent,PendingIntent.FLAG_IMMUTABLE)

            val builder = NotificationCompat.Builder(this, "1")
                .setSmallIcon(R.drawable.ic_notification_icon)
                .setContentTitle("My notification")
                .setContentText("Lorem ipsum dolor sit amet")
                .setStyle(NotificationCompat.BigTextStyle().bigText(getString(R.string.lipsum)))
                .setPriority(NotificationCompat.PRIORITY_DEFAULT)
                .setContentIntent(pendingIntent)
                .setAutoCancel(true)

            NotificationManagerCompat.from(this).apply {
                this.notify(Random.nextInt(),builder.build())
            }
        }
    }
}
```

Notification с действиями

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val name = "IMPORTANT"
            val descText = "Important notifications"
            val importance = NotificationManager.IMPORTANCE_HIGH
            val mChannel = NotificationChannel("1", descText, importance)
            mChannel.description = descText

            val notificationManager = getSystemService(NOTIFICATION_SERVICE) as NotificationManager
            notificationManager.createNotificationChannel(mChannel)
        }

        val btn = findViewById<Button>(R.id.button)
        btn.setOnClickListener {

            val intent = Intent(this, SecondActivity::class.java)
            val intent2 = Intent(this, ThirdActivity::class.java)

            val pendingIntent_second =
                PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_IMMUTABLE)
            val pendingIntent_third =
                PendingIntent.getActivity(this, 0, intent2, PendingIntent.FLAG_IMMUTABLE)

            val builder = NotificationCompat.Builder(this, "1")
                .setSmallIcon(R.drawable.ic_notification_icon)
                .setContentTitle("My notification")
                .setContentText("Lorem ipsum dolor sit amet")
                .setStyle(NotificationCompat.BigTextStyle().bigText(getString(R.string.lipsum)))
                .setPriority(NotificationCompat.PRIORITY_DEFAULT)
                .addAction(R.drawable.ic_notification_icon,"SECOND",pendingIntent_second)
                .addAction(R.drawable.ic_notification_icon,"THIRD",pendingIntent_third)
                .setAutoCancel(true)

            NotificationManagerCompat.from(this).apply {
                this.notify(Random.nextInt(), builder.build())
            }
        }
    }
}
```

Notification с картинками

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val name = "IMPORTANT"
            val descText = "Important notifications"
            val importance = NotificationManager.IMPORTANCE_HIGH
            val mChannel = NotificationChannel("1", descText, importance)
            mChannel.description = descText

            val notificationManager = getSystemService(NOTIFICATION_SERVICE) as NotificationManager
            notificationManager.createNotificationChannel(mChannel)
        }

        val btn = findViewById<Button>(R.id.button)
        btn.setOnClickListener {

            val bitmap = BitmapFactory.decodeResource(resources, R.drawable.nature)

            val builder = NotificationCompat.Builder(this, "1")
                .setSmallIcon(R.drawable.ic_notification_icon)
                .setContentTitle("My notification")
                .setLargeIcon(bitmap)
                .setContentText("Lorem ipsum dolor sit amet")
                .setStyle(NotificationCompat.BigPictureStyle().bigPicture(bitmap).bigLargeIcon(null))

            NotificationManagerCompat.from(this).apply {
                this.notify(Random.nextInt(), builder.build())
            }
        }
    }
}
```

Notification с цепочкой сообщений

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val name = "IMPORTANT"
            val descText = "Important notifications"
            val importance = NotificationManager.IMPORTANCE_HIGH
            val mChannel = NotificationChannel("1", descText, importance)
            mChannel.description = descText

            val notificationManager = getSystemService(NOTIFICATION_SERVICE) as NotificationManager
            notificationManager.createNotificationChannel(mChannel)
        }

        val btn = findViewById<Button>(R.id.button)
        btn.setOnClickListener {

            var p1 = Person.Builder()
                .setName("Nick")
                .setIcon(IconCompat.createWithResource(this, R.drawable.img_1)).build()

            var p2 = Person.Builder()
                .setName("Sarah")
                .setIcon(IconCompat.createWithResource(this, R.drawable.img_2)).build()

            val msg1 = NotificationCompat.MessagingStyle.Message(
                "Hi!", System.currentTimeMillis() - 100000L, p1)

            val msg2 = NotificationCompat.MessagingStyle.Message(
                "Hi! How are you?", System.currentTimeMillis() - 1000L, p2)


            val builder = NotificationCompat.Builder(this, "1")
                .setSmallIcon(R.drawable.ic_notification_icon)
                .setContentTitle("My notification")
                .setContentText("Lorem ipsum dolor sit amet")
                .setStyle(NotificationCompat.MessagingStyle(p2)
                    .addMessage(msg1)
                    .addMessage(msg2)
                )

            NotificationManagerCompat.from(this).apply {
                this.notify(Random.nextInt(), builder.build())
            }
        }
    }
}
```

## Дополнительные материалы

1. Дополнительная информация по поводу широковещательных сообщений - [ссылка](https://developer.android.com/guide/components/broadcasts).

2. Дополнительная информация по поводу модели "подписчик-издатель" - [ссылка 1](https://ru.wikipedia.org/wiki/Издатель-подписчик_(шаблон_проектирования)) и [ссылка 2](https://habr.com/ru/post/270339/).

3. Список явных широковещательных сообщений, которые можно получать с помощью приемника, зарегистрированного в манифесте приложения - [ссылка](https://developer.android.com/guide/components/broadcast-exceptions).
