# Архитектурные компоненты. Часть 1

## Понятие Architectural components

**Architecture components** - набор библиотек, который помогает создавать надежные, тестируемые и легкие в поддержке приложения. Начиная с классов для управления жизненным циклом компонентов UI и управления данными приложения.

Arhitecture components содержат в себе много новых компонентов, таких как: LiveData, ViewModel, LifecycleObserver, LifecycleOwner, а также библиотеку Room для работы с БД приложения. Вместе эти компоненты помогут разработчикам следовать хорошему архитектурному паттерну с правильным разделением ответственности между слоями, а также позволят достаточно легко и безболезненно вносить изменения, избегать утечек памяти и обновлять UI при изменении данных.

Дадим краткое описание компонент:

- LifecycleOwner — интерфейс для компонентов, у которого есть свой жизненный цикл (как у Activity и Fragment);
- LifecycleObserver — подписывается на изменения жизненного цикла LifecycleOwner’а, который самодостаточен. Он не полагается на методы onStart() и onStop() Activity или Fragment при инициализации и остановке;
- LiveData — это наблюдаемый объект для хранения данных, он уведомляет наблюдателей, когда данные изменяются. Этот компонент также является связанным с жизненным циклом LifecycleOwner (Activity или Fragment), что помогает избежать утечек памяти и других неприятностей;
- ViewModel — это объекты, которые предоставляют данные для UI компонентов. Они не имеют ссылок на view и независимы от жизненного цикла LifecycleOwner’a;
- Room - основанная на SQLite библиотека ORM. Room является абстракцией над слоем, в котором производятся конкретные действия над БД, такими как вставки, удаления, создания таблиц и так далее. Room использует аннотации для генерации кода во время компиляции. Более того, Room также поддерживает LiveData и RxJava2 Flowable.

## Шаблон MVVM с использованием Architecture Components

Архитектуру приложения с использованием Architecture Components схематически можно представить следующим образом

<p align="center" style="margin:auto;background-color:white">
  <img src="img/img_01.png" width="600" />
</p>

Опишем составляющие этого шаблона:

- View — этот слой содержит компоненты UI и отвечает за код, управляющий компонентами пользовательского интерфейса (view), такой как инициализация дочерних view, отображение progress bar’а, ввод данных пользователем, обработку анимаций и тому подобное. Такой код содержится в активити и фрагментах;
- ViewModel — объекты этого класса предоставляют данные для компонентов UI. В нашем случае View будут использовать LiveData для отслеживания изменений данных в ViewModel;
- Repository — репозитории являются абстракцией над источниками данных, которую приложение может использовать для получения данных и их кеширования. Эта абстракция полезна по двум основным причинам: 1) код не зависит от конкретной реализации хранилища данных, а также 2) в следствие предыдущей причины, мы можем легко менять конкретные реализации хранилища данных, например, для тестирования;
- Data Service — это слой, который содержит код для кеширования (например, используя SQLite), а также код для загрузки данных (например, загрузка данных с backend api при помощи Retrofit).

## Создание тестового приложения

Создадим простое приложение "My Notepad" для создания и редактирования простых заметок. Приложение состоит из двух окон:

- `MainActivity` - главное окно для вывода списка заметок, добавления новой заметки, редактирования и удаления существующей заметки;
- `NoteActivity` - окно для создания новой заметки и редактирования существующей.
